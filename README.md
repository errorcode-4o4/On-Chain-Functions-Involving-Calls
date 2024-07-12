# On-Chain-Functions-Involving-Calls

## Introduction
Protocol Name: Aave V2 <br />
Category: DeFi <br />
Smart Contract: LendingPoolAddressesProvider <br />

## Function Analysis
Function Name: _delegate <br />
Block Explorer Link: [Etherscan: LendingPool](https://etherscan.io/address/0xb53c1a33016b2dc2ff3653530bff1848a515c8c5#code) <br />

Function Code:
```
function _delegate(address implementation) internal {
    //solium-disable-next-line
    assembly {
      // Copy msg.data. We take full control of memory in this inline assembly
      // block because it will not return to Solidity code. We overwrite the
      // Solidity scratch pad at memory position 0.
      calldatacopy(0, 0, calldatasize())

      // Call the implementation.
      // out and outsize are 0 because we don't know the size yet.
      let result := delegatecall(gas(), implementation, 0, calldatasize(), 0, 0)

      // Copy the returned data.
      returndatacopy(0, 0, returndatasize())

      switch result
        // delegatecall returns 0 on error.
        case 0 {
          revert(0, returndatasize())
        }
        default {
          return(0, returndatasize())
        }
    }
  }
```
<br />
Used Encoding/Decoding or Call Method: delegatecall <br />

## Explanation

Purpose: The _delegate function in the Proxy contract facilitates the delegation of function calls to an implementation contract specified by implementation. It utilizes delegatecall, a low-level operation in Solidity that allows the contract to execute code from another contract while maintaining the context of the caller. <br />

Detailed Usage:

delegatecall: Executes the code of the implementation contract within the context of the Proxy contract. This means that the state variables and storage of the Proxy contract are accessible and modifiable by the implementation contract. <br />
Assembly Operations:
- Copies the calldata (transaction input data) to memory. <br />
- Executes the delegatecall to the implementation contract with the specified gas limit. <br />
- Copies the returned data from the delegatecall to the return data area. <br />
- Handles errors by reverting the transaction if the delegatecall returns 0. <br />

Impact:

- Flexibility: Enables upgradability of the Proxy contract by changing the implementation address to point to upgraded versions without modifying the Proxy contract itself.<br />
- Efficiency: Reduces gas costs compared to traditional contract calls by only requiring a single transaction to the Proxy contract, which then delegates execution to the implementation contract.<br />
- Security Considerations: Requires careful management of the implementation contract to ensure compatibility and security of the delegated functions.






