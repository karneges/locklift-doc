# 🧪 Testing contracts

This guide explains our recommended approach for testing contracts in locklift. It relies on [everscale-inpage-provider](https://www.npmjs.com/package/everscale-inpage-provider) to connect to [local-node](https://github.com/tonlabs/evernode-se) and on [Mocha](https://mochajs.org/) and [Chai](https://www.chaijs.com/) for the tests. It also uses our custom Chai matchers to make it easier to write clean test code.&#x20;

#### Initial setup <a href="#initial-setup" id="initial-setup"></a>

In this guide we’ll write some tests for the sample project. If you haven’t done it yet, go and [initialize it](../locklift/getting-started.md#initialize-locklift-project).

We recommend you [use TypeScript](https://hardhat.org/hardhat-runner/docs/guides/typescript) to get better autocompletion and catch possible errors earlier. This guide will assume you are using TypeScript

The setup includes some example tests in the `test/sample-test`.`ts` file, but ignore them for now. Instead, create a `test/my-tests.ts` file. During this guide we'll only run those, by running `npx locklift test -n local --tests test/my-tests.ts`, instead of just `npx locklift test -n local`.

#### A simple test <a href="#a-simple-test" id="a-simple-test"></a>

In the following sections we'll write some tests for the `Sample`contract that comes with the sample project. If you haven't read it yet, please take a look at the `contracts/Sample.sol` file.

For our first test we’ll deploy the `Sample` contract and assert that the `state` by the `getDetails()` getter is the same one that we passed in the constructor:

```typescript
/import { expect } from "chai";
import { Contract, Signer, toNano } from "locklift";
import { FactorySource } from "../build/factorySource";

let signer: Signer;
// define the sample variable for using it in multiple test cases
let sample: Contract<FactorySource["Sample"]>;
describe("Test Sample contract", async function () {
  before(async () => {
    signer = (await locklift.keystore.getSigner("0"))!;
  });
  describe("Contracts", async function () {
    it("Deploy contract", async function () {
      const INIT_STATE = 0;
      // Deploy Sample contract
      const { contract } = await locklift.factory.deployContract({
        contract: "Sample",
        publicKey: signer.publicKey,
        initParams: {
          _nonce: locklift.utils.getRandomNonce(),
        },
        // With the initial state that equals 0
        constructorParams: {
          _state: INIT_STATE,
        },
        // Initial account balance
        value: locklift.utils.toNano(2),
      });
      // put deployed contract to variable sample
      sample = contract;
       // check the contract balance
      expect(await locklift.provider.getBalance(sample.address)
        .then(balance => Number(balance))).to.be.above(0);
      
      // check our contract details
      expect(
        await sample.methods
          .getDetails()
          .call()
          .then(({ _state }) => _state),
      ).to.be.equal(INIT_STATE.toString(),"The state should be equal to passed to the constructor");
    }); 
  });
});
```

We deploy the contract itself: we deploy a  `Sample` contract, passing the `_state` as its constructor argument. We also pass an object with `initParams` (static contract fields) and value that will be sent to the calculated address.

Finally, we check that the value returned by the `getDetails()` getter in the contract matches the value that we used when we deployed it. Since all the functions on a contract are async, we have to use the `await` keyword to get its value; otherwise, we would be comparing a promise with a number and this would always fail.

#### Upgrade simple test with tracing features

In the following section, we are going to write a new test case for changing our contract `state`

{% hint style="info" %}
we are going to use code from the previous section
{% endhint %}

```typescript
    it("should state be updated", async () => {
      const NEW_STATE = 12;
      /*
        we are using locklift.tracing.trace
        for having an access to the full transaction evaluating tree
      */ 
      const { traceTree } = await locklift.tracing.trace(
        sample.methods
          .setState({
            _state: NEW_STATE,
          })
          .sendExternal({
            publicKey: signer.publicKey,
          }),
      );
      // check if the method has been called
      expect(traceTree).to.have.call("setState").withNamedArgs({
        _state: NEW_STATE.toString(),
      });
      // check if the event has been emitted
      expect(traceTree).to.emit("StateChange").count(1).withNamedArgs({
        _state: NEW_STATE.toString(),
      });
      // make a beautyPrint for transaction tree
      console.log(await traceTree?.beautyPrint());
    })
```

We updated the contract `state` field and then we tested it. First of all, we wrapped our transaction to the `locklift.tracing.trace` that gives us allow to the transaction tree. Output of the `trace` function is `transaction`(that didn't be used) and the `traceTree` object. We passed `traceTree` to the `expect` function, after it we used our specific `chai` matchers for testing contract calls and events with particular parameters. Then we printed out our transaction tree to the console:

```bash
CALL Sample.setState{valueReceive: 0,valueSent: 0, rest: -0.009391⮯, totalFees: 0.009391}(_state="12")
 EVENT Sample.StateChange(_state="12")

```

&#x20;

