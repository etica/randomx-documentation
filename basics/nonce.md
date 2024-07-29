---
description: Everything you need to know about the Nonce
---

# Nonce

#### Nonce

The nonce is a crucial element in the mining process. It is a 4-byte (32-bit) number that miners repeatedly modify and hash along with the blob to find a valid block hash. The nonce is located at a specific offset within the blob, known as the nonceOffset.\
_**In Monero and Etica the NonceOffset is at position 39bytes and has a size of 4bytes.**_

#### How it works

When miners receive the blob, their mining software iterates by replacing the bytes at the nonceOffset position. This process involves changing the nonce value, rehashing the blob, and checking if the resulting hash meets the network's difficulty target.\
\
Example of how and where the nonce is modified for a given blob

{% hint style="info" %}
Examples \
\
We'll be using the following blob example:\
da4670bae10db93a46fad64b14d091189ca<mark style="color:green;">fa19f61674</mark>963f2f2d635b4f2a812685f828da7adb7de554b4090363931bde43a6042eafd6197fbf4866191c9e845ae615393f941f4bb9f92b7ed537c2fb7\
\
NonceOffset is 39bytes, so the 4bytes nonces will be put at the position of <mark style="color:green;">**fa19f61674**</mark>\
\
Nonce 1: <mark style="color:blue;">**89abcdef**</mark>\
da4670bae10db93a46fad64b14d091189ca<mark style="color:blue;">**89abcdef**</mark>963f2f2d635b4f2a812685f828da7adb7de554b4090363931bde43a6042eafd6197fbf4866191c9e845ae615393f941f4bb9f92b7ed537c2fb7\
\
Nonce 2: <mark style="color:blue;">**5f6e7d8c**</mark>\
da4670bae10db93a46fad64b14d091189ca<mark style="color:blue;">**5f6e7d8c**</mark>963f2f2d635b4f2a812685f828da7adb7de554b4090363931bde43a6042eafd6197fbf4866191c9e845ae615393f941f4bb9f92b7ed537c2fb7
{% endhint %}

_**Note:**_ \
_When passing the Nonce to Ethereum related functions like Ethereum libraries it is often necessary to add 0x at the begining of the Nonce. For instance here it would beb 0x89abcdef_ _and 0x5f6e7d8c._
