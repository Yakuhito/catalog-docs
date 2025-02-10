---
description: A special primitive designed to accommodate the needs of Chia dApps
---

# Action Layer

_Note_: The base action layer code is available [here](https://github.com/Yakuhito/slot-machine/blob/master/puzzles/singleton/action.clsp). The default finalizer code is available [here](https://github.com/Yakuhito/slot-machine/blob/master/puzzles/singleton/finalizer.clsp). The reserve finalizer code is available [here](https://github.com/Yakuhito/slot-machine/blob/master/puzzles/singleton/reserve_finalizer.clsp).

The action layer is intended to be a dApp singleton's inner puzzle. The puzzle maintains a state, which is usually a list of frequently-needed variables that the dApp can update.

The action layer allows anyone to select one or more actions to run. The list of actions is stored as a Merkle tree, with the corresponding `MERKLE_ROOT` being static by default (the latter property may be changed with a custom finalizer).

The layer ensures every action is part of the Merkle tree and runs it, providing the current state of the action layer as truth (this may also be the state returned by the previous action that was run). An action's solution will always be: `(list Current_State solution)` , where `solution` is user-provided and must be validated by the action.

Every action is expected to return two new values as a cons-box: `(c new_state new_conditions)`. `new-conditions` will be added to the coin's output conditions list when using the default finalizer - this behavior, however, may be modified with a custom finalizer. `new_state` will be provided to the next action that runs. The finalizer will usually set the next coin's `STATE` as the `new_state`  returned by the last evaluated action.

Allowing multiple `actions` to run significantly lowers cost compared to only running one action per singleton spend. Moreover, this architecture is RBF-friendly: when a spend is pending in the mempool, a user wanting to use the singleton may add their actions at the end of the array of pending actions, completely avoiding the creation of a new singleton coin.

TODO

The action layer is split into two parts. The base layer covers the functionality described above, validating actions can be run and evaluating them with the most recent state. The finalizer .

**Warning**: Most finalizers, including the default and reserve ones, will assume the singleton's amount to be 1.
