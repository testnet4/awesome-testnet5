# awesome-testnet5

A curated, collaborative list of awesome testnet5 resources. Testnet5 ([BIP95](https://github.com/bitcoin/bips/blob/master/bip-0095.md)) is the proposed successor to [testnet4](https://github.com/testnet4/awesome-testnet4).

This list is young and still being filled in; pull requests are welcome.

## Specification

- [BIP95: Testnet 5](https://github.com/bitcoin/bips/blob/master/bip-0095.md): draft, by Pol Espinasa and Fabian Jahr
- [BIP95 genesis parameters (bips PR 2356)](https://github.com/bitcoin/bips/pull/2356)
- [BIP54: Consensus Cleanup](https://github.com/bitcoin/bips/blob/master/bip-0054.md), enforced from block 1
- [BIP94: Testnet 4](https://github.com/bitcoin/bips/blob/master/bip-0094.mediawiki), the network it replaces

## What changes from testnet4

- **No difficulty exception.** Testnet4 still let a block be mined at minimum difficulty after 20 minutes without a block, and that rule was gamed constantly. Testnet5 drops it, so the proof-of-work rules match mainnet.
- **A higher minimum difficulty:** `0x1a0fffff`, about 1,000,000 (testnet4's minimum is 1).
- **BIP54 from block 1:** the fixes for timewarp, slow-to-validate blocks, Merkle tree weaknesses and duplicate transactions.
- **Everything else follows mainnet,** including all soft forks up to Taproot.

## Network parameters

| | |
|---|---|
| Message start (magic) | `0x46495645` (`FIVE` in ASCII) |
| Default P2P port | `18353` |
| Address prefix | `tb1…`, the same as the other test networks |
| Minimum difficulty (`powLimit`) | `0x1a0fffff`, about 1,048,560 |
| Difficulty retarget | every 2,016 blocks, at most 4× up or ¼ down, as on mainnet |
| Genesis block | `00000000000008c1637e7d3252dc1c333d4546fd6fff6a474b200d674a91d497`, mined 2026-11-14 05:03:13 UTC |

## Implementations

- [Bitcoin Core PR 35861: Testnet 5 (BIP95)](https://github.com/bitcoin/bitcoin/pull/35861). Build the branch, then run with `-chain=testnet5`.
- [libbitcoin](https://github.com/libbitcoin) runs testnet5 nodes (see [Nodes](#nodes)).

## Nodes

Peers you can `addnode` (P2P port 18353):

- `lapis.erisian.com.au:18353`
- `carmine.libbitcoin.net:18353`
- `wormer.libbitcoin.net:18353`

## Explorers

- [wormer.libbitcoin.net](https://wormer.libbitcoin.net:58143/): the explorer built into Libbitcoin Server. It shows a web page in a browser and returns JSON to `Accept: application/json`. Example: [`/v1/tx/<txid>`](https://wormer.libbitcoin.net:58143/v1/tx/41ca9eecef375cb157163e2bec4e9baad769c6c6402e6068de6a2d0963444f4d)
- mempool.space: none yet. `mempool.space/testnet5` doesn't exist.

## Pools

Seen in testnet5 coinbases:

- [lapis.erisian.com.au](https://lapis.erisian.com.au/): ckpool, coinbase tag `/tn5/`. It mined block 1, and [chain.json](https://lapis.erisian.com.au/chain.json) lists its blocks.
- casa21.vinteum.org: ckpool, coinbase tag `ckpool/@jaonoctus/casa21.vinteum.org/0x539/`, taproot payout. Large since about 2026-11-15.
- P2Pool v2: coinbase tag `P2Poolv2`
- [datstr](https://datstr.com/): a decentralized share network. Each block's coinbase pays every miner directly, plus a commitment output.

## Faucets

- None known yet. Coins come from mining (see [Pools](#pools)).

## Timeline

| When (UTC) | Height | Event |
|---|---|---|
| 2026-11-14 05:03 | 0–1 | Genesis, then block 1 |
| 2026-11-14 19:14 | 2,016 | First retarget: difficulty 1.05M → 4.19M (the 4× cap), after about 14 hours |
| 2026-11-15 05:18 | 4,032 | Second retarget: 4.19M → 16.8M (4× cap again), after about 10 hours |
| 2026-11-15 | ~4,100 | Network about 1.4 PH/s, a block every ~50 s |

There's no difficulty exception, so blocks arrive far faster than every 10 minutes until retargets catch up with the hashrate, up to 4× per retarget. If hashrate leaves, blocks slow down until the next retarget. Expect a few rounds of feast and famine before it settles.

## Discussions

- [bitcoindev: Testnet 5 (2026-06-02)](https://groups.google.com/g/bitcoindev/c/kGUMTxOvdJA)
- [Bitcoin Core PR 35861](https://github.com/bitcoin/bitcoin/pull/35861)
- [bips PR 2356: genesis parameters](https://github.com/bitcoin/bips/pull/2356)

## Tips

- **Coinbase maturity is 100 blocks,** as on mainnet. During a fast stretch that can be under an hour.
- **Wallets built for chains with replay protection** (for example the BLAKE2b forks' `SIGHASH_UNIFIED`, `0x21`) won't work on testnet5. Sign with plain BIP143/BIP341 sighashes. The node rejects anything else with `Signature hash type missing or not understood`.
- **The node's RPC cookie is rewritten on every restart,** so restart anything that reads it.
