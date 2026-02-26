
This repo is to get more granual state-related data only from [lab.ethpandaops.io](https://lab.ethpandaops.io/ethereum/execution/state-growth) to inform the size estimates in [this slicing](https://notes.ethereum.org/U9xM4VOPR9isPK7lOZJUQg?view#3-Ethereum-Data)

### State Anatomy

![Ethereum State Diagram](state-diagram.png)

### Field Mapping (as of Feb 26, 2026)

| API Field | Value | What It Measures |
|---|---|---|
| `account_trienode_bytes` | 57.8 GB | All nodes in the account trie (branch + extension + leaf) |
| `account_bytes` | 16.8 GB | Leaf values only — the RLP-encoded `[nonce, balance, storageRoot, codeHash]` across all 365M accounts |
| `storage_trienode_bytes` | 205.6 GB | All nodes across every contract's storage trie |
| `storage_bytes` | 108.9 GB | Storage slot values only (the 256-bit data) across 1.5B slots |
| `contract_code_bytes` | 12.1 GB | Raw bytecode in the code store (not in any trie) |
| `total_bytes` | 275.5 GB | `account_trienode_bytes` + `storage_trienode_bytes` + `contract_code_bytes` |

> **Note:** `total_bytes` is the sum of the three trie/store-level fields. The leaf-only fields (`account_bytes`, `storage_bytes`) are subsets of their respective `*_trienode_bytes` fields, not additive.

### Data Source

All data is fetched from the ethPandaOps Lab API endpoint `fct_execution_state_size_daily`, which reports daily snapshots of Ethereum mainnet state size extracted from a geth node.
