---
description: General presentation and key concepts of how RandomX works
---

# RandomX in Monero: An Overview

RandomX is a proof-of-work (PoW) algorithm designed to be ASIC-resistant, favoring CPU mining. Here's how it works in Monero:

#### 1. Block Template

Monero uses a block template system. When a miner requests work, a block template is created and then hashed to obtain a blob.

#### 2. Blob Creation&#x20;

The block template is hashed to create a "blob" - a byte string that miners work on to try to find a valid nonce. In Monero system the blob is 76bytes long.

#### 3. Nonce Insertion

Miners insert a 4-byte nonce at a specific position (nonceOffset) in the blob. In Monero the nonceOffset is at 39bytes

#### 4. Reserved Space

Monero includes a reserved space in the blob where miners can insert custom data, ensuring each miner works on a unique problem.

#### 5. Mining Process

Miners repeatedly change the nonce and compute the RandomX hash of the modified blob until they find a hash that meets the network's difficulty target.

#### 6. Nonce submission

When a valid solution is found, the miner submits the nonce and resulting hash to the network for verification.

This is a general overview, next parts will go more in details.
