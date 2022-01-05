---
description: ⚠️ Work-in-progress.
---

# 🟣 Zorb Contracts

Tracking Issue: [https://github.com/almndbtr/field-notes/issues/3](https://github.com/almndbtr/field-notes/issues/3)

### Summary

From zorb.dev:

> [zorb ↗ ](https://github.com/ourzora/zorb/tree/main/packages/zorb-web-component)is a simple, open-source identity system for the decentralized Internet. It is maintained by ZORA, the decentralized marketplace protocol.

### Contracts Overview

On initial release, there are [two contracts](https://github.com/ourzora/zorb/tree/main/packages/zorb-contracts):&#x20;

* [ZorbNFT.sol](https://github.com/ourzora/zorb/blob/main/packages/zorb-contracts/contracts/ZorbNFT.sol)
* [ColorLib.sol](https://github.com/ourzora/zorb/blob/main/packages/zorb-contracts/contracts/ColorLib.sol)

Shortly after the minting closed, `@m1guelpf` and `@iannash` worked on [shipping a new set of contracts for refrigerating Zorbs](https://github.com/ourzora/zorb/tree/main/packages/zorb-fridge-contracts).

#### ZorbNFT.sol

This is the "main" contract for minting a [Zorb](https://zorb.dev).

#### ColorLib.sol

This is a custom library for handling the math functions required to generate the gradient step colors.
