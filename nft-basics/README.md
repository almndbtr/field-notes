---
description: NFT stands for non-fungible token. Let's talk basics!
---

# 👶 NFT Basics

### Explain it like I'm five 👧

* `N` is "non". "Non" is another way of saying "not."
* `F` means "fungible." "Fungible" is a funny word. You can think of it as "tradable" or "switchable."
* `T` means "token". A "token" is like a coin.
* "Non-fungible" means that it is one-of-a-kind.
* "Non-fungible token" is like a one-of-a-kind coin.
* There are many NFTs that look alike, and even some that could _look_ the same! But what makes an NFT unique is its metadata. Metadata is just more information that helps us know why they're different, like their ID number.

### Example | zorb 🟣

Let's use [zorb.dev](https://zorb.dev) as an example. Here are three `zorbs`:

![Zorb #9144](../.gitbook/assets/zorb-9144.svg)

![Zorb #9160](../.gitbook/assets/zorb-9160.svg)

![Zorb #9206](../.gitbook/assets/zorb-9206.svg)

Every Zorb _looks_ alike. In fact, they are all the same shape and color pixel-by-pixel!

What makes them different is their metadata. Here are each of them hosted on zorb.dev:

* 🟣[`#9144`](https://zorb.dev/nft/9144)
* 🟣[`#9160`](https://zorb.dev/nft/9160)
* 🟣[`#9206`](https://zorb.dev/nft/9206)

Each Zorb has a link to view them on Etherscan.&#x20;

What they all have in common is that  they all interacted with the same contract:

* 📜[0xCa21d4228cDCc68D4e23807E5e370C07577Dd152](https://etherscan.io/address/0xCa21d4228cDCc68D4e23807E5e370C07577Dd152)

... but, drilling into each of their respective transactions, you'll see that it was minted for a specific `TokenID` value:

* [Mint Transaction of #9144](https://etherscan.io/tx/0xd29384f43ec1e7cef5588cc5bb298d70b71b3654399ddeb29dca18f6ae8a990a)
* [Mint Transaction of #9160](https://etherscan.io/tx/0xb02596210aa0f5f452c9e5e09f8d987e9d7d8ccef2f58839808c987538436e76)
* [Mint Transaction of #9206](https://etherscan.io/token/0xCa21d4228cDCc68D4e23807E5e370C07577Dd152?a=9206)

Minting an NFT means that you're creating it and preserving it on the blockchain (in the case of Zorbs, Ethereum's blockchain).&#x20;

### Further Reading 📑

If you're interested in an end-to-end guide for minting an NFT, check these out:

* `@novaraptur`'s [An Artist's Guide to Solana NFTs and Holaplex](https://medium.com/@novaraptur/an-artists-guide-to-solana-nfts-and-holaplex-3acac536d965)
* OpenSea Blog | [The Non-Fungible Token Bible: Everything you need to know about NFTs](https://opensea.io/blog/guides/non-fungible-tokens/)
* Foundation | [`A complete guide to minting an NFT`](https://help.foundation.app/en/articles/4742869-a-complete-guide-to-minting-an-nft)&#x20;
* [Metaplex Docs](https://docs.metaplex.com)
