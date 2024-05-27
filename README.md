# SCM-MODULE-2---METAMASK

This Solidity smart contract, named `Assessment`, is designed for managing financial transactions. It provides functionalities for depositing funds and withdrawing funds, ensuring secure and transparent financial interactions.

## Description

This contract demonstrates the usage of `require()`, `assert()`, and `revert()` statements for error handling, ensuring robustness and reliability in financial transactions.

### Requirements

- Smart contract has at least two functions
- Value of the functions from the smart contract are visible on the frontend of the application

### Component and Functionality

`deposit:` A function that allows users to deposit funds into the contract. It takes an amount parameter and adds it to the contract's balance.

`withdraw:` This function enables users to withdraw funds from the contract. It deducts the specified amount from the contract's balance.

`getBalance:` A function that retrieves the current balance of the contract.

### Getting Started
To interact with this contract, you can deploy it on VS Code Studio or any Ethereum-compatible blockchain.

Open VS Code Studio.
Create a new file and paste the provided Solidity code and JavaScript code.
Compile the code.
Deploy the contract.
Interact with the deployed contract by calling its functions.
After cloning the GitHub repository, follow these steps to run the code on your computer:

Inside the project directory, in the terminal, type: npm i.
Open two additional terminals in your VS Code.
In the second terminal, type: npx hardhat node.
In the third terminal, type: npx hardhat run --network localhost scripts/deploy.js.
Back in the first terminal, type npm run dev to launch the front-end.
After this, the project will be running on your localhost, typically at http://localhost:3000/.

index.js
```index.js
import { useState, useEffect } from "react";
import { ethers } from "ethers";
import atm_abi from "../artifacts/contracts/Assessment.sol/Assessment.json";

export default function HomePage() {
  const [ethWallet, setEthWallet] = useState(undefined);
  const [account, setAccount] = useState(undefined);
  const [atm, setATM] = useState(undefined);
  const [balance, setBalance] = useState(undefined);

  const contractAddress = "0x5FbDB2315678afecb367f032d93F642f64180aa3";
  const atmABI = atm_abi.abi;

  const getWallet = async () => {
    if (window.ethereum) {
      setEthWallet(window.ethereum);
    }

    if (ethWallet) {
      const account = await ethWallet.request({ method: "eth_accounts" });
      handleAccount(account);
    }
  };

  const handleAccount = (account) => {
    if (account) {
      console.log("Account connected: ", account);
      setAccount(account);
    } else {
      console.log("No account found");
    }
  };

  const connectAccount = async () => {
    if (!ethWallet) {
      alert("MetaMask wallet is required to connect");
      return;
    }

    const accounts = await ethWallet.request({ method: "eth_requestAccounts" });
    handleAccount(accounts);

    // once wallet is set we can get a reference to our deployed contract
    getATMContract();
  };

  const getATMContract = () => {
    const provider = new ethers.providers.Web3Provider(ethWallet);
    const signer = provider.getSigner();
    const atmContract = new ethers.Contract(contractAddress, atmABI, signer);

    setATM(atmContract);
  };

  const getBalance = async () => {
    if (atm) {
      setBalance((await atm.getBalance()).toNumber());
    }
  };

  const deposit = async () => {
    if (atm) {
      let tx = await atm.deposit(1);
      await tx.wait();
      getBalance();
    }
  };

  const withdraw = async () => {
    if (atm) {
      let tx = await atm.withdraw(1);
      await tx.wait();
      getBalance();
    }
  };

  const initUser = () => {
    // Check to see if user has Metamask
    if (!ethWallet) {
      return <p>Please install Metamask in order to use this ATM.</p>;
    }

    // Check to see if user is connected. If not, connect to their account
    if (!account) {
      return <button onClick={connectAccount}>Please connect to My Metamask wallet</button>;
    }

    if (balance == undefined) {
      getBalance();
    }

    return (
      <div>
        <p>Your Account: {account}</p>
        <p>Your Balance: {balance}</p>
        <button onClick={deposit}>Deposit 1 ETH</button>
        <button onClick={withdraw}>Withdraw 1 ETH</button>
      </div>
    );
  };

  useEffect(() => {
    getWallet();
  }, []);

  return (
    <main className="container">
      <header>
        <h1>Welcome to My Metamask Wallet!</h1>
      </header>
      {initUser()}
      <style jsx>{`
        .container {
          text-align: center;
          background-color:#FDFD96; /* Changed background color to mint green */
          height: 100vh;
          display: flex;
          flex-direction: column;
          justify-content: center;
          align-items: center;
        }
      `}</style>
    </main>
  );
}

```
solidity
```solidity

// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.9;

//import "hardhat/console.sol";

contract WaterRefillingATM {
    address payable public owner;
    uint256 public balance;

    event Deposit(uint256 amount);
    event Withdraw(uint256 amount);

    constructor(uint initBalance) payable {
        owner = payable(msg.sender);
        balance = initBalance;
    }

    function getBalance() public view returns(uint256){
        return balance;
    }

    function deposit(uint256 _amount) public payable {
        uint _previousBalance = balance;

        // make sure this is the owner
        require(msg.sender == owner, "You are not the owner of this account");

        // perform transaction
        balance += _amount;

        // assert transaction completed successfully
        assert(balance == _previousBalance + _amount);

        // emit the event
        emit Deposit(_amount);
    }

    // custom error
    error InsufficientBalance(uint256 balance, uint256 withdrawAmount);

    function pay(uint256 _payAmount) public {
        require(msg.sender == owner, "You are not the owner of this account");
        uint _previousBalance = balance;
        if (balance < _payAmount) {
            revert InsufficientBalance({
                balance: balance,
                withdrawAmount: _payAmount
            });
        }

        // pay in the given amount
        balance -= _payAmount;

        // assert the balance is correct
        assert(balance == (_previousBalance - _payAmountAmount));

        // emit the event
        emit pay(_payAmount);
    }
}


## Authors
Abdulmanan, Joana A. 8213866@ntc.edu.ph
