# Hardhat-Intro-Workshop
A repository used as a workshop to learn the basics of Hardhat and smart contract development.

![Hardhat development environment wallpaper](https://miro.medium.com/v2/resize:fit:1200/1*Fe-twcLBfV79OGtQ1vVkkQ.png)

**What you will learn?**

- Introduction

1. Installation + Bootstrapping our Hardhat Project

2. Create a basic Solidity contract: a LSP7 Token + compile it

3. How you deploy a smart contract (= setup a deployment script)

4. How to setup Hardhat and connect it to a custom chain

5. How to verify a contract with Hardhat

6. How to put together / combine different types of LSP7 token extensions (Mintable, Burnable, CappedSupply...)

---

## Introduction

We first need to install Hardhat. But what is Hardhat?

Hardhat is a development environnement that makes it easier to develop any form of _"Ethereum Software"_. By that I mean any form of codebase related to building on Ethereum and on EVM based blockchains more generically.
Hardhat will make your development life easier when you have to develop for instance for the following use casers:
- Solidity smart contracts, including testing them.
- Deployment scripts, to deploy contracts on a staging or production blockchain

In a nutshell, Hardhat will help in doing the 4 following things:
- Editing
- Compiling
- Debugging
- Deploying

## 1. Installation + Bootstrapping our Hardhat Project

Go inside the folder of your choice (working directory) and create a new folder for your working project:

```bash
mkdir Hardhat-Project && cd Hardhat-Project
``` 

We will then create an empty npm project with a `package.json`. Run the command below and follow the instructions.

```
npm init
```

Let's now add Hardhat as a developer dependency inside our project,

```bash
npm install --save-dev hardhat
```

And create a new Hardhat project by running the following command:

```bash
npx hardhat init
```

> **Note:** make sure you say yes when Hardhat asks you to install `@nomiclabs/hardhat-toolbox`. This dependency contains all the tools that you will need!

## 2. Create a basic Solidity contract: a LSP7 Token + compile it

### 2.1 First lines of Solidity code

To create a LSP7 token contract, we will need to install the following Solidity library:

```bash
npm i @lukso/lsp-smart-contracts
```

Once you have installed the library, open the `contracts/` folder, and create a new file `MyToken.sol`. We will start writing the basic Solidity code for the Token contract:

```js
// SPDX-License-Identifier: UNLICENSED
pragma solidity 0.8.20;

contract MyToken {
    // smart contract logic will go here...
}
```

### 2.2 Compiling the contract + debugging

At this stage, let's try to compile our smart contract. In Hardhat you can do so by running the following command:

```bash
npx hardhat compile
```

At this stage, you should very likely get the following error:

```log
Error HH606: The project cannot be compiled, see reasons below.

The Solidity version pragma statement in these files doesn't match any of the configured compilers in your config. Change the pragma or configure additional compiler versions in your hardhat config.

  * contracts/MyToken.sol (0.8.20)

To learn more, run the command again with --verbose

Read about compiler configuration at https://hardhat.org/config


For more info go to https://hardhat.org/HH606 or run Hardhat with --show-stack-traces
```

> Note the available options `--verbose` and `--show-stack-traces` from the console error output. Running the command again with these flags can provide you more valuable insights and will help you in the future to debug!

The error here is the fact that the **Solidity version pragma in `MyToken.sol` does not match the configuration from Hardhat. We can change this by:
- **option 1:** either modify the pragma statement in the Solidity file to match our Hardhat
- **option 2:** or modify our Hardhat development configs for the Solidity compiler version.

### Hardhat configs

Let's choose option 2 to learn about a new important file that you will use often: **`hardhat.config.ts`**

In this file, change the config to the same pragma version as your `MyToken.sol` file.

```ts
import { HardhatUserConfig } from "hardhat/config";
import "@nomicfoundation/hardhat-toolbox";

const config: HardhatUserConfig = {
  solidity: "0.8.20",
};

export default config;
```

After saving the changes, try to re-compile and it should compile successfully! 🎉

## Implementing LSP7 in our `MyToken` contract

Our smart contract so far has no functionality. Let's make it work by making it as a LSP7 Digital Asset, a fungible token from the LUKSO LSP Standards.

We will use a preset contract called `LSP7Mintable` to get started with usable contract that can be deployed and that enables the contract owner to mint.

### Importing contracts from external library

In order to implement LSP7, we will import the functionality from the `@lukso/lsp-smart-contracts` library. Like in Javascript and ESM, we can do so by using an `import` statement with the path of the module we want to import as follow:

```js
import "@lukso/lsp-smart-contracts/contracts/LSP7DigitalAsset/presets/LSP7Mintable.sol";
```

Let try compile our modified contract. You should get the following error:

```log
Error HH411: The library @lukso/lsp-smart-contracts, imported from contracts/MyToken.sol, is not installed. Try installing it using npm.

For more info go to https://hardhat.org/HH411 or run Hardhat with --show-stack-traces
```

Let's follow the instructions from the error message and install this library as a dependency in our project:

```bash
npm install @lukso/lsp-smart-contracts
```

After installing the library, try to compile again and it should work! 🎉

Also in Solidity like in Javascript, it is often more preferable to import specific symbols and part of the file, instead of the whole file/module for clarity and to avoid potential name collisions between imported modules. We will re-write our import statement as follow:

```js
import {LSP7Mintable} from "@lukso/lsp-smart-contracts/contracts/LSP7DigitalAsset/presets/LSP7Mintable.sol";
```

### Add LSP7 to our Token

The Solidity documentations describes the Solidity language as a _"Contract Oriented Language"_, meaning its syntax works similarly as Object Oriented Programming languages, with the exceptions that instead of classes, the objects that can be created represent smart contracts.

Similarly to Object Oriented programming, contracts have inheritance. This is how complex contracts like Universal Profile, Digital Assets and NFT 2.0 can be created: through multiple differents independent modules (LSP4 Metadata, LSP14 Ownable 2 Steps, ERC725Y, etc...) that the final contract to deploy will inherit.

To implement our `LSP7Mintable` in our token, we will add it into the inheritance. This can be done by extending the contract using the `is` keyword.

```js
// SPDX-License-Identifier: UNLICENSED
pragma solidity 0.8.20;

import {LSP7Mintable} from "@lukso/lsp-smart-contracts/contracts/LSP7DigitalAsset/presets/LSP7Mintable.sol";

contract MyToken is LSP7Mintable {
    // smart contract logic will go here...
} 
```

If you try to compile this, you will get a new error.

```log
No arguments passed to the base constructor. Specify the arguments or mark "MyToken" as abstract.
```

_TODO: explain about `constructor`_

## 4. Connect Hardhat to a custom network / chain

> Reference: https://hardhat.org/tutorial/deploying-to-a-live-network#_7-deploying-to-a-live-network

In your `hardhat.config.ts`, setup a new network as follow:

```ts
module.exports = {
  solidity: "0.8.23",
  networks: {
    sepolia: {
      url: `https://rpc.testnet.lukso.network`,
      accounts: [TESTNET_PRIVATE_KEY]
    }
  }
};
```

## 5. Verify a deployed contract

TBD
