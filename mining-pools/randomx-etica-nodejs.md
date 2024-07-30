---
description: A package that allows to verify Etica RandomX Nonces in nodejs context
---

# etica-randomx-nodejs package

This package allows to verify Etica RandomX nonces.

To install randomx-etica-nodejs simply run:

```
npm install randomx-etica-nodejs
```

{% hint style="info" %}
If the package doesn't work on your environment ou might need to build it yourself
{% endhint %}

_Here is how to build the package yourself:_

```
git clone https://github.com/etica/randomx-etica-nodejs.git
cd src
go build -buildmode=c-shared -o librandomx_wrapper.so randomx_wrapper.go etica_verification.go
cd ..
npm install
node example_usage.js
```

