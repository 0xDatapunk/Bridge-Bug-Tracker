# L2/Bridge Bug Tracker

A community-maintained collection of bugs, vulnerabilities, and exploits in L2/Bridges.

L2/Bridges are scaling Ethereum. By their nature, they have also become honey pots for exploits, potentially with high severity impact. The Bugs tracker lists frameworks, actual bugs, and selected audits in the hope of making L2/Bridges more secure. These lists can be used as a reference for developers, auditors, and security tool makers.

#### Contributing
If you would like to contribute, there are two ways to do so:
1. Create a PR, filling in all of the necessary details yourself
2. Create an issue with a link or description of the bug or common vulnerability. The repo maintainers will then fill out the relevant details in a PR.

# Table of Contents
#### [The Current State of Layer2/Bridges](#current-state-of-Layer2/Bridges-header)
#### [Talks](#Talks-header)
#### [Different ways to go wrong - A Framework](#Different-ways-to-go-wrong-header)
#### [Real Hacks Reproduced](#Real-Hacks-Reproduced-header)
#### [Confirmed Bug Bounties](#Confirmed-Bug-Bounties-header)
#### [Selected Audits](#Selected-Audits-header)


# <a name="#current-state-of-Layer2/Bridges-header">The Current State of Layer2/Bridges</a>

  * https://l2beat.com/scaling/tvl
  * https://entethalliance.org/2023-02-28-state-of-l2-bridges/

# <a name="Talks-header">Talks</a>

