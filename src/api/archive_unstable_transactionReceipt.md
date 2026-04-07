# archive_unstable_transactionReceipt

**Parameters**:

- `transactionBytes`: String containing the hexadecimal-encoded SCALE-encoded bytes of the transaction to locate. Note: transaction hashes are not globally unique, so the raw bytes are used to identify a specific transaction.
- `count`: An optional positive integer. Maximum number of results to return. If omitted, a server-defined default limit applies. Servers must enforce an upper bound on this value to prevent DoS.

**Return value**: Array of `TransactionLocation` objects (possibly empty) representing every known occurrence of the given transaction across all known blocks.

If no block containing this transaction is found, an empty array is returned.

Each item in the array is a JSON object with the following structure:

```json
{
    "blockHash": "0x...",
    "transactionIndex": 0,
    "status": "finalized",
    "log": "0x..."
}
```

Where:

- `blockHash` (string): Hexadecimal-encoded hash of the block in which this transaction was found.
- `transactionIndex` (integer): Zero-based index of this transaction within the block body.
- `status` (string): Chain placement status of the block containing the transaction. One of:
  - `"finalized"`: The block is part of the finalized canonical chain.
  - `"best"`: The block is on the current best chain but not yet finalized.
  - `"fork"`: The block is on a non-best fork and not finalized.
- `log` (string): Hexadecimal-encoded SCALE-encoded raw event data associated with this extrinsic as stored in the block. This is provided as an uninterpreted data fact; consumers wishing to determine execution outcome should decode this against the relevant runtime metadata.

**Notes**:

- Because the same transaction bytes can legitimately appear in more than one block (e.g. across competing forks, or after an account is re-funded and re-submits an identical extrinsic), this method always returns an array and callers must handle multiple results.
- The `log` field contains raw on-chain event bytes. Interpretation of whether a transaction "succeeded" or "failed" is left to the caller, as it is runtime- and call-pattern-specific (e.g. proxy calls may not surface `ExtrinsicFailed` even on inner failure).
- The `count` limit exists to bound server-side work. Callers needing more results should query with progressively higher `count` values or use block-level methods (`archive_v1_body`, `archive_v1_storage`) for exhaustive scanning.
- This is an unstable function; its API may change before promotion to `archive_v2`.
