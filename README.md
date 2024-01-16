# Hardhat-Intro-Workshop
A repository used as a workshop to learn the basics of Hardhat and smart contract development

![Hardhat development environment wallpaper](https://miro.medium.com/v2/resize:fit:1200/1*Fe-twcLBfV79OGtQ1vVkkQ.png)

## Getting Started

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

## Installation + Bootstrapping our repository

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
