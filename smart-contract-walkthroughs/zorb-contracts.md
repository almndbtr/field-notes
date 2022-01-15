---
description: ⚠️ Work-in-progress.
---

# 🟣 Zorb Contracts

Tracking Issue: [https://github.com/almndbtr/field-notes/issues/3](https://github.com/almndbtr/field-notes/issues/3)

## Summary

From zorb.dev:

> [zorb ↗ ](https://github.com/ourzora/zorb/tree/main/packages/zorb-web-component)is a simple, open-source identity system for the decentralized Internet. It is maintained by ZORA, the decentralized marketplace protocol.

## Contracts Overview

On initial release, there are [two contracts](https://github.com/ourzora/zorb/tree/main/packages/zorb-contracts):&#x20;

* [ZorbNFT.sol](https://github.com/ourzora/zorb/blob/main/packages/zorb-contracts/contracts/ZorbNFT.sol)
* [ColorLib.sol](https://github.com/ourzora/zorb/blob/main/packages/zorb-contracts/contracts/ColorLib.sol)

Shortly after the minting closed, `@m1guelpf` and `@iannash` worked on [shipping a new set of contracts for refrigerating Zorbs](https://github.com/ourzora/zorb/tree/main/packages/zorb-fridge-contracts).

### ZorbNFT.sol

This is the "main" contract for minting a [Zorb](https://zorb.dev). There are three interesting portions worth diving into:

* the mint
* the known marketplaces feature
* how it's rendered as an on-chain SVG

#### Let's start with the mint.

When someone interacts with the contract to [mint](https://github.com/ourzora/zorb/blob/7987ddd736803f88d97cd150a89ee70426ae3134/packages/zorb-contracts/contracts/ZorbNFT.sol#L129-L134) a Zorb, this function is executed:

```solidity
    /// Simple public mint function
    function mint() public {
        require(mintIsOpen(), "Mint not open");
        _mint(msg.sender, currentTokenId.current());
        currentTokenId.increment();
    }
```

Put another way:

* If the NFT is not open for minting, do not continue with the mint. Exit with the message, "Mint not open." This is something that is viewable on Etherscan, should someone decide to interact with the contract before the "valid" time period.
* If the mint is open, call [`_mint`](https://docs.openzeppelin.com/contracts/4.x/api/token/erc721#ERC721-\_mint-address-uint256-) . This function is imported via the ERC721 module. It mints the current token ID and transfers it to `msg.sender`, which in this case is the client (whoever interacts with the contract).
* Increment the current token ID so the next client that interacts with the contract will mint the next token ID, rather than the one that was just minted.

Let's look at [`mintIsOpen()`](https://github.com/ourzora/zorb/blob/7987ddd736803f88d97cd150a89ee70426ae3134/packages/zorb-contracts/contracts/ZorbNFT.sol#L122-L127) along with [the variables](https://github.com/ourzora/zorb/blob/7987ddd736803f88d97cd150a89ee70426ae3134/packages/zorb-contracts/contracts/ZorbNFT.sol#L80-L84) it relies on:

```solidity
    /// Production mint start = 2022 EST new years
    uint256 private constant MINT_START_AT = 1641013200;

    /// Production mint duration = 20 + 22 hours
    uint256 private constant MINT_DURATION = 42 hours;

    /// [Editor's note]: scroll down...
    ///
    ///
    
    /// Informational function returning if the mint is currently ongoing
    function mintIsOpen() public view returns (bool) {
        return
            block.timestamp > MINT_START_AT &&
            block.timestamp <= MINT_START_AT + MINT_DURATION;
    }
```

* This is a validation function: it ensures that, when the `mint` function is called, the mint is actually open. The time range is from January 1, 2022, 12:00AM EST to the next 42 hours.&#x20;
* `1641013200` is a `UNIX` timestamp. You can convert `UNIX` timestamps to [Epoch](https://en.wikipedia.org/wiki/Epoch) time format using a site like [https://www.epochconvert.com/](https://www.epochconvert.com) to confirm the value.

#### Known Marketplaces

This is a feature that allows someone to "freeze" their Zorb NFT. I will provide a step-by-step walkthrough. If you want to skip this and get straight to freezing, I recommend checking out Ali's blog post: [https://blog.ali.dev/engineering/2022/01/13/freeze-your-zorb/](https://blog.ali.dev/engineering/2022/01/13/freeze-your-zorb/)

Let's begin! :nerd:

```solidity
// https://github.com/ourzora/zorb/blob/7987ddd736803f88d97cd150a89ee70426ae3134/packages/zorb-contracts/contracts/ZorbNFT.sol#L86-L87
/// Mapping that stores known marketplace contracts (escrow/auction/staking etc)
mapping(address => bool) private knownMarketplace;
```

`knownMarketplace` is a private variable. While it is not publicly accessible by clients to modify directly, the [`setKnownMarketplace`](https://github.com/ourzora/zorb/blob/7987ddd736803f88d97cd150a89ee70426ae3134/packages/zorb-contracts/contracts/ZorbNFT.sol#L110-L120)  function can update the `knownMarketplace`:

```solidity
    /// Set known marketplace contracts
    /// @param marketPlaces list of addresses
    /// @param isKnown flag if the above marketplaces are known
    function setKnownMarketplaces(address[] calldata marketPlaces, bool isKnown)
        external
        onlyOwner
    {
        for (uint256 i = 0; i < marketPlaces.length; i++) {
            knownMarketplace[marketPlaces[i]] = isKnown;
        }
    }
```

This function goes through each of the addresses (which get assigned to the `marketPlaces` variable) that the client passes in. For each address, it updates `knownMarketplace` with the value of `isKnown`. The normative case would be to set `isKnown` to `true` so that the caller (assumedly, also the owner) wants to make their address "known" in the dictionary of known marketplaces.

The [`onlyOwner`](https://docs.openzeppelin.com/contracts/2.x/api/ownership#Ownable-onlyOwner--) reference is placed there to ensure that the function throws (exits with error) if it's called by any account other than the owner.

Taking a step back, the person that calls this would want to set these addresses to something they know, trust, or have control over.&#x20;

That way, when the token is transferred, the internal [`_beforeTokenTransfer`](https://github.com/ourzora/zorb/blob/7987ddd736803f88d97cd150a89ee70426ae3134/packages/zorb-contracts/contracts/ZorbNFT.sol#L184-L196) hook is called so that the known marketplace is "recognized" in the dictionary of known marketplaces and the last owner's address is cached. This is what ensures that the Zorb is "frozen", such that it still "points" back to the last owner:

```solidity
    /// Used to implement known marketplace functionality
    /// @param from token transfer from
    /// @param to token transfer to
    /// @param tokenId token being transferred
    function _beforeTokenTransfer(
        address from,
        address to,
        uint256 tokenId
    ) internal override {
        if (knownMarketplace[to]) {
            lastOwner[tokenId] = from;
        }
    }
```

### How it's rendered as an on-chain SVG

This contract produces an [ERC-721](https://docs.openzeppelin.com/contracts/4.x/api/token/erc721) token.

The [`tokenURI` function](https://github.com/ourzora/zorb/blob/7987ddd736803f88d97cd150a89ee70426ae3134/packages/zorb-contracts/contracts/ZorbNFT.sol#L212-L238) is responsible for returning the on-chain encoded SVG for each Zorb.

Put another way: this Zorb is not stored in a third-party server owned by some company (Amazon, Google, Opensea, or other) and it's not stored on the [Interplanetary File System (IPFS)](https://ipfs.io). This Zorb is stored directly in the return value of [`tokenURI`](https://docs.openzeppelin.com/contracts/4.x/api/token/erc721#IERC721Metadata-tokenURI-uint256-)., which comes from the [ERC-721 module](https://docs.openzeppelin.com/contracts/4.x/api/token/erc721). That's so cool!&#x20;

Ok, ok, here's the code. This time, I've embedded my comments in the snippet as a double slash, `//`:&#x20;

```solidity
    /// TokenURI function returning on-chain encoded SVG for each Zorb
    /// @param tokenId token id to render
    function tokenURI(uint256 tokenId)
        public
        view
        override
        returns (string memory)
    {
        // Validation: ensure the `tokenId` exists. Throw an error, otherwise.
        require(_exists(tokenId), "No token");

        // The `idString` coerces the number to string:
        // https://github.com/ourzora/nft-editions/blob/12e1147362e2b4480625e7b8ab2a33c42a2c3ed1/contracts/IPublicSharedMetadata.sol#L19-L25
        // This function is defined on `sharedMetadata`, stemming from IPublicSharedMetadata
        // from `@zoralabs/nft-editions-contracts/contracts/IPublicSharedMetadata.sol`
        // Check out the package: https://www.npmjs.com/package/@zoralabs/nft-editions-contracts
        // And also: https://github.com/ourzora/nft-editions
        string memory idString = sharedMetadata.numberToString(tokenId);

        return
            // encodes the argument json bytes into base64-data uri format
            // https://github.com/ourzora/nft-editions/blob/12e1147362e2b4480625e7b8ab2a33c42a2c3ed1/contracts/IPublicSharedMetadata.sol#L12-L17
            sharedMetadata.encodeMetadataJSON(
                abi.encodePacked(
                    '{"name": "Zorb #',
                    idString,
                    unicode'", "description": "Zorbs were distributed for free by ZORA on New Year’s 2022. Each NFT imbues the properties of its wallet holder, and when sent to someone else, will transform.\\n\\nView this NFT at [zorb.dev/nft/',
                    idString,
                    '](https://zorb.dev/nft/',idString,


                    ')", "image": "',
                    // The Zorb art is generated by based on the render address.
                    // But, if there is a known marketplace for that address,
                    // it defaults to the last owner's address rather than the wallet it sits in!
                    zorbForAddress(getZorbRenderAddress(tokenId)),
                    '"}'
                )
            );
    }soli
```

### ColorLib.sol

This is a custom library for handling the math functions required to generate the gradient step colors. This is a port from their JavaScript library from their zorb-web-component. Check out the source here: [https://github.com/ourzora/zorb/blob/main/packages/zorb-web-component/src/lib.ts](https://github.com/ourzora/zorb/blob/main/packages/zorb-web-component/src/lib.ts)&#x20;

An exercise that I will leave for the reader is de-constructing each of the functions that are called to produce the Zorb in question. I am choosing not to allocate time to break this part of the contract down. :relieved:
