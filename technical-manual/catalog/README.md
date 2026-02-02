# CATalog

CATalog is, at a high level, a registry that allows non-expiring registrations of data hashes. Each registration creates an NFT representing the registration. By social convention, TAILs are the data being registered, and the returned NFTs are used to store and update CAT details such as name or image.

The CATalog registry's state is `(cat_maker_puzzle_hash . registration_price)` , where the first argument is the CAT maker for the payment CAT, while the second variable holds the amount of the CAT to be paid for a registration.

The registry launches NFTs by first creating 'uniqueness prelauncher' coins, which then create launcher coins and the eve NFTs. Uniqueness prelaunchers contain the unique data's hash (i.e., the TAIL hash) in their puzzle, allowing other dApps and APIs to efficiently verify the authenticity of the NFTs.&#x20;

Generally, announcements and messages from the main registry are sent using a one-byte prefix followed by a hash of the actual message contents. To prevent collisions, the prefixes and message structures are defined in a single file ([here](https://github.com/xch-dev/chia-wallet-sdk/blob/main/crates/chia-sdk-driver/src/primitives/action_layer/catalog_registry_prefix.rs)).

_Written by_ [_yakuhito_](https://x.com/yakuh1t0) _from_ [_FireAcademy.io_](https://fireacademy.io/) _on Feb 11th, 2025._

