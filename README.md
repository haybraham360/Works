# Works
pragma solidity ^0.5.0;

// This is a simple payment platform contract that allows users to
// deposit and withdraw funds, as well as transfer funds to other users.

// The address of the contract is also used as the address of the payment platform.
// This means that users can send funds directly to the contract, and the contract
// will automatically credit the user's account.

// The contract maintains a mapping of user accounts, where each account is
// identified by its address and contains the account's balance.

contract PaymentPlatform {
  // Mapping of user accounts, where each account is identified by its address
  // and contains the account's balance.
  mapping(address => uint) public accounts;

  // Event that is emitted whenever a user makes a deposit or withdraws funds.
  event Deposit(address indexed account, uint amount);

  // Event that is emitted whenever a user transfers funds to another user.
  event Transfer(
    address indexed from,
    address indexed to,
    uint amount
  );

  // Fallback function that is automatically called when the contract receives
  // funds from a user. This allows users to deposit funds by simply sending
  // a transaction to the contract's address.
  function() external payable {
    // Get the address of the user who sent the funds
    address sender = msg.sender;

    // Credit the user's account with the amount of the deposit
    accounts[sender] += msg.value;

    // Emit a Deposit event to record the transaction
    emit Deposit(sender, msg.value);
  }

  // Function to allow users to withdraw funds from their account.
  function withdraw(uint amount) public {
    // Get the address of the user who called the function
    address sender = msg.sender;

    // Ensure that the user has enough funds in their account to cover the withdrawal
    require(accounts[sender] >= amount, "Insufficient funds");

    // Send the funds to the user by calling the transfer function on their address
    sender.transfer(amount);

    // Deduct the withdrawn amount from the user's account
    accounts[sender] -= amount;

    // Emit a Deposit event to record the transaction
    emit Deposit(sender, amount * -1);
  }

  // Function to allow users to transfer funds to another user.
  function transfer(address recipient, uint amount) public {
    // Get the address of the user who called the function
    address sender = msg.sender;

    // Ensure that the user has enough funds in their account to cover the transfer
    require(accounts[sender] >= amount, "Insufficient funds");

    // Transfer the funds from the sender's account to the recipient's account
    accounts[recipient] += amount;
    accounts[sender] -= amount;

    // Emit a Transfer event to record the transaction
    emit Transfer(sender, recipient, amount);
  }
}
