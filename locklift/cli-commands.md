---
description: 'This command initializes new Locklift project, filled with samples:'
---

# 💬 CLI commands

### Initialize Locklift package

```bash
npx locklift init --path amazing-locklift-project
# New Locklift project initialized in amazing-locklift-project
```

This command initializes a new Locklift project, filled with samples:

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

#### Other flags

`-f, --force` - force run the init command (in case you have any files in the target directory);

### Build contracts

This command uses the specified TON Solidity compiler and TVM linker to build all project contracts.

```bash
npx locklift build
```

Output

```
Found 1 sources
Building contracts/Sample.sol
Compiled contracts/Sample.sol
Linked contracts/Sample.sol
```

### Test contracts

This command runs the project Mocha tests, `test` folder by default. The `locklift` object will be set up and included automatically, you don't need to import it manually.

```bash
npx locklift test --network local
```

Output

```
  Test Sample contract
    Contracts
      ✓ Load contract factory
      ✓ Deploy contract (1491ms)
      ✓ Interact with contract (1110ms)


  3 passing (3s)
```
