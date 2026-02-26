## Ethereum State Summary — February 25, 2026

A data-driven snapshot of Ethereum's on-chain state, sourced from the [EthPandaOps Lab](https://lab.ethpandaops.io) analytics platform. All figures are derived from daily geth node snapshots exposed via the EthPandaOps API.

---

### What Data Is Tracked

The platform tracks Ethereum's full on-chain state through daily snapshots from a go-ethereum (geth) node. The state is decomposed into three layers:

| Layer | What It Measures |
|-------|-----------------|
| **Account Trie** | Account leaves (EOAs + contracts), account data bytes, and Merkle Patricia Trie (MPT) nodes |
| **Storage Tries** | Storage slots across all contracts, storage data bytes, and storage MPT nodes |
| **Contract Code** | Deployed bytecode size and count of unique implementations |

**Total state = Account Trie + Storage Tries + Contract Code**

Additionally, the platform models **state expiry** — simulating what would happen to state size under various inactivity-based expiration policies.

---

### Current State Snapshot (Feb 25, 2026)

| Metric | Value |
|--------|-------|
| **Total state size** | **275.3 GB** |
| Account trie nodes | 503M nodes (57.7 GB) |
| Account data | 364.3M accounts (16.8 GB) |
| Storage trie nodes | 2.04B nodes (205.5 GB) |
| Storage data | 1.51B slots (108.8 GB) |
| Contract code | 2.15M unique bytecodes (12.1 GB) |

Storage dominates: the storage trie alone accounts for ~74.6% of total state (205.5 GB of trie nodes + 108.8 GB of data = 314.3 GB logical, though the total is 275.3 GB due to how geth's snapshot structure overlaps).

---

### Growth Trends: Jan 2025 → Feb 2026

| Metric | Jan 1, 2025 | Feb 25, 2026 | Change |
|--------|-------------|--------------|--------|
| **Total size** | 218.5 GB | 275.3 GB | **+56.8 GB (+26.0%)** |
| Accounts | 269.9M | 364.3M | +94.4M (+35.0%) |
| Storage slots | 1.22B | 1.51B | +287M (+23.5%) |
| Account trie nodes | 367.7M | 503.1M | +135.4M (+36.8%) |
| Storage trie nodes | 1.66B | 2.04B | +385M (+23.2%) |
| Contract bytecodes | 1.48M | 2.15M | +677K (+45.9%) |

State grew ~26% in ~14 months. Account growth outpaced storage slot growth in percentage terms, while contract bytecode deployments grew fastest at nearly 46%.

---

### Top 20 Contracts by Storage

Ranked by effective bytes (raw value data, excluding trie overhead). Each slot carries 65 bytes of disk overhead in geth's snapshot structure.

| Rank | Contract | Slots | Bytes | % of All Slots |
|------|----------|------:|------:|---------------:|
| 1 | **XEN Crypto** | 141.4M | 858 MB | 9.4% |
| 2 | Permit2 (Uniswap) | 17.2M | 403 MB | 1.1% |
| 3 | USDT (Tether) | 22.1M | 268 MB | 1.5% |
| 4 | Seaport (OpenSea) | 16.3M | 242 MB | 1.1% |
| 5 | Forsage.io | 20.7M | 233 MB | 1.4% |
| 6 | USDC (Circle) | 13.4M | 181 MB | 0.9% |
| 7 | Ronin Bridge (Axie) | 10.8M | 170 MB | 0.7% |
| 8 | ENS Registry | 8.7M | 166 MB | 0.6% |
| 9 | Aave Token | 9.1M | 158 MB | 0.6% |
| 10 | CryptoKitties | 6.2M | 157 MB | 0.4% |
| 11 | WETH | 6.9M | 116 MB | 0.5% |
| 12 | Arbitrum Bridge | 3.5M | 107 MB | 0.2% |
| 13 | (unnamed: 0x8853…) | 5.3M | 100 MB | 0.4% |
| 14 | Seaport v2 (OpenSea) | 6.0M | 86 MB | 0.4% |
| 15 | Uniswap NFT Positions | 9.0M | 85 MB | 0.6% |
| 16 | (unnamed: 0x8a91…) | 10.0M | 82 MB | 0.7% |
| 17 | ENS Base Registrar | 8.0M | 81 MB | 0.5% |
| 18 | ENS Public Resolver | 2.8M | 81 MB | 0.2% |
| 19 | ZkBridge (Lighter) | 5.2M | 79 MB | 0.3% |
| 20 | zkSync ETH Bridge | 2.8M | 73 MB | 0.2% |

The top 20 contracts hold **~345M storage slots** — roughly **23% of all 1.51B slots** on Ethereum.

---

### State Expiry Modeling

The platform simulates two expiry strategies at multiple inactivity thresholds (1 month, 12 months, 18 months, 24 months):

- **Slot-based expiry**: Individual storage slots expire independently if not written to within the threshold.
- **Contract-based expiry**: All of a contract's slots expire together if the contract hasn't been accessed within the threshold.

#### Case Study: XEN Crypto (Rank 1)

XEN Crypto alone holds 9.4% of all Ethereum storage slots. Most of this storage is dormant:

| Policy | Active Slots | Bytes | Reduction |
|--------|------------:|------:|----------:|
| Baseline (no expiry) | 141.4M | 858 MB | — |
| 24-month inactivity | 133.2M | 811 MB | -5.8% |
| 18-month inactivity | 132.2M | 805 MB | -6.5% |
| 12-month inactivity | 124.2M | 756 MB | -12.2% |
| 1-month inactivity | 9.6M | 58 MB | **-93.2%** |

Under a 1-month expiry window, 93% of XEN Crypto's storage would expire — 131.8 million slots of effectively dead state. Even under a conservative 12-month policy, 17.2 million slots (~12%) would be reclaimed.

---

### Data Sources

All data is sourced from the EthPandaOps analytics API, backed by daily geth node snapshots.

**API base**: `https://lab.ethpandaops.io/api/v1`

| Endpoint | Purpose |
|----------|---------|
| `fct_execution_state_size_daily` | Daily total state size with full breakdown |
| `fct_storage_slot_state_by_block_daily` | Current storage slot counts and value bytes |
| `fct_storage_slot_state_with_expiry_by_block_daily` | Slot-based expiry simulations |
| `fct_contract_storage_state_with_expiry_by_block_daily` | Contract-based expiry simulations |
| `fct_storage_slot_top_100_by_bytes` | Top 100 contracts ranked by storage consumption |

**Disk overhead formula**: Each storage slot occupies `65 + value_bytes` on disk (1 prefix byte + 32 account hash + 32 storage hash + value data).

**Live dashboards**: [State Growth](https://lab.ethpandaops.io/ethereum/execution/state-growth) · [State Expiry](https://lab.ethpandaops.io/ethereum/execution/state-expiry)
