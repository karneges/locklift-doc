---
description: >-
  The tracing module scans the message tree, determines which contracts have
  been deployed, and decodes all method calls. In case of an error in some
  section of the execution graph, tracing will show th
---

# 🚅 Tracing

{% hint style="info" %}
_**Note: If you want to use tracing be sure to provide a tracing endpoint to the config that supports graphql**_
{% endhint %}



```typescript
// trace deploy
const {contract: deployedContractInstance, tx} = await locklift.tracing.trace(locklift.factory.deployContract(...))
// trace simple transaction
const changeStateTransaction = await locklift.tracing.trace(MyContract.methods.changeCounterState({newState: 10}).sendExternal({publicKey: signer.publicKey}))
// trace runTarget
const accountTransaction = await locklift.tracing.trace(myAccount.runTarget(...))
```
