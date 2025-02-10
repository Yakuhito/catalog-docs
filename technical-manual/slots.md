# Slots

A singleton slot coin is created to store values that may be read at a later spend by the singleton. In a way, slots allow singletons to have 'long-term' memory. Another way to think about slots are one-time tickets.

To read the values, the singleton will spend the slot coin by sending an empty message with mode 18 (puzzle-puzzle).

Slots are curried twice: the first time, SINGLETON\_STRUCT is curried in. Optionally, if the slot has a nonce (i.e., the controller singleton has multiple slot types that it needs to differentiate between), a simple nonce layer is also added. Controller singletons will usually store the 1st curry hash (e.g., SLOT\_1ST\_CURRY\_HASH in CATalog's register action), as the value is static and unique for every singleton controller launcher id (and nonce). The first curry hash is also used as a hint when launching a slot.

The second curry adds 'VALUE\_HASH' to the puzzle. This is the hash of the value the controller singleton wants to consume. Slots only store hashes since this means the whole value doesn't have to be revealed in the slot's puzzle reveal, potentially saving a significant number of bytes for each slot spend.

**Warning**: Slots do not ensure uniquenes by themselves. Users can always 'omit' a given slot to trick a naive dApp that the slot doesn't exist. For example, a naive handle reigstration app might be tricked into registering the same handle twice. If dApps need to ensure slots are not skipped, they should index them or build more advanced data structures such as cross-slot doubly-linked sorted lists. More information about the technique can be found in [this blog post](https://blog.fireacademy.io/p/solving-the-problem-of-uniqueness).

**Warning 2**: Slots assume the controller singleton always has an amount of 1.
