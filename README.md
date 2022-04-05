# PoolTogether TWAB Delegator contest details

- $25,650 USDC main award pot
- $1,350 USDC gas optimization award pot
- Join [C4 Discord](https://discord.gg/code4rena) to register
- Submit findings [using the C4 form](https://code4rena.com/contests/2022-02-pooltogether-twab-delegator-contest/submit)
- [Read our guidelines for more details](https://docs.code4rena.com/roles/wardens)
- Starts February 22, 2022 00:00 UTC
- Ends February 24, 2022 23:59 UTC

# Resources

- [Code on Github](https://github.com/pooltogether/v4-twab-delegator/commit/2b6d42506187dd7096043e2dfec65fa06ab18577)
- [Documentation](https://dev.pooltogether.com/protocol/contracts/v4-twab-delegator/)

# Contest Scope

This is a contest to evaluate the new TWAB Delegator contract for PoolTogether V4. This contract allows accounts to easily delegate their chance to win. This contract allows users to delegate a *portion* of their tickets to another user. For more details [read the documentation](https://dev.pooltogether.com/protocol/contracts/v4-twab-delegator/).

The TWAB Delegator contract is the top-level contract: all interactions occur with this contract. It has two superclasses; the LowLevelDelegator contract and the PermitAndMulticall contract. The TWAB Delegator contract also acts as a contract factory for Delegation contracts. The Delegation contracts are deployed as a minimal proxy via CREATE2 and actually hold the PoolTogether V4 tickets.

| Contract Name | Source Lines of Code | Libraries | External Calls |
| ------------- | -------------------- | ---------- | -------------- |
| [TWABDelegator.sol](https://github.com/pooltogether/v4-twab-delegator/blob/2b6d42506187dd7096043e2dfec65fa06ab18577/contracts/TWABDelegator.sol) | ~300 sLoC | OpenZeppelin Contracts | PoolTogether V4 Ticket contract
| [PermitAndMulticall.sol](https://github.com/pooltogether/v4-twab-delegator/blob/2b6d42506187dd7096043e2dfec65fa06ab18577/contracts/PermitAndMulticall.sol) | ~40 sLoC | OpenZeppelin Contracts | PoolTogether V4 Ticket contract
| [LowLevelDelegator.sol](https://github.com/pooltogether/v4-twab-delegator/blob/2b6d42506187dd7096043e2dfec65fa06ab18577/contracts/LowLevelDelegator.sol) | ~35 sLoC | OpenZeppelin Contracts | None |
| [Delegation.sol](https://github.com/pooltogether/v4-twab-delegator/blob/2b6d42506187dd7096043e2dfec65fa06ab18577/contracts/Delegation.sol) | ~45 sLoC | None | PoolTogether V4 Ticket contract |

## TWABDelegator

The TWABDelegator allows users to stake tickets on the contract, so it implements an ERC20 token.  It inherits, without modification, the standard OZ ERC20 token implementation.

**Note:** The Ticket contract that the TWAB Delegator is bound to is expected to be a standard PoolTogether V4 ticket contract. This token does not implement fee-on-transfer behaviour, so we've decided to exclude any compatibility with fee-on-transfer tokens.

## PermitAndMulticall

The PermitAndMulticall contract wraps the standard `multicall()` function with a `permitAndMulticall()` function.  This function allows the caller to permit ticket spend and batch multiple actions in one transaction.

## LowLevelDelegator

This contract uses the OZ Clone contract to instantiate minimal proxies instances of the Delegation contract. This allows the caller to transfer tickets to the Delegation contracts and delegate portions of their Tickets.

## Delegation

This is the contract that holds the tickets. It's like a very simple smart contract wallet.

# Areas of Concern

Aside from the standard auditing concerns, such as loss of funds, we want:

- To ensure that only the TWAB Delegator can control the Delegation instances.
- To ensure that the Delegation lock behaves as expected
- The representative should NOT have access to the delegator's funds.  The rep can only delegate.

Of course this list isn't comprehensive, so if you see anything else let us know!
