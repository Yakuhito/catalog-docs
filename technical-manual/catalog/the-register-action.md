---
description: The whole point of CATalog
---

# The Register Action

_Note_: The register action code be found [here](https://github.com/Yakuhito/slot-machine/blob/master/puzzles/actions/catalog/register.clsp).

The register action, as the name suggest, registers a new CATalog entry. This requires a precommitment coin that contains the TAIL hash, the initial NFT owner puzzle hash, as well as refund-related information (CAT maker and CAT maker solution) to be created before.

When the action is run, it:

* Creates a slot that represents the CAT. This prevents future registrations from registering the same TAIL hash.
* Spends the slots representing the new slot's neighbors to assert that they exist, and updates their neighbors so the new structure is: left neighbor <-> new slot <-> right neighbor
* Creates a uniqueness prelauncher for the CAT NFT, whose puzzle contains the TAIL hash. The prelauncher puzzle hash and parent can be used in the future to assert that a given NFT represents a particular CAT asset id (TAIL hash/CATalog entry).
* Consumes the precommitment coin
* Asserts the CAT NFT was correctly created (i.e., it was created to the specified owner)

The action also creates a puzzle announcement. This announcement can be asserted to ensure the dApp singleton is running the right action with the right parameters. For the register action, the announcement prefix, `'r'` , is concatenated to `(sha256tree (tail_hash . initial_nft_owner_ph))`.

_Written by_ [_yakuhito_](https://x.com/yakuh1t0) _from_ [_FireAcademy.io_](https://fireacademy.io/) _on Feb 14th, 2025._
