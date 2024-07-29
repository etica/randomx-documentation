---
description: Everything you need to know about the Reserved Space
---

# Reserved Space

#### Why a Reserved Space

In RandomX system there is not a unique Blob that all miners work on. In fact in RandomX each miner must get a different blob, two different miners do not mine with the same blob.\
The _**Reserved Space**_ is a specific location of the blob that is supposed to be updated to generate unique blobs for each miner.

#### How it works

\
_**Monero:**_ In Monero, there is a specific location in the block templates where it is possible to set extraNonce data that will then generate different blob hashes when the block template is hashed to generate the blob.&#x20;

**Etica:** Etica doesn't use a block template system so the Reserved Space works slightly differently than Monero. Etica uses a ReservedSpaceOffset at position 55bytes, ReservedSpaceSize is 8bytes, so ReservedSpace data will be inserted in the blob at position 55-62bytes.\
\
Here is how Etica Reserved Space works:\
\
1\. **extraNonce**:\
Each miner get an extraNonce (random 8bytes string).\
\
2\. **Update the blob**\
a) Then a 32bytes hash is generated using keccack256 (also named soliditysha3 ) to hash (address, extraNonce, challengeNumber) like this:

```
extraNonceHash = web3utils.soliditySha3(poolAddress, extraNonce, challengeNumber);
```

_The address input must be the address that sends the transaction on blockchain._\
_extraNonce input is the random 8bytes string generated specifically for a miner._\
_challengeNumber input must be the current ETI challengeNumber._

b) Then the extraNonceHash 32bytes is truncated to only take the first 8bytes. This 8bytes data will then be placed at ReservedSpaceOffset position (at position 55-62bytes)\
\
Example of the whole process to generate a specific blob for each miner:

{% hint style="info" %}
Example\
\
We'll be using the following randomxBlob example (randomxBlob is a general blob retireved from blockchain that is updated for each new block):\
da4670bae10db93a46fad64b14d091189ca<mark style="color:green;">fa19f61674</mark>963f2f2d635b4f2a812685f828da7adb7de554b4090363931bde43a6042eafd6197fbf4866191c9e845ae615393f941f4bb9f92b7ed537c2fb7\
\
For reminder: <mark style="color:green;">NonceOffset</mark> is at position 39-42bytes, this is where the miners will iterate to put random 4bytes nonces (will be put at the position of <mark style="color:green;">**fa19f61674**</mark>)\
But for our example we need to pay attention to the ReservedSpaceOffset:



da4670bae10db93a46fad64b14d091189ca<mark style="color:green;">fa19f61674</mark>963f2f2d635b4f2a812<mark style="color:purple;">**685f828da7adb7de**</mark>554b4090363931bde43a6042eafd6197fbf4866191c9e845ae615393f941f4bb9f92b7ed537c2fb7\
\
<mark style="color:purple;">ReservedSpaceOffset</mark> is at position 55-62bytes, this is where each miner must put a unique random 8bytes data to make sure they are the only one mining with this specific blob. In our example each miner would get a unique blob by replacing the data at the position of <mark style="color:purple;">**685f828da7adb7de**</mark>\
\
_**ReservedSpace blob examples:**_\


_Example1:_\
Miner1 gets extraNonce: 98b88b8366a79836 (random 8bytes)\
By applying the process explain above (2. **Update the blob)** the Miner1 would generate a random ReservedHash 8bytes data from the 8bytes extraNonce. Then this ReservedHash will be placed in the blob at position 55-62bytes. For instance in our example let's say the extraNonce _98b88b8366a79836_ generates this ReservedHash: _bc52365203f86653_.\
The **randomxBlob** for Miner1 would then be updated like this:\
da4670bae10db93a46fad64b14d091189ca89abcdef963f2f2d635b4f2a812_<mark style="color:purple;">**bc52365203f86653**</mark>_554b4090363931bde43a6042eafd6197fbf4866191c9e845ae615393f941f4bb9f92b7ed537c2fb7\
This updated blob is the blob to **send to Miner1 only**\


Example2:\
Miner2 would get another extraNonce, thus would get another 8bytes ReservedHash. The blob for Miner2 would also be updated with the personalised ReservedHash of Miner2 exactly like for Miner1.\
\
Miner2 gets extraNonce: 3fae5b7c9d2a4f8b (random 8bytes)\
This leads to ReservedHash: 7d2e8f1a6c4b9035\
The **randomxBlob** for Miner2 would then be updated like this:\
da4670bae10db93a46fad64b14d091189ca5f6e7d8c963f2f2d635b4f2a812<mark style="color:purple;">**7d2e8f1a6c4b9035**</mark>554b4090363931bde43a6042eafd6197fbf4866191c9e845ae615393f941f4bb9f92b7ed537c2fb7
{% endhint %}

_**Note:**_ \
_When passing the Nonce to Ethereum related functions like Ethereum libraries it is often necessary to add 0x at the begining of the Nonce. For instance here it would beb 0x89abcdef_ _and 0x5f6e7d8c._
