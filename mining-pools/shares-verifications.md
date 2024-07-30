---
description: Everything you need to know about the process to verify nonces sent by miners
---

# Shares verification

Here is the full process to verify nonces sent by miners

#### 1. Import randomx-etica-node.js

Full details of how to install randomx-etica-nodejs here: [randomx-etica-nodejs.md](../code-implementation/randomx-etica-nodejs.md "mention")

```
import randomx from "randomx-etica-nodejs";
```

#### 2. Get nonce and from result from miner submit request:

```javascript
var nonce = interaction.params.nonce;
var hashresult = interaction.params.result;
```

#### 3. Load miner's Blob

The miner should have a unique blob specific for this miner. For nonce verification you need to load this minerBlob. [reserved-space.md](../basics/reserved-space.md "mention")\
Then convert minerBlob to 0x format like this:

```javascript
const minerBlob = randomxHelper.convertToRawWithout0x(this.randomxBlobWithReservedHash);
(randomxHelper functions details below at bottom of the page)
```

#### 4. Finally, convert inputs data in right format

```
const nonceBuffer = Buffer.from(nonce, 'hex');
const hashresultBuffer = Buffer.from(hashresult, 'hex');
const targetBigNumber = new BigNumber(this.target);
const targetBuffer = Buffer.from(targetBigNumber.toString(16).padStart(64, '0'), 'hex');
const seedHash = randomxHelper.convertToRawWithout0x(this.randomxSeedhash);
(randomxHelper functions details below at bottom of the page)
```

{% hint style="info" %}
_Check_ [read-etica-smart-contract.md](read-etica-smart-contract.md "mention") _section for how to retrieve the variables data from Etica Smart Contract (your system should have these variables available as vars for checks)_
{% endhint %}

#### 4. Verify Nonce

```
const isValid = randomx.VerifyEticaRandomXNonce(minerBlob, nonceBuffer, targetBuffer, seedHash, hashresultBuffer);

if(!isValid){           
     // invalid nonce do your logic
     console.log('invalid nonce');
}
else {
     // valid nonce do your logic
     console.log('valid nonce');
}
```

***

{% hint style="info" %}
Here is randomxHelper.js for details about what the mentioned randomxHelper functions like convertWithout0x() do
{% endhint %}

```
randomxHelper.js:

import web3utils from 'web3-utils'


export function convertWithout0x(_data){
    return _data.startsWith('0x') ? _data.slice(2) : _data;
}

export function convertAdd0x(_data){
    if (typeof _data !== 'string') {
        throw new Error('Input data must be a string');
    }
    if (_data.startsWith('0x')) {
        return _data;
    }
    const result = '0x' + _data;
    return result;
}

export function convertToRawWithout0x(_data) {
    // Check if _data is null or undefined
    if (_data == null) {
        console.warn('convertToRawWithout0x: Input data is null or undefined');
        return '';
    }

    // Ensure _data is a string
    if (typeof _data !== 'string') {
        console.warn('convertToRawWithout0x: Input is not a string, attempting to convert');
        try {
            _data = String(_data);
        } catch (error) {
            console.error('convertToRawWithout0x: Failed to convert input to string', error);
            return '';
        }
    }

    // Trim any whitespace
    _data = _data.trim();

    // Check if the string is empty after trimming
    if (_data === '') {
        console.warn('convertToRawWithout0x: Input string is empty');
        return '';
    }

    // Remove '0x' prefix if it exists
    return _data.toLowerCase().startsWith('0x') ? _data.slice(2) : _data;
}


export function convertTargetToCompact(target) {
    // Remove '0x' prefix if present
    target = target.startsWith('0x') ? target.slice(2) : target;
    
    // Convert target to BigNumber
    const targetBN = new web3utils.BN(target, 16);
    
    // Calculate difficulty: (2^256 - 1) / target
    const maxTarget = new web3utils.BN(2).pow(new web3utils.BN(256)).sub(new web3utils.BN(1));
    const difficulty = maxTarget.div(targetBN);
    
    // Calculate 32-bit representation: ((2^256 - 1) / difficulty) >> 224
    const compactTarget = maxTarget.div(difficulty).shrn(224);
    
    // Convert to hexadecimal and pad to 8 characters
    let compactHex = compactTarget.toString(16).padStart(8, '0');
    
    // Swap endianness
    compactHex = compactHex.match(/.{2}/g).reverse().join('');
    
    return compactHex;
} 


export function setReservedHashOnBlob(blockHeader, poolAddress, challengeNumber, extraNonce){

    console.log('-------------------  setting reservedHash -------------------');

    const reservedOffset = 55;
    const reservedSpaceSize = 8; // 8 bytes

    console.log('extraNonce is: ', extraNonce);

    console.log('this.poolAddress is:', poolAddress);
    console.log('this.challengeNumber is:', challengeNumber);

    console.log('typeof this.poolAddress is:', typeof poolAddress);
    console.log('typeof this.challengeNumber is:', typeof challengeNumber);

    var extraNonceHash = web3utils.soliditySha3( poolAddress, extraNonce, challengeNumber );
        console.log('extraNonceHash: ', extraNonceHash);

        // Truncate extraNonceHash to 8 bytes
        const truncatedExtraNonceHash = Buffer.from(extraNonceHash.slice(2), 'hex').slice(0, reservedSpaceSize);
        console.log('truncatedExtraNonceHash:', truncatedExtraNonceHash.toString('hex'));

        // Ensure blockHeader is a Buffer
        if (!Buffer.isBuffer(blockHeader)) {
            if (typeof blockHeader === 'string') {
                // If it's a string, assume it's hex (with or without '0x' prefix)
                blockHeader = blockHeader.startsWith('0x') ? blockHeader.slice(2) : blockHeader;
                blockHeader = Buffer.from(blockHeader, 'hex');      
            } else {
                // If it's not buffer or string, throw an error
                throw new Error(`Unsupported blockHeader type: ${typeof blockHeader}`);
            }
        }

        // Validate the resulting Buffer
        if (!Buffer.isBuffer(blockHeader) || blockHeader.length === 0) {
            throw new Error('Failed to convert blockHeader to a valid Buffer');
        }

        console.log('blockHeader before inserting truncatedExtraNonceHash:', blockHeader.toString('hex'));

        // Insert truncatedExtraNonceHash into blockHeader at offset 55, blockHeader With ReservedHash set at offset 55
        var updatedblockHeader = Buffer.concat([
            blockHeader.slice(0, reservedOffset),
            truncatedExtraNonceHash,
            blockHeader.slice(reservedOffset + reservedSpaceSize)
        ]);

        console.log('blockHeader after inserting truncatedExtraNonceHash:', updatedblockHeader.toString('hex'));

        return updatedblockHeader.toString('hex');

}
```
