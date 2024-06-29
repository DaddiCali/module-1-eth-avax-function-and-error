MonthlyAllowanceTracker Contract
Overview
The MonthlyAllowanceTracker contract is a Solidity smart contract designed to track and manage monthly allowances in Ether. It allows the owner to add allowances, spend daily expenses, and update the monthly allowance limit.

Functions
addAllowance
Allows anyone to add Ether to the monthly allowance. Requires the added value to be greater than 0 and 500 Ether or less.

spendDailyAllowance
Allows anyone to spend Ether from the monthly allowance for daily expenses. Requires the spent value to be greater than 0 and within the current monthly allowance.

updateMonthlyAllowance
Allows the contract owner to update the monthly allowance limit to a new value.

getMonthlyAllowance
Returns the current value of the monthly allowance.

// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract MonthlyAllowanceTracker {
    address public owner;
    uint256 public monthlyAllowance;
    uint256 public monthlyCycleStart;

    event AllowanceUpdated(uint256 newAllowance);
    event AllowanceAdded(uint256 addedValue);
    event DailyExpenses(uint256 spentValue);

    constructor() {
        owner = msg.sender;
        monthlyAllowance = 0; // Initialize with zero allowance
        monthlyCycleStart = block.timestamp;
    }

    modifier onlyOwner() {
        require(msg.sender == owner, "Only contract owner can call this function");
        _;
    }

    modifier checkMonthlyCycle() {
        if (block.timestamp >= monthlyCycleStart + 30 days) {
            monthlyAllowance = 0; // Reset allowance at the start of a new cycle
            monthlyCycleStart = block.timestamp;
        }
        _;
    }

    function addAllowance(uint256 _value) public payable onlyOwner checkMonthlyCycle {
        require(_value > 0, "Add Allowance value should be greater than 0");
        require(_value <= 500 ether, "Add Allowance value should be 500 Ether or less");
        monthlyAllowance += _value;
        emit AllowanceAdded(_value);
    }

    function spendDailyAllowance(uint256 _value) public checkMonthlyCycle {
        require(_value > 0, "Daily Expenses value should be greater than 0");
        require(_value <= monthlyAllowance, "Insufficient monthly allowance");
        monthlyAllowance -= _value;
        emit DailyExpenses(_value);

        // Using assert to ensure state variable integrity
        assert(monthlyAllowance >= 0);
    }

    function updateMonthlyAllowance(uint256 newAllowance) public onlyOwner {
        monthlyAllowance = newAllowance;
        emit AllowanceUpdated(newAllowance);

        // Using assert to ensure state variable integrity
        assert(monthlyAllowance == newAllowance);
    }

    function getMonthlyAllowance() public view returns (uint256) {
        return monthlyAllowance;
    }

    function forceRevert() public pure {
        // Using revert to demonstrate its functionality
        revert("This is a forced revert statement");
    }

    // Fallback function to receive Ether
    receive() external payable {}
}


Owner
The owner of the contract is set during deployment and has exclusive access to the updateMonthlyAllowance function.

Owner: Butt Carlsly June D.

License
This project is licensed under the MIT License. See the LICENSE file for details.
