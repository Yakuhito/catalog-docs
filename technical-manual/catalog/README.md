# CATalog

CATalog is, at a high level, a registry that allows non-expiring registrations of data hashes. Each registration creates an NFT representing the registration. By social convention, TAILs are the data being registered, and the returned NFTs are used to store and update CAT details such as name or image.

The CATalog registry's state is `(cat_maker_puzzle_hash . registration_price)` , where the first argument is the CAT maker for the payment CAT, while the second variable holds the amount of the CAT to be paid for a registration.

The registry launches NFTs by first creating 'uniqueness prelauncher' coins, which then create launcher coins and the eve NFTs. Uniqueness prelaunchers contain the unique data's hash (i.e., the TAIL hash) in their puzzle, allowing other dApps and APIs to efficiently verify the authenticity of the NFTs.&#x20;

_Note_: The default CAT maker can be found [here](https://github.com/Yakuhito/slot-machine/blob/master/puzzles/nft/uniqueness_prelauncher.clsp).

_Written by_ [_yakuhito_](https://x.com/yakuh1t0) _from_ [_FireAcademy.io_](https://fireacademy.io/) _on Feb 11th, 2025._

