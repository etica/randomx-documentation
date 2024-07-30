---
description: everything you need to know about how to handle target and difficulty
---

# Target

Here is an example of the whole process to go from a difficulty to the corresponding target to send to miners.

#### 1. Convert difficulty to target:

Here is a code example of how to convvert a difficulty to target\
For instance a difficulty of 50000 would return a target of 2315841784746323908471419700173758157065399693312811280789151680158262592

```javascript
static getTargetFromDifficulty(difficulty)
    {

      console.log(' getTargetFromDifficulty() difficulty:', web3utils.toBN( difficulty).toString());
    
      var max_target = web3utils.toBN('2').pow(web3utils.toBN('256')).sub(web3utils.toBN('1'));

      const difficultyBN = web3utils.toBN(difficulty);
 
      var current_target = max_target.div(difficultyBN);

      console.log(' getTargetFromDifficulty() current_target:',  current_target.toString());
 
      return current_target ;
    } 
```

#### 2. Then convert target in hex format:

```
this.target = '0x' +web3utils.toBN(valueToConvert).toString('hex');
```

#### 3. Convert target to miner expected format:

```javascript
function convertTargetToCompact(target) {
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
```

Example in code use:

```javascript
Example how to send compactTarget to a miner:
sendNewJob(increaseJobId=true) {
        console.log('--------------- SENDING NEW JOB ---------------------')
        const compactTarget = randomxHelper.convertTargetToCompact(this.target);
        const randomxBlobRaw = randomxHelper.convertToRawWithout0x(this.randomxBlobWithReservedHash);
        const randomxSeedhashRaw = randomxHelper.convertToRawWithout0x(this.randomxSeedhash);
        if (increaseJobId) {
            this.jobId += 1
        }
        this.sendInteraction(new AskNewJob(randomxBlobRaw,
            randomxSeedhashRaw,
            compactTarget,
            this.epochCount,
            this.jobId
        ));
    }
```
