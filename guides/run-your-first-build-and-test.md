# 🥇 Run your first build and test

After the initialization project, you can find an initial contract, test, and script&#x20;

```
├── contracts
│   └── Sample.sol
├── locklift.config.ts
├── scripts
│   └── 1-deploy-sample.ts
├── giverSettings
|   └── index.ts
└── test
    └── sample-test.ts
```

### Run build

```
npx locklift build
```

After this command, your contracts will be built and you can find files such as

* Sample.abi.json
* Sample.tvc
* Sample.code
* Sample.code

Also, the `build` folder will be included `factorySource.ts` that included types, this file is being generated every time after `build` and `test` commands

### Run test

```bash
npx locklift test --network local
```

For this command need to specify the network which will be used for testing, network name should be included in the [config file](../locklift/configuration.md)

{% hint style="info" %}
You don't need to build manually, the `test` command will trigger the `build` command to
{% endhint %}

As a result of the test, you will see the output like

````
```
  Test Sample contract
    Contracts
      ✓ Load contract factory
      ✓ Deploy contract (1491ms)
      ✓ Interact with contract (1110ms)


  3 passing (3s)
```
````
