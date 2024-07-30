# Pool/Miners communications

#### Login

```json
Miner to Pool:
{
id: 1,
jsonrpc: '2.0',
method: 'login',
params: {
login: '0x5f64108cCabE790207d09F514DB76F659E9aa8eb',
pass: 'x',
agent: 'XMRig/6.21.3 (Windows NT 10.0; Win64; x64) libuv/1.48.0 msvc/2019',
algo: [
'rx/0',          'cn/2',
'cn/r',          'cn/fast',
'cn/half',       'cn/xao',
'cn/rto',        'cn/rwz',
'cn/zls',        'cn/double',
'cn/ccx',        'cn-lite/1',
'cn-heavy/0',    'cn-heavy/tube',
'cn-heavy/xhv',  'cn-pico',
'cn-pico/tlo',   'cn/upx2',
'cn/1',          'rx/wow',
'rx/arq',        'rx/graft',
'rx/sfx',        'rx/keva',
'argon2/chukwa', 'argon2/chukwav2',
'argon2/ninja',  'ghostrider'
]
}
}

```

```json
Pool response:
{
id: 1,
jsonrpc: '2.0',
result: {
id: '1',
job: {
blob: 'afe2594d58c7a2924a87f456030f650e254f46fd7b86dc816ae4f4fe136f768b9d20b952fa2aa0671fb67149feafecd781c112bc1bc5230a8e1b6d600ad606770fe3576c5403491c40d31eb4657bc560',
job_id: '1',
target: '8b4f0100',
height: 18,
seed_hash: '6c04e936f063050f70b86c024b637335dd48c98bd47803a880e2f3bbdaf09642'
},
status: 'OK'
},
error: null
}

```

{% hint style="info" %}
**how to get the data**

_job\_id:_ must be string can contain letters\
_blob:_ for miner blob construction please read the BASICS sections Blob, Nonce and Reserved Space. [read-etica-smart-contract.md](read-etica-smart-contract.md "mention")

_seed\_hash:_ is a global variable [read-etica-smart-contract.md](read-etica-smart-contract.md "mention")

height: is the current ETI block height [read-etica-smart-contract.md](read-etica-smart-contract.md "mention")

_target:_ it's a target not difficulty, check this page for details [target.md](target.md "mention")
{% endhint %}

#### Submit Share

```
Submit share (Miner to Pool):

{ 
jsonrpc: '2.0', 
method: 'submit', 
params:{
id: '1',
job_id: '1',
nonce: '91d20171',
result: '436f54becf6abc2aba5b7a804464dcce9db0696b7222554b905c977cd6d50000'
}
}

```

{% hint style="info" %}
_nonce:_ the 4bytes nonce solution\
_result:_ the 32bytes randomX hash result
{% endhint %}

```
Pool response:

{
    result: {
        status: "OK"
    },
    id: interactionId
}

```

{% hint style="info" %}
```
where interactionId correspond to the params.id sent by miner
```
{% endhint %}

#### Send Job (Pool to Miner)

```json
Send new job to miner:
{ 
jsonrpc: '2.0', 
method: 'job', 
params:{ blob:'da4670bae10db93a46fad64b14d091189cafa19f61674963f2f2d635b4f2a812685f828da7adb7de554b4090363931bde43a6042eafd6197fbf4866191c9e845ae615393f941f4bb9f92b7ed537c2fb7',  
target: '8b4f0100', 
 job_id: '2', 
seed_hash:'6c04e936f063050f70b86c024b637335dd48c98bd47803a880e2f3bbdaf09642', 
height: 19, 
algo: 'rx/0'
}
}
```

{% hint style="info" %}
**how to get the data**

_job\_id:_ must be string can contain letters\
_blob:_ for miner blob construction please read the BASICS sections Blob, Nonce and Reserved Space. [markdown.md](../basics/markdown.md "mention")

_seed\_hash:_ is a global variable [read-etica-smart-contract.md](read-etica-smart-contract.md "mention")

height: is the current ETI block height [read-etica-smart-contract.md](read-etica-smart-contract.md "mention")

algo: 'rx/0' (it's a zero not O) corresponds to randomX algo
{% endhint %}

