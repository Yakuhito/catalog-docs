---
description: Long-term memory for singletons
---

# Slots

_Note_: The slot puzzle is available [here](https://github.com/Yakuhito/slot-machine/blob/master/rue-puzzles/singleton/slot.rue) ([Chialisp](https://github.com/Yakuhito/slot-machine/blob/master/puzzles/singleton/slot.clsp)).

A slot is a coin intended to store values that may be read at a later time/spend by the singleton that created it. In a way, slots allow singletons to have 'long-term' memory. Another way to think about slots is 'one-time tickets only redeemable by the issuing singleton.'

To read the values, the singleton will spend the slot coin by sending an empty message with mode 18 (puzzle-puzzle).

Slots are curried twice: the first time, `SINGLETON_STRUCT` and `NONCE` are curried in. Nonce is used to distinguish different slots when needed - for example, a 'user rewards' slot might have a different nonce than a 'round reward info' slot, even though they both have the same structure in their values (e.g., 3 numbers). This deters attacks where a slot of one type may be confused by a slot of another type with the same data structure, as the 1st curry depends on the slot type (nonce). Controller singletons will usually store the 1st curry hash (e.g., `SLOT_1ST_CURRY_HASH` in CATalog's register action), as the value is static and unique for every singleton controller launcher id and nonce.

The second curry adds `VALUE_HASH` to the puzzle. This is the hash of the value the controller singleton wants to consume. Slots only store hashes since this means the whole value doesn't have to be revealed in the slot's puzzle reveal, potentially saving a significant number of bytes for each slot spend. As a general rule of thumb, the full values of spent and created slots can usually be determined by looking at a singleton's solution - this is the case for CATalog, XCHandles, and the reward distributor.

The slot puzzle asserts it was created by a singleton with the same launcher id as the singleton spending the slot, ensuring the slots can't be faked. The puzzle does not, however, assert anything about the controller singleton's inner puzzle. This property accommodates more complex 'launches' where, for example, the CATalog eve singleton has a custom inner puzzle that launches two special slots (list ends, which have special properties) and then sets the new inner puzzle to the 'dApp' puzzle that allows anyone to register new CATs.

**Note**: dApps should choose the hinting strategy that works best for their particular design. CATalog and XCHandles slots, for example, use the first curry hash as a hint to allow for much faster syncs. The Reward Distributor, on the other hand, uses either a reward/clawback puzzle hash or the hash of the epoch the slot is representing, minimizing the number of look-ups done for any interaction as the whole history of the singleton doesn't have to be parsed.&#x20;

**Warning**: Slots do not ensure uniquenes by themselves. Users can always 'omit' a given slot to trick a naive dApp that the slot doesn't exist. For example, a naive handle registration app might be tricked into registering the same handle twice. If dApps need to ensure slots are not skipped, they should index them or build more advanced data structures such as cross-slot doubly-linked sorted lists. More information about the technique can be found in [this blog post](https://blog.fireacademy.io/p/solving-the-problem-of-uniqueness).

**Warning 2**: Slots assume the controller singleton always has an amount of 1.

_Written by_ [_yakuhito_](https://x.com/yakuh1t0) _from_ [_FireAcademy.io_](https://fireacademy.io/) _on Feb 11th, 2025._