**EVM-to-EVM Chain Bridges: The Good, the Bad and the Ugly** by Jan Gorzny, quantstamp [video](https://www.youtube.com/watch?v=f4GOa4XwCjY)

**Review of Cross-Chain Bridge Hacks** by Jan Gorzny, quantstamp [video](https://youtu.be/EdH7UaJec3g?t=18280), [slides](https://drive.google.com/file/d/1N_BWDDm1YELMkD5WZEEFQ0sD2opAkCgn/view)

**Securing a Cross-Chain Bridge** by Christopher Whinfrey, hop protocol [video](https://youtu.be/umV-wcKlpjg?t=24940), [slides](https://drive.google.com/file/d/1NEhABFJVt6hGGuvRrohbxnWQ442i2DO8/view)

**Pre-Crime: the future of omnichain security** by Ryan Zarick, LayerZero Labs [video](https://youtu.be/umV-wcKlpjg?t=26109), [slides](https://drive.google.com/file/d/1dd6R9LHmZ1At7UxGYbYMwSsu9KDG0sD-/view)

**Breaking down bridge security models** by Layne Haber, Connext [video](https://youtu.be/umV-wcKlpjg?t=26916), [slides](https://drive.google.com/file/d/1nonAR8QKgLWAcPRveADskAejtdiY8D2J/view)

**Breaking bridges** by Yoav Weiss, EF [video](https://youtu.be/umV-wcKlpjg?t=27768), [slides](https://drive.google.com/file/d/1GpSEeFe0xmC4WlOA8mm4JSgRnTEiyiTX/view)

**The Bridge Risk Framework Seminar** by Spearbit, [video](https://www.youtube.com/watch?v=JVNgsmEc5Lk), [blog](https://gov.l2beat.com/t/l2bridge-risk-framework/31)

**Bridge Security** by Spearbit, [intro](https://github.com/spearbit/portfolio/blob/master/content/bridges/BridgeSecurityIntroduction.md), [checklist](https://github.com/spearbit/portfolio/blob/master/content/bridges/BridgeSecurityChecklist.md)

# <a name="#Different-ways-to-go-wrong-header">Different ways to go wrong - A Framework</a>

### from [Quantstamp](https://drive.google.com/file/d/1N_BWDDm1YELMkD5WZEEFQ0sD2opAkCgn/view)
<details><summary>The Custodian</summary>
  - Incorrect asset amount released with respect to the burnt tokens<br>
  - Assets released despite the debt token has not been burnt<br>
  - Asset transaction replay for a single burn transaction<br>
  </details>
  
<details><summary>The Debt Issuer</summary>
  - Incorrect amount of debt issued with respect to the deposited assets<br>
  - Debt token issued although the actual verification did not take place<br>
  - Anybody can issue debt tokens<br>
</details>
<details><summary>The Communicator</summary>
  - Issues debt tokens although no assets have been deposited<br>
  - Issues no debt tokens although assets have been deposited<br>
  - Accepts fraudulent messages from a fake custodian or a debt issuer<br>
  - Does not relay messages<br>
  - The source contract does not emit events upon deposit/withdrawal<br>
</details>
<details><summary>The Interface (could be fixed with "revoke approval")</summary>
  - Deposit from another account<br>
  - Execute any calls from any contract<br>
</details>
<details><summary>The Network</summary>
  - 51% attack<br>
</details>


# <a name="#Real-Hacks-Reproduced-header">Real Hacks Reproduced</a>

The below table shows known hacks since 2021:

| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Date&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Protocol | Funds At Risk | Root Cause | References | Code to Reproduce |
| ------------- | ------------- | ------------- | ------------- | ------------- | ------------- |
| 2022-10-07 | BNB Bridge  | $586M | <details><summary>BSC has a special precompile to verify IAVL trees, which is buggy</summary>*in [proofInnerNode.Hash function](https://github.com/cosmos/iavl/blob/de0740903a67b624d887f9055d4c60175dcfa758/proof.go#L53), the value of Right is ignored if Left is not empty, so you were able to change the path yet the (path, nleaf) hash did not change.*</details> | [twitter](https://twitter.com/dedaub/status/1578428002701959170?s=46&t=baZxJq2wl8J6EeoDaM_o2w) [gist](https://gist.github.com/samczsun/8635f49fac0ec66a5a61080835cae3db) | N/A |
| 2022-08-02 | [Nomad](https://docs.nomad.xyz/nomad-101/introduction)  | $152M | <details><summary>custodian: transaction replay attack <br> `acceptableRoot[address(0)] == true`</summary>*Within the process() function is an assert (line 185) that validates that the message for the transfer is associated with a valid root.  By default, a root for an unproven message would be 0x00. <br><br> In an upgrade to the protocol, Nomad decided to initialize the value of trusted roots to 0x00.  While this is common practice, it also matches the value for an untrusted root, so all messages are automatically viewed as proven.<br>[...](https://halborn.com/explained-the-nomad-hack-august-2022/)*</details> | [Meidum](https://medium.com/nomad-xyz-blog/nomad-bridge-hack-root-cause-analysis-875ad2e5aacd) [twitter](https://twitter.com/samczsun/status/1554252024723546112) | [.sol](https://github.com/0xDatapunk/DeFiHackLabs/blob/main/src/test/NomadBridge.exp.sol) |
| 2022-06-24 | [Harmony's Horizon](https://docs.harmony.one/home/general/introduction/what-is-harmony) | $100M | <details><summary>Private key compromised</summary>*the bridge only used a 2 of 5 validation scheme.  This means that only two blockchain accounts needed to be compromised for an attacker to approve any malicious transaction that they wished. <br><br> The Harmony Horizon bridge was exploited via the theft of two private keys. These private keys were encrypted with both a passphrase and a key management service, and no system had access to multiple plaintext keys.  However, the attacker managed to access and decrypt multiple keys.<br>[...](https://halborn.com/explained-the-harmony-horizon-bridge-hack/)*</details> | [twitter](https://twitter.com/0xIvo/status/1540165571681128448) | [.sol](https://github.com/0xDatapunk/DeFiHackLabs/blob/main/src/test/Harmony_multisig.sol) |
| 2022-06-08 | Optimism / Wintermute | 20M $OP | <details><summary>multisig address replay on L2</summary>*Wintermute provided OP an Ethereum (L1) multisig address that they had not yet deployed to Optimism (L2). Attacker replayed txs to deploy ProxyFactory on L2, using the address of "Gnosis Safe: Deployer 3", which was pre-EIP155, thus does not include chainid. Attacker then generate a massive amount of multisig contracts until finding the matching address*</details> | [blog](https://inspexco.medium.com/how-20-million-op-was-stolen-from-the-multisig-wallet-not-yet-owned-by-wintermute-3f6c75db740a) | [.sol](https://github.com/0xDatapunk/DeFiHackLabs/blob/main/src/test/Optimism_exp.sol) |
| 2022-03-29 | [Ronin](https://docs.roninchain.com/docs/components/ronin-bridge-v2) | $624M | <details><summary>Private key compromised</summary>*The Ronin Network uses a set of nine validator nodes to approve transactions on the bridge, and a deposit or withdrawal requires approval by a majority of five of these nodes. The attacker gained control of four validators controlled by Sky Mavis and a third-party Axie DAO validator that signed their malicious transactions.<br>[...](https://halborn.com/explained-the-ronin-hack-march-2022/)*</details> | [twitter](https://twitter.com/captaindefi2/status/1508852842685153282?lang=en) | [.sol](https://github.com/0xDatapunk/DeFiHackLabs/blob/main/src/test/Ronin_exp.sol) |
| 2022-03-20 | [Li Finance](https://www.covalenthq.com/docs/project-showcase/dex/li-finance/) | $570K | <details><summary>allow calls to any contracts</summary>*The hack took advantage of our pre-bridge swap feature. Our smart contract allows a caller to pass an array of multiple swaps using any address with arbitrary calldata.<br><br> This design gave us maximum flexibility in what DEXs we could call and what methods we could call. This also allowed anyone to call other contracts, not just DEXs. Our contract checks to make sure that the result of the swap or swaps is enough tokens to continue the bridging operation.<br><br>The attacker started by passing a legitimate swap of a small amount followed by multiple calls directly to various token contracts. Specifically, they called the `transferFrom` method which allowed the attacker to transfer funds from users’ wallets that had previously given infinite approval to our contract for that specific token.<br><br>This worked because these calls were performed within the context of the contract, which had permission to transfer user funds. The attacker transferred these tokens into a separate wallet that he controlled.<br><br>Once the transfers were completed, the small amount swapped at the beginning was bridged, and the transaction was completed.*</details> | [blog](https://blog.li.fi/20th-march-the-exploit-e9e1c5c03eb9) | [.sol](https://github.com/0xDatapunk/DeFiHackLabs/blob/main/src/test/LiFi_exp.sol) |
| 2022-02-06 | [Meter](https://docs.meter.io/) | $4.3M | <details><summary>missing `require(amount_from_calldata==msg.value)` in deposit()</summary>*The problem with this assumption is that Meter has two functions where users could make deposits: depositEth and the underlying ETH20 deposit function. The depositEth function fulfills this assumption and validates the amount of value in the transaction’s calldata, which is the value that will later be passed to the deposit function.<br><br>The other deposit function does not fulfill this assumption or check that msg.value equals the amount specified within the calldata. The attacker called this deposit function directly and passed it an arbitrary amount in the calldata. This value was then sent to the handler’s deposit function, enabling the attacker to drain value from the protocol.<br><br>Hundred Finance was affected by the attack because the local price of BNB.bsc was depreciated due to the hack. Exploiters were able to buy BNB.bsc at a discounted rate and use them as collateral for loans with Hunter Finance, who used the global Chainlink price for the assets. As a result, the attackers could drain uncompromised assets from the protocol. Two of the four opportunistic loans were repaid, leaving Hunter Finance out $3.3 million. <br> [...](https://halborn.com/explained-the-meter-io-hack-february-2022/)*</details> | [twitter](https://twitter.com/ishwinder/status/1490227406824685569) <br /> [blog](https://blog.chainsafe.io/breaking-down-the-meter-io-hack-a46a389e7ae4) | [source](https://github.com/meterio/chainbridge-solidity-v1.0.0-eth/blob/4aa52d503202fa50b6379be6696c73f2cd694864/contracts/Bridge.sol#L441) <BR> [.sol](https://github.com/0xDatapunk/DeFiHackLabs/blob/main/src/test/Meter_exp.sol) |
| 2022-01-18 | [Wormhole](https://docs.wormhole.com/wormhole/) | $360M | <details><summary>debt issuer: fake verification attack <br> fake account to precompiled sysvar</summary>*The `solana_program::sysvar::instructions` mod is meant to be used with the Instructions sysvar, a sort of precompile on Solana. However, the version of `solana_program` that Wormhole used didn't verify the address being used. <br><br>This meant that you could create your own account which stored the same data that the Instructions sysvar would have stored, and substituted that account for the Instruction sysvar in the call to `verify_signatures`. This would essentially bypass signature validation entirely.*</details> | [twitter](https://twitter.com/samczsun/status/1489044939732406275) | solana |
| 2022-01-28 | [Qubit Finance](https://docs.qbt.fi/protocol/bridge) | $80M | <details><summary>address(0).safeTransferFrom() does not revert</summary>*the contract did not use OpenZeppelin’s SafeERC20 library. If the contract had used this library, the exploit would not have been possible as the SafeERC20.safeTransferFrom function makes use of functionCall() (function from OpenZeppelin’s Address.sol contract) which verifies that the target address contains contract code. This is not the case with the 0 address. <br><br> The exploited contract used a modified safeTransferFrom() function which instead of making use of functionCall() to verify that the target address contained contract code, used the call() function directly. As the 0 address has no code at all, no code is run, and the call is completed successfully without reverting. As a result, the deposit function executed successfully but no real tokens were deposited. The Ethereum QBridge caught the Deposit event and interpreted it as a valid deposit of ETH.  As a result, qXETH tokens were minted for the attacker on BSC. <br><br> By repeating this process multiple times, the attacker was able to build up a large amount of qXETH without depositing any real tokens into the protocol.  The attacker then was able to convert these tokens into BNB, draining about $80 million in assets from the protocol.<br>[...](https://halborn.com/explained-the-qubit-hack-january-2022/)*</detail> | [rekt](https://rekt.news/qubit-rekt/) | [.sol](https://github.com/0xDatapunk/DeFiHackLabs/blob/main/src/test/Qubit_exp.sol) |
| 2022-01-18 | [Multichain<br>/Anyswap]() | $1.4M | <details><summary>a) fail to validate token, b) fallback does not revert, c) infinite approval</summary>*address _underlying = AnyswapV1ERC20(token).underlying(); It’s intended to unwrap the underlying token (“DAI”) from the its anyToken wrapping (“anyDAI”). However, token now is now the attacker’s controlled contract. We can see in the debugger, that this contract now returns WETH as its “underlying asset”. Multichain failed here as this function should have checked if the token address is indeed a Multichain token<br><br>IERC20(_underlying).permit(from, address(this), amount, deadline, v, r, s); Originally, the expected result was that the underlying token’s (“WETH”) ERC20 contract permit() is called to approve the router’s (this) ability to withdraw an amount from the user’s (from) address, as the user supplied a signed transaction for that denoted by (v,r,s). However, WETH contract does not have a permit() function! WETH contract does have a “fallback function” that is called when a function is called but not found. WETH’s fallback function is deposit() that does nothing material in this case, but allows its calling function’s execution to continue as it does not fail.<br><br>TransferHelper.safeTransferFrom(_underlying, from, token, amount); Originally, we expected that if we got to this line it means the signature in the line above was verified and now we can use the approve granted by it to actually move the the amount from the user to the router. However, the signature was not verified, as seen above. In theory, it should not be a problem, as although the attacker’s input should not have passed the signature validation, it did not approve the router access to transfer the funds on the victim’s behalf. However, Multichain’s dapp requested from all of its users a practically infinite approval sum. This insecure methodology is quite common in dapps, to save user expenses on gas. We had warned in the past that such behavior (we named it baDAPProve) can be hazardous in case of a rogue or a vulnerable dapp, and now this potential threat had materialized. By abusing this excessive approval, the function transfers the WETH amount from the victim account to the attackers’ controlled contract.*</detail> | [medium](https://medium.com/zengo/without-permit-multichains-exploit-explained-8417e8c1639b) | [.sol](https://github.com/0xDatapunk/DeFiHackLabs/blob/main/src/test/Anyswap_poc.t.sol) |
| 2021-08-11 | [PolyNetwork](https://github.com/polynetwork/docs) | $611M | <details><summary>custodian: call relay attack <br> use cross-chain messages to call special contracts with hash collision</summary>*The core of this attack is that the verifyHeaderAndExecuteTx function of the EthCrossChainManager contract can execute specific cross-chain transactions through the _executeCrossChainTx function.<br><br>Since the owner of the EthCrossChainData contract is the EthCrossChainManager contract, the EthCrossChainManager contract can modify the keeper of the contract by calling the putCurEpochConPubKeyBytes function of the EthCrossChainData contract.<br><br>The verifyHeaderAndExecuteTx function of the EthCrossChainManager contract can perform user-specified cross-chain transactions by calling the _executeCrossChainTx function internally. So the attacker only needs to pass in the carefully constructed data through the verifyHeaderAndExecuteTx function for the _executeCrossChainTx function to execute the call to the EthCrossChainData contract PutCurEpochConPubKeyBytes function to change the keeper role to the address specified by the attackers.<br><br>After replacing the address of the keeper role, the attacker can construct a transaction at will and withdraw any amount of funds from the contract.*</detail> | [rekt](https://rekt.news/polynetwork-rekt/) <br /> [medium](https://slowmist.medium.com/the-root-cause-of-poly-network-being-hacked-ec2ee1b0c68f) | [.sol](https://github.com/0xDatapunk/DeFiHackLabs/blob/main/src/test/PolyNetwork/PolyNetwork_exp.sol) |
| 2021-07-10 | [Chainswap](https://docs.chainswap.com/) | $4.4M | <details><summary>`require(signatory == signatures[i].signatory, "unauthorized");`</summary>*this shows the misunderstanding of signature verification as both signatory and r,s,v are provided by the user*</detail> | [twitter](https://twitter.com/real_n3o/status/1414071223940571139) <br /> [rekt](https://rekt.news/chainswap-rekt/) | [.sol](https://github.com/0xDatapunk/DeFiHackLabs/blob/main/src/test/Chainswap_exp2.sol) |
| 2021-07-02 | [Chainswap](https://docs.chainswap.com/) | $.8M | <details><summary>`require(signatory == signatures[i].signatory, "unauthorized");`</summary>*this shows the misunderstanding of signature verification as both signatory and r,s,v are provided by the user*</detail> | [post-mortem](https://chain-swap.medium.com/chainswap-post-mortem-and-compensation-plan-90cad50898ab) | [.sol](https://github.com/0xDatapunk/DeFiHackLabs/blob/main/src/test/Chainswap_exp1.sol) |

# <a name="Confirmed-Bug-Bounties-header">Confirmed Bug Bounties</a>

 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Date&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Protocol | References | Vuln | Exploit |
| ------------- | ------------- | ------------- | ------------- | ------------- |
| 2022-09-19 | [Arbitrum](https://developer.arbitrum.io/)  | [twitter](https://twitter.com/0xriptide/status/1572051111246467074)<br>[medium](https://medium.com/@0xriptide/hackers-in-arbitrums-inbox-ca23272641a2) | <details><summary>`postUpgradeInit` function wipes slots 0,1 & 2 and sets the bridge and allowListEnabled slots to new values — but leaves sequencerInbox and the two booleans set by the intializer modifier empty.</summary>*call the public initialize() function and set our own address as the bridge to accept all incoming ETH deposits … but only because of this gas optimization in the code from a month prior.*</detail> | Once initialized the contact with our own bridge contract address, we can hijack all incoming ETH deposits from users attempting to bridge to Arbitrum via the depositEth() function | 
| 2022-06-07 | [Aurora](https://doc.aurora.dev/) | [blog](https://aurora.dev/blog/aurora-mitigates-its-inflation-vulnerability), [immunefi](https://medium.com/immunefi/aurora-infinite-spend-bugfix-review-6m-payout-e635d24273d#b405), [source](https://github.com/aurora-is-near/aurora-engine/blob/5c8691ea6ea5f1b309ef227f7f5c719ffea45d28/engine-precompiles/src/native.rs#L198), [disclosure](https://app.ardrive.io/#/drives/7ba902d8-d26a-4dad-99b8-807eaaf8d925/folders/c63e6e8b-0d66-4018-b5ac-e93afe948d46) | <details><summary>delegateCall to precompiles</summary>*In the exit to NEAR and exit to Ethereum precompiles, the contract address was hardcoded with disregard to how DelegateCall works. When someone calls the contract it comes from the address of the contract always, and not from the input. Also, since the balance is from the EOA and not the contract, there is no transfer of ETH. This results in the Aurora Engine scheduling a transfer from its NEP-141 ETH balance to the adversary while it has not received an ETH transfer.*</detail> | Instead of removing the hardcoded contract address, given context, it turned out to be better to instead return an exit error if the address given does not match the inputs' address. |
| 2022-02-02 | [Optimism](https://www.optimism.io/)  | [github](https://github.com/ethereum-optimism/optimism/blob/develop/technical-documents/postmortems/2022-02-02-inflation-vuln.md)<br>[writeup](https://www.saurik.com/optimism.html) | <details><summary>The code for Suicide is directly modifying the stateObject's data.Balance field instead of checking UsingOVM and redirecting that modification to OVM_ETH</summary></details> | Contract balances were improperly zeroed during self-destruction, so that the contract address would still have a balance after it had been self-destructed. This could have enabled an attacker to run a loop which doubled the balance of a contract each time, resulting in massive inflation and issuance directly to the attacker. | 

# <a name="Selected-Audits-header">Selected Audits</a>

### 2023-02-06 Optimism Bedrock - [Sherlock](https://app.sherlock.xyz/audits/contests/63) - [report](https://app.sherlock.xyz/audits/contests/38)

<details><summary>3 Highs </summary>
  
  1. [Due to additional operations between gas check and gas use, malicious user can finalize other’s withdrawal with less than specified gas limit, leading to loss of funds](https://github.com/sherlock-audit/2023-01-optimism-judging/issues/109) 
  2. [Due to forwarded gas being silently reduced if exceeding 63/64th of total gasleft(), withdrawals with high gas limits can be bricked by a malicious user, permanently locking funds](https://github.com/sherlock-audit/2023-01-optimism-judging/issues/96) 
  3. [Due to presence of reentrancy guard on the function relayMessage, a malicious user can make users lose their fund during finalizing their withdrawal](https://github.com/sherlock-audit/2023-01-optimism-judging/issues/87)
</details>   
  
<details><summary> 11 Mediums </summary>  
  
  1. [Due to not checking the value of is_last, batcher frames are incorrectly decoded leading to consensus split (.go)](https://github.com/sherlock-audit/2023-01-optimism-judging/issues/279) 
  2. [Due to MAX_RESOURCE_LIMIT, censorship resistance is undermined and bridging of assets can be DOSed at low cost](https://github.com/sherlock-audit/2023-01-optimism-judging/issues/277)
  3. [When decoding a deposit transaction JSON string without the "gas" field, a panic/runtime error is triggered due to a nil pointer dereference (.go)](https://github.com/sherlock-audit/2023-01-optimism-judging/issues/276)
  4. [Due to incorrect gas factor of 16 instead of 4 for 0 value bytes, MigrateWithdrawal() may set gas limit so high for old withdrawals when migrating them by mistake and they can't be relayed in the L1 and users funds would be lost (.go)](https://github.com/sherlock-audit/2023-01-optimism-judging/issues/235)
  5. [Due to missing function, contract with only IOptimismMintableERC20 interface is not compatible with StandardBridge](https://github.com/sherlock-audit/2023-01-optimism-judging/issues/220)
  6. [Due to small size of the blockHeightLRU, attacker can replay blocks and eclipses a node from the P2P network (.go)](https://github.com/sherlock-audit/2023-01-optimism-judging/issues/177)
  7. [Migration can be bricked by sending a message directly to the LegacyMessagePasser (.go)](https://github.com/sherlock-audit/2023-01-optimism-judging/issues/105)
  8. [Challenger can delete a l2Output which is older than 7 days meaning withdrawals will stop working for even confirmed transaction](https://github.com/sherlock-audit/2023-01-optimism-judging/issues/75)
  9. [Since depositTransaction does not enforce minimum gas limit, it is costly to the sequencer to process thess txs without compensation](https://github.com/sherlock-audit/2023-01-optimism-judging/issues/71)
  10. [Deposits from L1 to L2 using L1CrossDomainMessenger will fail and will not be replayable when L2CrossDomainMessenger is paused](https://github.com/sherlock-audit/2023-01-optimism-judging/issues/57)
  11. [Due to the requirement that reproving can only be done on the same L2 block number, withdrawal transactions can get stuck if output root is reproposed](https://github.com/sherlock-audit/2023-01-optimism-judging/issues/53)
</details>

### 2022-12-15 connext - [Spearbit](https://github.com/spearbit/portfolio/blob/master/pdfs/ConnextNxtp-Spearbit-Security-Review.pdf)
15 Highs, 19 Mediems
  
### 2022-10-28 zkSync V2 - [C4](https://app.sherlock.xyz/audits/contests/63) - [report](https://code4rena.com/reports/2022-10-zksync)

<details><summary>2 Mediums </summary>
  
  1. [diamondCut is not protected in case of governor’s key leakage](https://github.com/code-423n4/2022-10-zksync-findings/issues/46) 
  2. [BLOCK_PERIOD should be set to 12 secs instead of 13, which results in a transaction in the Priority Queue incorrectly expires 5.5 hours earlier than expected.](https://github.com/code-423n4/2022-10-zksync-findings/issues/259)
</details>  

### 2022-10-18 LI.FI - [Spearbit](https://github.com/spearbit/portfolio/blob/master/pdfs/LIFI-Spearbit-Security-Review.pdf)
8 Highs, 19 Mediums  
  
### 2022-10-17 Connext Amarok - [C4](https://code4rena.com/contests/2022-06-connext-amarok-contest) - [report](https://code4rena.com/reports/2022-06-connext)  
  
  
### 2022-10-04 optimismDrippie - [Spearbit](https://github.com/spearbit/portfolio/blob/master/pdfs/Connext-Spearbit-Security-Review.pdf)
1 Medium
  
### 2022-08-30 Connext - [Spearbit](https://github.com/spearbit/portfolio/blob/master/pdfs/Connext-Spearbit-Security-Review.pdf)
4 Critical, 16 Highs, 20 Mediums

### 2022-05-05 LI.FI - [C4](https://code4rena.com/contests/2022-03-lifi-contest) - [report](https://code4rena.com/reports/2022-03-lifinance)
2 Highs, 13 Mediums
  
### 2021-08-30 Connext - [C4](https://code4rena.com/contests/2021-07-connext-contest) - [report](https://code4rena.com/reports/2021-07-connext)
5 Highs, 2 Mediums
