# Compilation of Hacks/Vulns/Audits On Ethereum Bridges-L2s
Talks
===================
**Securing a Cross-Chain Bridge** by Christopher Whinfrey, hop protocol [video](https://youtu.be/umV-wcKlpjg?t=24940), [slides](https://drive.google.com/file/d/1NEhABFJVt6hGGuvRrohbxnWQ442i2DO8/view)

**Pre-Crime: the future of omnichain security** by Ryan Zarick, LayerZero Labs [video](https://youtu.be/umV-wcKlpjg?t=26109), [slides](https://drive.google.com/file/d/1dd6R9LHmZ1At7UxGYbYMwSsu9KDG0sD-/view)

**Breaking down bridge security models** by Layne Haber, Connext [video](https://youtu.be/umV-wcKlpjg?t=26916), [slides](https://drive.google.com/file/d/1nonAR8QKgLWAcPRveADskAejtdiY8D2J/view)

**Breaking bridges** by Yoav Weiss, EF [video](https://youtu.be/umV-wcKlpjg?t=27768), [slides](https://drive.google.com/file/d/1GpSEeFe0xmC4WlOA8mm4JSgRnTEiyiTX/view)

Hacks Reproduced
===================

The below table shows known hacks since 2021:

| Date  | Protocol | Funds At Risk | Root Cause | References | Code to Reproduce |
| ------------- | ------------- | ------------- | ------------- | ------------- | ------------- |
| 20220802 | [Nomad](https://docs.nomad.xyz/nomad-101/introduction)  | $152M | `acceptableRoot[address(0)] == true` | [twitter](https://twitter.com/samczsun/status/1554252024723546112) | [.sol](https://github.com/0xDatapunk/DeFiHackLabs/blob/main/src/test/NomadBridge.exp.sol) |
| 20220624 | [Harmony's Horizon](https://docs.harmony.one/home/general/introduction/what-is-harmony) | $100M | Private key compromised | [twitter](https://twitter.com/0xIvo/status/1540165571681128448) | [.sol](https://github.com/0xDatapunk/DeFiHackLabs/blob/main/src/test/Harmony_multisig.sol) |
| 20220329 | [Ronin](https://docs.roninchain.com/docs/components/ronin-bridge-v2) | $624M | Private key compromised | [twitter](https://twitter.com/captaindefi2/status/1508852842685153282?lang=en) | [.sol](https://github.com/0xDatapunk/DeFiHackLabs/blob/main/src/test/Ronin_exp.sol) |
| 20220320 | [Li Finance](https://www.covalenthq.com/docs/project-showcase/dex/li-finance/) | $570K | allow calls to any contracts | [blog](https://blog.li.fi/20th-march-the-exploit-e9e1c5c03eb9) | [.sol](https://github.com/0xDatapunk/DeFiHackLabs/blob/main/src/test/LiFi_exp.sol) |
| 20220118 | [Wormhole](https://twitter.com/samczsun/status/1489044939732406275) | $360M | fake account to precompiled sysvar | [twitter](https://twitter.com/samczsun/status/1489044939732406275) | solana |
| 20220128 | [Qubit Finance](https://docs.qbt.fi/protocol/bridge) | $80M | address(0).safeTransferFrom() does not revert | [rekt](https://rekt.news/qubit-rekt/) | [.sol](https://github.com/0xDatapunk/DeFiHackLabs/blob/main/src/test/Qubit_exp.sol) |
| 20220118 | [Multichain/Anyswap]() | $1.4M | a) fail to validate token <br /> b) fallback does not revert <br /> c) infinite approval   | [medium](https://medium.com/zengo/without-permit-multichains-exploit-explained-8417e8c1639b) | [.sol](https://github.com/0xDatapunk/DeFiHackLabs/blob/main/src/test/Anyswap_poc.t.sol) |
| 20210811 | [PolyNetwork](https://github.com/polynetwork/docs) | $611M | use cross-chain messages to call special contracts | [rekt](https://rekt.news/polynetwork-rekt/) <br /> [medium](https://medium.com/breadcrumbsapp/the-600m-poly-network-hack-the-biggest-hack-in-defi-history-e2efe56cf3a8) | [.sol](https://github.com/0xDatapunk/DeFiHackLabs/blob/main/src/test/PolyNetwork/PolyNetwork_exp.sol) |
| 20210710 | [Chainswap](https://docs.chainswap.com/) | $4.4M | `require(signatory == signatures[i].signatory, "unauthorized");` | [twitter](https://twitter.com/real_n3o/status/1414071223940571139) <br /> [rekt](https://rekt.news/chainswap-rekt/) | [.sol](https://github.com/0xDatapunk/DeFiHackLabs/blob/main/src/test/Chainswap_exp2.sol) |
| 20210702 | [Chainswap](https://docs.chainswap.com/) | $.8M | `require(signatory == signatures[i].signatory, "unauthorized");` | [post-mortem]([https://twitter.com/real_n3o/status/1414071223940571139](https://chain-swap.medium.com/chainswap-post-mortem-and-compensation-plan-90cad50898ab) | [.sol](https://github.com/0xDatapunk/DeFiHackLabs/blob/main/src/test/Chainswap_exp1.sol) |

Confirmed Bug Bounties
===========================

Audits
===========================
