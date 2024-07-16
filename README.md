# 1. Smart Contract Update: Withdrawal Functions (Presale/contracts/Pool.sol)
## Objective
Enhance the security and flexibility of the emergencyWithdrawContribution and withdrawRemainedCRO functions in the smart contract.

## Original Functions
```
function emergencyWithdrawContribution() external {
    uint256 amount = contributionOf[msg.sender];
    payable(msg.sender).transfer(amount);
}

function withdrawRemainedCRO() external {
    require(poolState == PoolState.cancelled);
    require(factory == msg.sender, "Not allowed");
    uint256 amount = address(this).balance;
    payable(governance).transfer(amount);
}
```
# Changes Implemented
## 1. Function: emergencyWithdrawContribution
* Addition of a zero-check for the contribution amount:
  Ensure that the contributor has a non-zero contribution before allowing withdrawal.

* Updated Code:
```
function emergencyWithdrawContribution() external {
    uint256 amount = contributionOf[msg.sender];
    require(amount > 0);
    payable(msg.sender).transfer(amount);
}
```
## 2. Function: withdrawRemainedCRO
* Addition of factoryOwner parameter:
  Allow the function to transfer the remaining balance to a specified factoryOwner address rather than the fixed governance address.

* Updated Code:
```
function withdrawRemainedCRO(address factoryOwner) external {
    require(poolState == PoolState.cancelled, "Pool is not cancelled");
    require(factory == msg.sender, "Not allowed");
    uint256 amount = address(this).balance;
    payable(factoryOwner).transfer(amount);
}
```
## Summary of Changes
* emergencyWithdrawContribution: Added a require statement to check that the amount to be withdrawn is greater than zero. This prevents unnecessary transactions and potential errors when contributors have zero contributions.
* withdrawRemainedCRO: Modified the function to accept an address parameter (factoryOwner) for more flexible and dynamic withdrawal of the remaining CRO balance. This allows specifying the recipient address at the time of withdrawal instead of using a fixed address.

# 2. Smart Contract Update: Withdrawal Functions (Presale/contracts/Pool.sol)
Objective
Enhance the security and flexibility of the emergencyWithdrawContribution in the smart contract.
```
  function emergencyWithdrawContribution() external {
        uint256 amount = contributionOf[msg.sender];
        require(amount > 0);
        payable(msg.sender).transfer(amount);
    }
```
# Changes Implemented
## Reduction of totalRaised and Reset of contributionOf:
After ensuring a non-zero contribution, deduct the contribution amount from totalRaised and reset the contributor's amount to zero.
* Updated Code:
```
function emergencyWithdrawContribution() external {
        uint256 amount = contributionOf[msg.sender];
        require(amount > 0);
        totalRaised -=  contributionOf[msg.sender];
        contributionOf[msg.sender] = 0;
        payable(msg.sender).transfer(amount);
}
```
