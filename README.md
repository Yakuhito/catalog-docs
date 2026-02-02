---
description: Frequently Asked Questions about CATalog
---

# CATalog FAQ

### What is CATalog?

CATalog is a decentralized, on-chain shared database containing CAT asset details such as symbols, names, and display images. CAT issuers register their CATs and receive an NFT which can be used to change the corresponding entry's details at any time. Verifiers - usually marketplaces or sites that display CAT data - maintain a list of every CAT that abides by their listing rules (e.g., 'The CAT is not trying to impersonate another CAT'), as well as potential overrides and other trusted verifiers. Because everything is on-chain and on DataLater, all websites, wallets, and dApps can see data and act on it.

### How is a CAT's owner determined?

The owner of a CAT is defined as the first entity to register that CAT's TAIL program on CATalog. For this reason, **must** use a CAT issuance tool that supports CATalog in order to avoid someone else claiming their CAT's entry.

### How should I refer to CATalog?

Please always spell CATalog like this sentence does - the first three letters should be in uppercase, while the rest of the word is in lowercase. The only exception to this rule is Rust code, where you may spell CATalog as `Catalog`  to meet naming conventions.

### What are the fees to list a CAT?

It currently costs 5 wUSDC.b to create a new entry in CATalog. This fee helps prevent spam. CATalog entries do **not** expire and do not require any renewal fees. Verifiers may require additional funds in exchange for verifying CATs.

For CATalog, the price singleton has the power to change both the fee token (wUSDC.b) and the CAT price (5.0). This singleton is currently a 7-of-11 multisig controlled by [warp.green validators](https://docs.warp.green/#who-are-the-validators).

### How do I know CATalog was properly deployed?

Anyone can use [this CLI tool](https://github.com/Yakuhito/slot-machine/) to verify the validity of CATalog's mainnet deployment procedure by running `cargo r catalog verify-deployment`.
