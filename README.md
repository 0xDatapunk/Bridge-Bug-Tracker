# Compilation of Hacks/Vulns/Audits On Ethereum Bridges-L2s
Talks
===================
**Securing a Cross-Chain Bridge** by Christopher Whinfrey, hop protocol [video](https://youtu.be/umV-wcKlpjg?t=24940), [slides](https://drive.google.com/file/d/1NEhABFJVt6hGGuvRrohbxnWQ442i2DO8/view)

**Pre-Crime: the future of omnichain security** by Ryan Zarick, LayerZero Labs [video](https://youtu.be/umV-wcKlpjg?t=26109), [slides](https://drive.google.com/file/d/1dd6R9LHmZ1At7UxGYbYMwSsu9KDG0sD-/view)

**Breaking down bridge security models** by Layne Haber, Connext [video](https://youtu.be/umV-wcKlpjg?t=26916), [slides](https://drive.google.com/file/d/1nonAR8QKgLWAcPRveADskAejtdiY8D2J/view)

**Breaking bridges** by Yoav Weiss, EF [video](https://youtu.be/umV-wcKlpjg?t=27768), [slides](https://drive.google.com/file/d/1GpSEeFe0xmC4WlOA8mm4JSgRnTEiyiTX/view)

Hacks Reproduced
===================

Inline-style: 
![alt text](https://github.com/adam-p/markdown-here/raw/master/src/common/images/icon48.png "Logo Title" \ "Text 1")

The below table shows known hacks since 2021:

| Date  | Protocol | Funds At Risk | Root Cause | References | Code to Reproduce |
| ------------- | ------------- | ------------- | ------------- | ------------- | ------------- |
| 20220802 | [Nomad](https://docs.nomad.xyz/nomad-101/introduction)  | $152M | `acceptableRoot[address(0)] == true` | [twitter](https://twitter.com/samczsun/status/1554252024723546112) | [.sol](https://github.com/0xDatapunk/DeFiHackLabs/blob/main/src/test/NomadBridge.exp.sol) |
| 20220624 | [Harmony's Horizon](https://docs.harmony.one/home/general/introduction/what-is-harmony) | $100M | Private key compromised | [twitter](https://twitter.com/0xIvo/status/1540165571681128448) | [.sol](https://github.com/0xDatapunk/DeFiHackLabs/blob/main/src/test/Harmony_multisig.sol) |
| 20220329 | [Ronin](https://docs.roninchain.com/docs/components/ronin-bridge-v2) | $624M | Private key compromised | [twitter](https://twitter.com/captaindefi2/status/1508852842685153282?lang=en) | [.sol](https://github.com/0xDatapunk/DeFiHackLabs/blob/main/src/test/Ronin_exp.sol) |
| 20220320 | [Li Finance](https://www.covalenthq.com/docs/project-showcase/dex/li-finance/) | $570K | allow calls to any contracts | [blog](https://blog.li.fi/20th-march-the-exploit-e9e1c5c03eb9) | [.sol](https://github.com/0xDatapunk/DeFiHackLabs/blob/main/src/test/LiFi_exp.sol) |
| 20220206 | [Meter](https://docs.meter.io/) | $4.3M | [](# "The problem with this assumption is that Meter has two functions where users could make deposits: depositEth and the underlying ETH20 deposit function.  The depositEth function fulfills this assumption and validates the amount of value in the transaction’s calldata, which is the value that will later be passed to the deposit function.\
&nbsp;
The other deposit function does not fulfill this assumption or check that msg.value equals the amount specified within the calldata.  The attacker called this deposit function directly and passed it an arbitrary amount in the calldata.  This value was then sent to the handler’s deposit function, enabling the attacker to drain value from the protocol.\
&nbsp;
Hundred Finance was affected by the attack because the local price of BNB.bsc was depreciated due to the hack.  Exploiters were able to buy BNB.bsc at a discounted rate and use them as collateral for loans with Hunter Finance, who used the global Chainlink price for the assets.  As a result, the attackers could drain uncompromised assets from the protocol.  Two of the four opportunistic loans were repaid, leaving Hunter Finance out $3.3 million.") | [twitter](https://twitter.com/ishwinder/status/1490227406824685569) <br /> [blog](https://blog.chainsafe.io/breaking-down-the-meter-io-hack-a46a389e7ae4) | [.sol](https://github.com/0xDatapunk/DeFiHackLabs/blob/main/src/test/Meter_exp.sol) |
| 20220118 | [Wormhole](https://docs.wormhole.com/wormhole/) | $360M | fake account to precompiled sysvar | [twitter](https://twitter.com/samczsun/status/1489044939732406275) | solana |
| 20220128 | [Qubit Finance](https://docs.qbt.fi/protocol/bridge) | $80M | [address(0).safeTransferFrom() does not revert](# "the contract did not use OpenZeppelin’s SafeERC20 library. If the contract had used this library, the exploit would not have been possible as the SafeERC20.safeTransferFrom function makes use of functionCall() (function from OpenZeppelin’s Address.sol contract) which verifies that the target address contains contract code. This is not the case with the 0 address. /n The exploited contract used a modified safeTransferFrom() function which instead of making use of functionCall() to verify that the target address contained contract code, used the call() function directly. As the 0 address has no code at all, no code is run, and the call is completed successfully without reverting. As a result, the deposit function executed successfully but no real tokens were deposited. The Ethereum QBridge caught the Deposit event and interpreted it as a valid deposit of ETH.  As a result, qXETH tokens were minted for the attacker on BSC. <br /> By repeating this process multiple times, the attacker was able to build up a large amount of qXETH without depositing any real tokens into the protocol.  The attacker then was able to convert these tokens into BNB, draining about $80 million in assets from the protocol.") | [rekt](https://rekt.news/qubit-rekt/) | [.sol](https://github.com/0xDatapunk/DeFiHackLabs/blob/main/src/test/Qubit_exp.sol) |
| 20220118 | [Multichain/Anyswap]() | $1.4M | a) fail to validate token <br /> b) fallback does not revert <br /> c) infinite approval   | [medium](https://medium.com/zengo/without-permit-multichains-exploit-explained-8417e8c1639b) | [.sol](https://github.com/0xDatapunk/DeFiHackLabs/blob/main/src/test/Anyswap_poc.t.sol) |
| 20210811 | [PolyNetwork](https://github.com/polynetwork/docs) | $611M | use cross-chain messages to call special contracts | [rekt](https://rekt.news/polynetwork-rekt/) <br /> [medium](https://medium.com/breadcrumbsapp/the-600m-poly-network-hack-the-biggest-hack-in-defi-history-e2efe56cf3a8) | [.sol](https://github.com/0xDatapunk/DeFiHackLabs/blob/main/src/test/PolyNetwork/PolyNetwork_exp.sol) |
| 20210710 | [Chainswap](https://docs.chainswap.com/) | $4.4M | `require(signatory == signatures[i].signatory, "unauthorized");` | [twitter](https://twitter.com/real_n3o/status/1414071223940571139) <br /> [rekt](https://rekt.news/chainswap-rekt/) | [.sol](https://github.com/0xDatapunk/DeFiHackLabs/blob/main/src/test/Chainswap_exp2.sol) |
| 20210702 | [Chainswap](https://docs.chainswap.com/) | $.8M | `require(signatory == signatures[i].signatory, "unauthorized");` | [post-mortem](https://chain-swap.medium.com/chainswap-post-mortem-and-compensation-plan-90cad50898ab) | [.sol](https://github.com/0xDatapunk/DeFiHackLabs/blob/main/src/test/Chainswap_exp1.sol) |

Confirmed Bug Bounties
===========================

Audits
===========================
