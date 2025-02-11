---
description: Commit-reveals made easy
---

# Commitments

_Note_: The inner puzzle for commitment coins can be found [here](https://github.com/Yakuhito/slot-machine/blob/master/puzzles/singleton/precommit.clsp).

Sometimes, dApps need to use a commit-reveal pattern. A good example is registering a CAT on CATalog: a user only needs a TAIL reveal to register their CAT. However, one cannot simply provide it to a dApp: a malicious farmer could see the transaction, extract the TAIL reveal, and register the CAT instead.

To solve this issue, CATalog (and XCHandles) employs a commit-reveal mechanism. Users wanting to register a CAT must create a commitment coin that contains the TAIL reveal, as well as other registration-specific data. The commitment coin must stay unspent for a given number a blocks - 32 is the 'safe' minimum generally recommended. After the commit phase is over, the user can reveal the TAIL by spending the commitment. A mempool observer cannot register the same value, as they'd have to create a commitment coin and wait the designated number of blocks before the puzzle becomes spendable.

To commit assets other than XCH, commitment puzzles must be wrapped in layers such as the CAT v2 one. The puzzle itself supports this, but dApps must ensure that the controller singleton can build the correct full puzzle for commitment coins (by using CAT makers, for example - described in the next page).

It's vital to note that any dApp must also carefully consider refunds. Schemes using commitments lock funds and involve two distinct transactions. Sometimes, commitment coins that appeared valid when the first transaction might become invalid by the time the second (reveal) transaction can be made. As such, controller singletons should offer a mechanism where funds can be returned to users if the commitment can't be used for a registration. For example, CATalog can 'refund' lock funds if the CAT in which payments are made has changed, the payment amount is wrong, or if the TAIL has already been registered.

**Warning**: Refund logic must be carefully registered. If any commitment can be refunded, for example, a malicious farmer may change a registration action to a refund one. They may then create a commitment for the same TAIL hash. The rightful owner of the CAT would not be able to register their CAT as they would have to start the registration process all over again - which they only realize after the malicious farmer's commitment coin has at least 1 confirmation (i.e., it was already included in at least the most recently farmed block).

**Warning 2**: Commitment coins are only effective if implemented correctly. For example, only committing a TAIL by itself is not useful at all - a farmer can use the same commitment coin to register the CAT to their address. CATalog commitments also contain the user's NFT receive address, as well as other data.

_Written by yakuhito from_ [_FireAcademy.io_](https://fireacademy.io/) _on Feb 11th, 2025._
