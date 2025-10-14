---
description: >-
  What Abstract Names is, how it’s built, how you mint and manage names, and how
  ownership and renewals work.
---

# Identity layer

### What is Abstract Names?

Abstract Names turns wallet addresses into readable names and on-chain identities that you can recognize at a glance. The naming system is built from a few parts that each do a specific job. Most of the time you’ll interact with the Manager App, which provides a simple interface to search, mint, and manage your name.

### Core modules

A modular [architecture](../smart-contracts/architecture.md) with 5 core modules.

| Module                                         | What it does                                                          |
| ---------------------------------------------- | --------------------------------------------------------------------- |
| [Registry](../smart-contracts/registry.md)     | Stores names as NFTs with their length tier and expiration date.      |
| [Controller](../smart-contracts/controller.md) | Handles pricing, sale phases, renewals, and reserved names.           |
| [Resolver](../smart-contracts/resolver.md)     | Answers where a name points: an address, profile records, or content. |
| [Validator](../smart-contracts/validator.md)   | Ensures names use allowed characters and the right length.            |
| [Renderer](../smart-contracts/renderer.md)     | Renders a visual appealing NFT with the bound name displayed on it.   |

### Minting

Minting a name is straightforward. You search for an available name and choose how many years to register. Pricing depends on length: shorter names are rarer and fall into higher tiers. After minting, you own an NFT that represents your name until it expires. You can transfer it like any other NFT.

### What you can do with a name

Managing a name lets you decide what the name points to. You can set it to resolve to a specific address, add profile details like a website or social handle, and attach a content hash for decentralized storage links. You can also set your name as “primary,” which allows apps to display your name when they see your address.

### Expireys

Names expire so that unused names can eventually return to the pool. Before your expiration date, you can renew for one or more years. If you transfer a name, sensitive resolver data such as a custom address or content hash is cleared for safety, and older profile records are treated as outdated unless they’re reset by the new owner.
