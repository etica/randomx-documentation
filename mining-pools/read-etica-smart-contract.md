---
description: How to read Etica Smart Contract
---

# Read Etica Smart Contract

#### Variables

Here is the list of variables from Etica Smart contract that you will need in your system.



**epochCount (ETI Block Height):**

```javascript
var epochCount = await tokenContract.methods.epochCount().call();
```

#### randomxBlob (current generic randomx blob):

```javascript
var randomxBlob = await tokenContract.methods.randomxBlob().call();
```

#### randomxSeedhash (current seedhash)

```javascript
var randomxSeedhash = await tokenContract.methods.randomxSeedhash().call();
```

#### challengeNumber (current challengeNumber)

```javascript
var challengeNumber = await tokenContract.methods.getChallengeNumber().call() ;
```

#### Difficulty (current ETI difficulty):

```javascript
 var miningDifficultyString = await tokenContract.methods.getMiningDifficulty().call()  ;
 var miningDifficulty = parseInt(miningDifficultyString)
```

#### Target (current ETI target):

```javascript
var miningTargetString = await tokenContract.methods.getMiningTarget().call()  ;
var miningTarget = web3utils.toBN(miningTargetString)
```

#### miningReward (current ETI block reward):

```javascript
var miningReward = await tokenContract.methods.getMiningReward().call() ;
```
