---
description: Long-term memory for singletons
---

# Slots

_Note_: The slot puzzle is available [here](https://github.com/Yakuhito/slot-machine/blob/master/puzzles/singleton/slot.clsp).

A singleton slot coin is created to store values that may be read at a later spend by the singleton. In a way, slots allow singletons to have 'long-term' memory. Another way to think about slots are one-time tickets.

To read the values, the singleton will spend the slot coin by sending an empty message with mode 18 (puzzle-puzzle).

Slots are curried twice: the first time, `SINGLETON_STRUCT` is curried in. Optionally, if the slot has a nonce (i.e., the controller singleton has multiple slot types that it needs to differentiate between), a simple nonce layer is also added. Controller singletons will usually store the 1st curry hash (e.g., `SLOT_1ST_CURRY_HASH` in CATalog's register action), as the value is static and unique for every singleton controller launcher id (and nonce). The first curry hash is also used as a hint when launching a slot.

The second curry adds `VALUE_HASH` to the puzzle. This is the hash of the value the controller singleton wants to consume. Slots only store hashes since this means the whole value doesn't have to be revealed in the slot's puzzle reveal, potentially saving a significant number of bytes for each slot spend.

The slot puzzle verifies the coin's parent is a singleton with the same launcher id as the singleton spending the slot, ensuring the slots aren't 'fake.' The slot puzzle does not, however, assert anything about the controller singleton's inner puzzle. This property accommodates more complex 'launches' where, for example, the CATalog eve singleton has a custom inner puzzle that launches two special slots (list ends, which have special properties) and then sets the new inner puzzle to the 'dApp' puzzle that allows anyone to register new CATs.

**Warning**: Slots do not ensure uniquenes by themselves. Users can always 'omit' a given slot to trick a naive dApp that the slot doesn't exist. For example, a naive handle registration app might be tricked into registering the same handle twice. If dApps need to ensure slots are not skipped, they should index them or build more advanced data structures such as cross-slot doubly-linked sorted lists. More information about the technique can be found in [this blog post](https://blog.fireacademy.io/p/solving-the-problem-of-uniqueness).

**Warning 2**: Slots assume the controller singleton always has an amount of 1.

_Written by_ [_yakuhito_](https://x.com/yakuh1t0) _from_ [_FireAcademy.io_](https://fireacademy.io/) _on Feb 11th, 2025._
