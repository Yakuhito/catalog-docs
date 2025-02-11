---
description: Useful concepts to have in mind when trying to decode Technical Manual(s)
---

# Other Useful Concepts

### Controller Singletons

Most complex dApps - including TibetSwap, warp\[.]green, CATalog, XCHandles, and the DIG Reward Distributor - use singletons that contain the majority of the important logic. For example, while TibetSwap pools have two reserves each, it is the pool singleton that calculates the new amount of reserves each spends and communicates the re-creation conditions, which the reserves 'blindly' follow after receiving announcements/messages from the singleton. More generally, a dApp singleton may create 'helper' coins that store information or value (be it in XCH or CATs), but the 'most important' logic lies within the singleton. Controller singletons is a term sometimes used to describe this property both in documentation and Chialisp comments/variable names. It signifies that the 'burden of logic' is located in the singleton, not its controlled (helper) coin(s).

### Malicious Farmers

A dApp developer's worst nightmare is almost certain to include a malicious farmer. This term is used as the embodiment of a malicious actor. The 'malicious farmer' case is used to exemplify potential attacks because farmers usually have the ideal conditions to profit from badly-designed dApps: they know the contents of the blocks they're about to farm and, unlike normal farmers, wish to hurt normal users, usually by maximizing farmer rewards (in terms of assets - XCH, CATs, handles in demand, and so on). Malicious actors may not be able to modify mempool items beyond RBF capabilities or modify the order of future transaction blocks at will. However, a well-designed app should withstand the 'worst-case scenario' where the adversary has control over as many aspects as possible to really be considered secure.

### CAT Makers

Managing CAT reserves is more difficult than it might initially seem. A CAT's outer and inner layer are known to a singleton (the inner puzzle is usually a 'pay to delegated puzzle by singleton puzzle') - however, the intermediary layers are not. A DAO CAT might have an intermediary layer that manages active votes, while revocable CATs have a revocation layer sitting between the outer and inner puzzles. A dApp might want to allow the flexibility of being able to 'own' or interact with CATs whose intermediary layers haven't yet been written. CATalog and XCHandles, for example, want this flexibility in the CAT used for registration payments. To have it, they allow governance (the price singleton) to set the current CAT maker instead of CAT tail hash.

_Note_: The default CAT maker can be found [here](https://github.com/Yakuhito/slot-machine/blob/master/puzzles/default_puzzles/default_cat_maker.clsp).

The puzzle accepts the tail hash hash (double-hashing is simply employed for efficiency), the desired inner puzzle hash of a coin, and a user solution. It returns the full puzzle hash of a CAT coin with those parameters. While the user-provided solution is not used, one can imagine a case where the user could supply intermediary layer state such as active votes for a DAO CAT. Note that the maker does not return any output conditions.

To summarize, CAT makers are short, simple to write 'adapters' that produce the full puzzle hash for a CAT coin given outer layer parameters (e.g., TAIL hash) and the inner ('custody') puzzle. They're employed to support CATs with different intermediary layer structures.&#x20;

### The Delegated State Action

The registration fees for CATalog and XCHandles are paid in CAT tokens (initially, these represent warped stablecoins). One design decision was to allow another singleton (sometimes referred to as the 'price singleton') to control pricing parameters and puzzles. For CATalog, this means that the pricing singleton may change the CAT the registration fees are paid in, as well as the amount. XCHandles also allows the pricing singleton to change both pricing puzzles (the one that controls the 'base' price of a handle and the one that handles expiry auctions).

Since both registries' states consist fully of pricing parameters, a 'delegated state' action was added to both. The action updates the current singleton's state to one sent by the price singleton via a message with mode 18 (puzzle-puzzle). While the action has limited potential to be useful to other dApps, it enables the pricing parameter freedom that both XCHandles and CATalog require.

_Note_: The delegated state action code can be found [here](https://github.com/Yakuhito/slot-machine/blob/master/puzzles/actions/shared/delegated_state.clsp).

### Launching

Live dApps will have restrictions on what can be done - CATalog, for example, only allows slot keys to be the hash of a TAIL, and left neighbor < right neighbor. These rules may have to be broken during setup. For the doubly-linked lists to exist in CATalog (and XCHandles, for that matter), the special 'list ends' must be created. These ends have the lowest and highest possible values as keys (see notes below) - which we don't know the preimage (TAIL reveal) of.&#x20;

_Note_: Lowest 32-byte value when using `<` for comparison: `0x8000000000000000000000000000000000000000000000000000000000000000`

_Note_: Highest 32-byte value when using `>`  for comparison: `0x7fffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff`

They're needed for the first registration and may be needed for subsequent dApp interactions as well. To avoid a lot of special casing, CATalog uses a special inner puzzle for the eve singleton that creates the two ends as slots and re-creates the singleton using the 'regular' inner puzzle. More generally, dApps might want to allow 'breaking the rules' at launch - people verifying a dApp is truly decentralized must ensure the 'constrained' inner puzzle is currently inside the dApp singleton and the transition is only one way (i.e., the unconstrained inner puzzle can create the constrained puzzle, but not the other way around - otherwise the current inner puzzle isn't really constrained).

**Warning:** Special care must be put into ensuring that everything done when there are no restrictions is compatible with what can be done when there are restrictions. For example, CATalog must not break when it interacts with one or both of the slots representing the ends of the doubly-linked linked list.

### Pre-Mines

Pre-mines are another way of bending the rules at launch. The number of 'unconstrained' generations of a singleton before it's upgraded to a dApp should be minimal, ideally zero (although 1 is far more practical). This minimizes the 'special case' attack surface considerably. However, some dApps might still want to 'pre-mine' a lot of records. For example, CATalog might need to 'import' CATs registered before launch. This is likely a multi-block endeavor requiring going through the 'normal' registration process with more constraints - only the deployer should be able to do it, and it should be easily auditable afterwards. From parsing and security perspectives, this is much easier done by creating a special payment CAT that only the deployer owns. The deployer registers all 'imported' CATs before switching the payment CAT to a non-exclusive one (e.g., wUSDC.b) and transferring control of the price singleton to its intended party. This initial 'gated initiation' can then easily be parsed and verified by software that checks whether a registry was correctly launched.

_Written by_ [_yakuhito_](https://x.com/yakuh1t0) _from_ [_FireAcademy.io_](https://fireacademy.io/) _on Feb 11th, 2025._

