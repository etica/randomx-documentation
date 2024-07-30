---
description: How to call mintrandomX(), the function to call mint ETI
---

# call mintrandomX()

#### 1. Inputs

Here is the list of inputs you will need to call mintrandomX()



{% hint style="info" %}
For reminder here is how to retrieve information from Etica Smart Contract

[read-etica-smart-contract.md](read-etica-smart-contract.md "mention")
{% endhint %}

```javascript
nonce: nonce submited by miner
blockHeader: current generic randomxBlob
currentChallenge: current challengeNumber
randomxHash: randomx hash result submited by miner 
claimedTarget: must be a target at least lower than current Network target
seedHash: current seedHash
extraNonce: the miner extraNonce that was used to create the miner's blob
```

#### Call example



```javascript
var mintMethod = tokenContract.methods.mintrandomX( nonce, randomx_blob, challenge_number, randomxhash, claimedtarget, randomx_seedhash, extraNonce )

try{
    var txCount = await web3.eth.getTransactionCount(addressFrom);
   } catch(error) {
     return error;
   }

// make sure that are in 0x format:
const _nonce = randomxHelper.convertAdd0x(nonce);
const _extraNonce = randomxHelper.convertAdd0x(extraNonce);

var txData = web3.eth.abi.encodeFunctionCall({
            name: 'mintrandomX',
            type: 'function',
            inputs: [{
                type: 'bytes4',
                name: 'nonce'
            },{
                type: 'bytes',
                name: 'blockHeader'
            },{
              type: 'bytes32',
              name: 'currentChallenge'
            },{
              type: 'bytes',
              name: 'randomxHash'
            },{
              type: 'uint256',
              name: 'claimedTarget'
            },{
              type: 'bytes',
              name: 'seedHash'
            },
            {
              type: 'bytes8',
              name: 'extraNonce'
            }
           ]
        }, [_nonce, randomx_blob, challenge_number, randomxhash, claimedtarget, randomx_seedhash, _extraNonce ]);
        
    // Use fixed gas cost values (these are random values that work but they can be optimised):    
    var max_gas_cost = 999000;
    var estimatedGasCost = 300000;
    const txOptions = {
      nonce: web3utils.toHex(txCount),
      gas: web3utils.toHex(estimatedGasCost),
      gasPrice: 200000000000,
      value: 0,
      to: addressTo,
      from: addressFrom,
      data: txData
    }
    
    try{
      console.log('-------------- SUBMITING MINING TRANSACTION a4------------');
      txResult= await TransactionHelper.sendSignedRawTransactionSimple(web3,txOptions,addressFrom,privateKey);      
      return {success:true,txResult: txResult}
    } catch(error){
    //handle error, 
    // If there is an issue it means the node doesn't accept the nonce, it can be an invalid nonce or target
    // It is also possible it is due to normal tx failures like not enough gas
    // If you're sure of the nonce validity, you can implement an auto-retry submit with different gas values
    
    }
    
    
    static async sendSignedRawTransactionSimple(web3,txOptions,addressFrom,pKey){
          
         
   
          let signedTx = await new Promise((resolve, reject) => {
          web3.eth.accounts.signTransaction(txOptions, pKey, function (error, signedTx) {
            if (error) {
               console.log(error);
            // handle error
               reject(error)
            } else {
                resolve(signedTx)
            }
    
          })
        });
    
    
    
          let submittedTx = await new Promise((resolve, reject) => {
            web3.eth.sendSignedTransaction(signedTx.rawTransaction)
                .on('transactionHash', (txHash) => {
                  console.log('on tx hash: ', txHash)
                  resolve({success:true, txHash: txHash})
                })
                .on('error', (reason) => {
                  console.log('error tx rejected by node');
                  console.log('error tx rejected by node, reason is: ', reason);
                  reject({success:false, reason: reason})
                })
     
          })
    
            
            console.log('submittedTx',submittedTx)
    
              return submittedTx
    
    
      }

```



{% hint style="info" %}
<mark style="color:red;">**Important**</mark>\
The implementation of mintrandomX() doesn't allow to call _**estimateGas()**_ cost, it needs to be called directly. For instance following estimateGas cannot be called for mintrandomX():

`estimatedGasCost = await mintMethod.estimateGas({gas: max_gas_cost, gasPrice:1000000000, from:addressFrom, to:addressTo });`
{% endhint %}

