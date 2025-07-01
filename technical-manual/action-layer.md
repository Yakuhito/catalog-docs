---
description: A special primitive designed to accommodate the needs of Chia dApps
---

# Action Layer

_Note_: The base action layer code is available [here](https://github.com/Yakuhito/slot-machine/blob/master/puzzles/singleton/action.clsp). The default finalizer code is available [here](https://github.com/Yakuhito/slot-machine/blob/master/puzzles/singleton/finalizer.clsp). The reserve finalizer code is available [here](https://github.com/Yakuhito/slot-machine/blob/master/puzzles/singleton/reserve_finalizer.clsp).

The action layer is intended to be a dApp singleton's inner puzzle. The puzzle maintains a state, which is usually a list of frequently-needed variables that the dApp can update.

The action layer allows anyone to select one or more actions to run. The list of actions is stored as a Merkle tree, with the corresponding `MERKLE_ROOT` being static by default (the latter property may be changed with a custom finalizer).

The layer ensures every action is part of the Merkle tree and runs it with a custom solution, also providing the State truth. An action's solution will always be: `(list State_Truth solution)` , where `solution` is user-provided and must be validated by the action. `State_Truth` is `(Ephemeral_State . Persistent_State)`. The ephemeral state starts as `()` every spend (i.e., the first action always receives the null value) and can be modified for the next action through return values (see below). The persistent state is saved in the action layer's puzzle - it is, by default, always set by the last run action, except for the very first value, which is set when the eve action layer is first created. The puzzle is also optimized such that each action's code is only revealed at most once each spend, no matter how many 'calls' are made to run the action.

The ephemeral state offers flexibility for dApps. For example, some dApps might use it as a counter which, along with the coin id, determines a nonce for asserting payments via the normal offer puzzle. Other dApps may use it as a switch to indicate whether the action being run is the first one in the spend - along with `(list ASSERT_HEIGHT_RELATIVE 0)`, the ephemeral state makes it possible to make an action that can only run as the first action in a given block.

Every action is expected to return two new values as a cons-box: `(c new_state_truth new_conditions)`. `new-conditions` will be added to the coin's output conditions list when using the default finalizer - this behavior, however, may be modified with a custom finalizer. `new_state_truth` will be provided to the next action that runs. The finalizer will usually set the next coin's `STATE` as  `new_state = (r new_state_truth)`  using values returned by the last evaluated action. This is the case for both the default and the reserve finalizers.

Allowing multiple `actions` to run significantly lowers cost compared to only running one action per singleton spend. Moreover, this architecture is RBF-friendly: when a spend is pending in the mempool, a user wanting to use the singleton may add their actions at the end of the array of pending actions, completely avoiding the creation of a new singleton coin.

The action layer is split into two parts. The base layer covers the functionality described above, validating actions can be run and evaluating them with the most recent state. The finalizer is used to 'finish' the spend by handling everything else.

The finalizer is given action outputs as a list where item _i_ is the output of the _&#x69;_&#x74;h action that was run. The default finalizer simply flattens the list and re-creates the action singleton using the new state (i.e., the state returned by the last action).

The finalizer, however, can be customized. A good example is the reserve finalizer, which, aside from flattening the list and re-creating the singleton, also manages an external token reserve owned by the singleton. A finalizer has access to the initial state of the singleton (i.e., the `STATE`  parameter that was curried in in the current base action layer), as well as the new state. The reserve finalizer uses this information to spend the current singleton's reserve and recreate it. Moreover, actions of an action layer using the reserve finalizer can add conditions to be included in the reserve's output by prepending them with `-42`. In other words, if one of the conditions outputted by a given action is `(list -42 CREATE_COIN puzzle_hash 1 (list hint))` , the condition will be taken by the finalizer and included in the reserve's delegated puzzle, ensuring the reserve has `(list CREATE_COIN puzzle_hash 1 (list hint))` in its output.

Custom finalizers can be used to significantly reduce cost and complexity of some dApps. For example, a Reward Distributor processing 10 payouts in the same spend will only spend its reserve once. Moreover, individual actions don't have to send any type of messages to the reserves or ensure that they exist.

**Warning**: Most finalizers, including the default and reserve ones, will assume the singleton's amount to be 1.

**Warning 2**: The reserve finalizer treats the first amount in the state as the reserve amount.

_Written by_ [_yakuhito_](https://x.com/yakuh1t0) _from_ [_FireAcademy.io_](https://fireacademy.io/) _on Feb 11th, 2025._
