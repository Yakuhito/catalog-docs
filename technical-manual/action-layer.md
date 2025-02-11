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

The action layer is split into two parts. The base layer covers the functionality described above, validating actions can be run and evaluating them with the most recent state. The finalizer is used to 'finish' the spend by handling everything else.

The finalizer is given action outputs as a list where item _i_ is the output of the _&#x69;_&#x74;h action that was run. The default finalizer simply flattens the list and re-creates the action singleton using the new state (i.e., the state returned by the last action).

The finalizer, however, can be customized. A good example is the reserve finalizer, which, aside from flattening the list and re-creating the singleton, also manages an external token reserve owned by the singleton. A finalizer has access to the initial state of the singleton (i.e., the `STATE`  parameter that was curried in in the current base action layer), as well as the new state. The reserve finalizer uses this information to spend the current singleton's reserve and recreate it. Moreover, actions of an action layer using the reserve finalizer can add conditions to be included in the reserve's output by prepending them with `-42`. In other words, if one of the conditions outputted by a given action is `(list -42 CREATE_COIN puzzle_hash 1 (list hint))` , the condition will be taken by the finalizer and included in the reserve's delegated puzzle, ensuring the reserve has `(list CREATE_COIN puzzle_hash 1 (list hint))` in its output.

Custom finalizers can be used to significantly reduce cost and complexity of some dApps. For example, a DIG Reward Distributor processing 10 payouts in the same spend will only spend its reserve once. Moreover, individual actions don't have to send any type of messages to the reserves or ensure that they exist.

**Warning**: Most finalizers, including the default and reserve ones, will assume the singleton's amount to be 1.

**Warning 2**: The reserve finalizer treats the first amount in the state as the reserve amount.
