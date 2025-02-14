---
description: Something went wrong - but worry not, you can get your funds back.
---

# The Refund Action

_Note_: The refund action code be found [here](https://github.com/Yakuhito/slot-machine/blob/master/puzzles/actions/catalog/refund.clsp).

Registering an entry in CATalog requires two transactions. First, a user must create a precommitment coin. Second, after waiting for 32 blocks, the user needs to interact with the registry, which checks (spends) their precommitment coin.

During the period between the two actions,  it is possible that the precommitment becomes invalid. This would happen if:

* The CAT maker puzzle of the registry changes (e.g., CATalog used to take fees in wUSDC.b, so the precommitment coin is a wUSDC.b CAT - but the price singleton updated the payment CAT to wUSDC, which means the precommitment is now in the wrong CAT)
* The precommitment amount is wrong (e.g., the price singleton changed price of a registration from 20 wUSDC.b to 10 wUSDC.b)
* The TAIL hash has been registered

The action also creates a puzzle announcement. This announcement can be asserted to ensure the dApp singleton is running the right action with the right parameters. For the refund action, the announcement prefix, `'`$`'` , is concatenated to `(sha256tree (tail_hash . initial_nft_owner_ph))`.

_Written by_ [_yakuhito_](https://x.com/yakuh1t0) _from_ [_FireAcademy.io_](https://fireacademy.io/) _on Feb 14th, 2025._
