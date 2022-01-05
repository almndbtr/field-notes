---
description: ⚠️ Work-in-progress.
---

# Zorb Contracts

### Summary

From zorb.dev:

> [zorb ↗ ](https://github.com/ourzora/zorb/tree/main/packages/zorb-web-component)is a simple, open-source identity system for the decentralized Internet. It is maintained by ZORA, the decentralized marketplace protocol.

### Overview of contracts

* At time of release, there are two contracts.
  * Source: [https://github.com/ourzora/zorb/tree/main/packages/zorb-contracts](https://github.com/ourzora/zorb/tree/main/packages/zorb-contracts)
    * [ZorbNFT.sol](https://github.com/ourzora/zorb/blob/main/packages/zorb-contracts/contracts/ZorbNFT.sol): this is the "main" contract for minting a [Zorb](https://zorb.dev).
    * [ColorLib.sol](https://github.com/ourzora/zorb/blob/main/packages/zorb-contracts/contracts/ColorLib.sol): this is a custom library for handling the math functions required to generate the gradient step colors (see [annotation](https://github.com/ourzora/zorb/blob/main/packages/zorb-contracts/contracts/ColorLib.sol#L65)).
* Shortly after the minting closed, `@m1guelpf` and `@iannash` worked on shipping a new set of contracts for refrigerating Zorbs.
  * Source: [https://github.com/ourzora/zorb/tree/main/packages/zorb-fridge-contracts](https://github.com/ourzora/zorb/tree/main/packages/zorb-fridge-contracts)
