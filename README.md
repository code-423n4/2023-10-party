# Party Protocol Audit Details

- Total Prize Pool: $60,500 USDC
  - HM awards: $41,250 USDC
  - Analysis awards: $2,500 USDC
  - QA awards: $1,250 USDC
  - Bot Race awards: $3,750 USDC
  - Gas awards: $1,250 USDC
  - Judge awards: $6,000 USDC
  - Lookout awards: $4,000 USDC
  - Scout awards: $500 USDC
- Join [C4 Discord](https://discord.gg/code4rena) to register
- Submit findings [using the C4 form](https://code4rena.com/contests/2023-10-party-protocol/submit)
- [Read our guidelines for more details](https://docs.code4rena.com/roles/wardens)
- Starts October 31, 2023 20:00 UTC
- Ends November 10, 2023 20:00 UTC

## Overview

The Party Protocol aims to be a standard for group coordination, providing on-chain functionality for essential group behaviors:

1. **Formation:** Assembling a group and combining resources.
2. **Coordination:** Making decisions and taking action together.
3. **Distribution:** Sharing resources with group members.

This next release aims to expand what Parties can do, introducing the ability for Party to sign messages via ERC1271, allowing governance settings to be changed after initialization, extending capabilities for the authority role, and more. More specific details about all changes can be found below.

Before continuing, it is highly recommended that you read the following pages from the [docs](https://docs.partydao.org):

- [Introduction](https://docs.partydao.org/docs/Introduction)
- [Party: Overview](https://docs.partydao.org/docs/partys/Overview)
- [Crowdfund: Overview](https://docs.partydao.org/docs/crowdfund/Overview)
- [Party](https://docs.partydao.org/docs/partys/Party)

## Changelog

| Change                                                 | Motivation                                                                                         |
| ------------------------------------------------------ | -------------------------------------------------------------------------------------------------- |
| Add functionality for Parties to sign messages via ERC1271 | Parties want to use web-apps that require singing a message from the address to access them. Additionally, this change allows passing proposals to sign signatures used in on-chain actions.  |
| Add functionality for Parties to change governance settings | Previously, governance settings were not changeable once starting the party. This created issues since parties could not adjust their settings to account for their change over time |
| Add functionality to skip proposal’s veto period if all hosts have accepted it | Once all hosts accept a proposal, the veto period is skipped. This allows for parties to move faster. |
| Add functionality for authorities to decrease the voting power of a specific member with `PartyGovernanceNFT.decreaseVotingPower()` | We have ideas for new usecases for parties that would take advantage of a more fluid intrinsic voting power controlled by an external contract |
| Add functionality for authorities to decrease the total voting power of a Party with `PartyGovernanceNFT.decreaseTotalVotingPower()` | Given that authorities now have the ability to burn party cards even after finalization, the total voting power must by adjustable downwards by authorities. |
| Update contract to ERC-1167 proxies from previous custom proxy implementation | Using the `ERC-1167 proxies` allows for better integration with services such as auto-verification on etherscan |
| Update `InitialETHCrowdfund` to allow setting additional authorities for a newly created Party. | To enable parties to immediately have the ability to interact with the `AddPartyCardsAuthority` as well as other authorities coming in the future |
| Update `governanceValues` to be stored in `ProposalStorage` instead of `Party` | To allow the ProposalExecutionEngine to access it  to enable Parties to modify governance settings. |
| Update `PartyGovernanceNFT.burn()` to allow authorities to burn any Party card even after governance has started | To expand the variety of modules and extensions that could be built upon the Party Protocol leveraging the capabilities of the authority. |
| Update to inline modifiers to reduce contract size for `Party` | Necessary to meet the contract size limit. |


## Links

- **Previous Audits:** [2023-05-party](https://github.com/code-423n4/2023-05-party), [2023-04-party](https://github.com/code-423n4/2023-04-party), [2022-09-party](https://github.com/code-423n4/2022-09-party)
- **Documentation:** [https://docs.partydao.org](https://docs.partydao.org/)
- **Website:** https://www.party.app/
- **Twitter:** [https://twitter.com/prtyDAO](https://twitter.com/prtyDAO?ref_src=twsrc%5Egoogle%7Ctwcamp%5Eserp%7Ctwgr%5Eauthor)
- **Discord:** https://discord.gg/pMPcwMDtyR

## Scope

| Contract                                                                                                | SLOC | Purpose                | Libraries used                                           |
| ------------------------------------------------------------------------------------------------------- | ---- | ---------------------- | -------------------------------------------------------- |
| [contracts/crowdfund/InitialETHCrowdfund.sol](https://github.com/code-423n4/2023-10-party/blob/main/contracts/crowdfund/InitialETHCrowdfund.sol) | 300  | This contract is a crowdfund for creating a new ETH party | |
| [contracts/crowdfund/ETHCrowdfundBase.sol](contracts/crowdfund/ETHCrowdfundBase.sol) | 251 | The base contract for `InitialETHCrowdfund` | |
| [contracts/party/PartyGovernance.sol](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernance.sol) | 778  | This contract is inherited by the `Party` contract--the core contract in the protocol. This contract has the governance logic for parties | |
| [contracts/party/PartyGovernanceNFT.sol](https://github.com/code-423n4/2023-10-party/blob/main/contracts/party/PartyGovernanceNFT.sol) | 313  | This contract is inherited by the `Party` contract--the core contract in the protocol. This contract has the token logic for parties | [`solmate/ERC721`](https://github.com/PartyDAO/party-protocol/blob/main/contracts/vendor/solmate/ERC721.sol), [`openzeppelin/contracts/interfaces/IERC2981.sol`](https://openzeppelin.com/contracts/) |
| [contracts/proposals/ProposalExecutionEngine.sol](https://github.com/code-423n4/2023-10-party/blob/main/contracts/proposals/ProposalExecutionEngine.sol) | 244  | This contract is delegate called from parties for execution logic involved with proposal. Parties also have a fallback that does a static delegate call to the `ProposalExecutionEngine` |  |
| [contracts/proposals/ProposalStorage.sol](https://github.com/code-423n4/2023-10-party/blob/main/contracts/proposals/ProposalStorage.sol) | 47  | Shared storage storage bucket that is accessed in multiple contracts | |
| [contracts/proposals/SetGovernanceParameterProposal.sol](https://github.com/code-423n4/2023-10-party/blob/main/contracts/proposals/SetGovernanceParameterProposal.sol) | 55  | A new proposal type that allows setting governance parameters for the party | |
| [contracts/proposals/SetSignatureValidatorProposal.sol](https://github.com/code-423n4/2023-10-party/blob/main/contracts/proposals/SetSignatureValidatorProposal.sol) | 40  | A new proposal type that allows setting a signature validator for a given signature hash or simple validating the hash | |
| [contracts/signature-validators/OffChainSignatureValidator.sol](https://github.com/code-423n4/2023-10-party/blob/main/contracts/signature-validators/OffChainSignatureValidator.sol) | 60  | Validator contract that reconstructs a message and ensures its a plaintext message. It then returns valid if the signer has sufficient voting power or membership in the party | |
| [contracts/utils/Implementation.sol](https://github.com/code-423n4/2023-10-party/blob/main/contracts/utils/Implementation.sol) | 32  | A contract that provides helper function for implementation contracts used from a proxy | |


## Out of Scope

The file `contracts/crowdfund/ReraiseETHCrowdfund.sol` is out of scope. All files located in `contracts/renderers` are out of scope. All files not located within the `contracts` folder are also out of scope.

## Automated Findings / Publicly Known Issues

The 4naly3er report can be found [here](https://github.com/code-423n4/2023-10-party/blob/main/4naly3er-report.md).

Automated findings output for the audit can be found [here](https://github.com/code-423n4/2023-10-party/blob/main/bot-report.md) within 24 hours of audit opening.

_Note for C4 wardens: Anything included in the 4naly3er **or** the automated findings output is considered a publicly known issue and is ineligible for awards._

All the following are known issues or risks deemed acceptable that shouldn't lead to a valid finding:

- In some cases members of a Party may not be able to claim from the `TokenDistributor` although they should be eligible. In all known cases, it will have been triggered by the addition of new members and a change in the total voting power of the Party. We understand the unexpected results and are willing to accept the risk, given their low likelihood and the constraints for this release. Some known situations with unexpected outcomes:
  - Party cards are burned and then added resulting in totalVotingPower <= totalVotingPower when the distribution was created.
  - NFT parties have unclaimed Party cards from original crowdfund, a distribution is created, and then new party cards are added preventing the members with unclaimed cards from claiming from the distribution.
  - Multiple Party cards (A,B,C) are added and a new distribution is made on the `safeTransfer` callback (or `safeMint` callback if these cards being minted) of the first new party card (A). Subsequently, more party cards are added (D,E). B and C are no longer able to claim the distribution
- Authorities have “root user privileges” over a Party and is it well aware that they can exploit or break Parties in a wide variety of ways, especially if they are set to a malicious EOA or unsafe smart contract. This is why authorities are almost always smart contracts that are highly audited and trusted, and are NOT expected to be EOAs.
- Hosts have unilateral veto power on any proposal. Hosts can block a governance party in this way.
- It is possible that someone could manipulate parties by contributing ETH and then buying their NFT that they own. This is known and not considered a bug or a valid finding by the team.
- If a party were to list a malicious NFT (eg. reverts on transfer after listing), somewhere along the way it may break the proposal flow and put the party in a stuck state until `cancelDelay` is reached. While it is annoying, we do not consider it serious because (1) the proposal can always be canceled and (2) it is unlikely a malicious NFT will have a market.
- There are emergency admin recovery functions on Parties and crowdfunds callable by the multisig that can execute arbitrary bytecode, though party hosts can disable them.
- Canceling an `InProgress` proposal can leave the governance party in a vulnerable or undesirable state because there is no cleanup logic run during a cancel. For example, if a party cancels a Zora proposal while the Zora auction is active, the party will no longer possess the NFT (Zora is custodial), and must either wait for the auction to conclude or execute an arbitrary call to cancel the action directly on Zora in order to retrieve it.

In addition, any of the “Sponsor Acknowledged” issues from past audits:

- [2023-05-party-findings](https://github.com/code-423n4/2023-05-party-findings/issues?q=label%3A%22sponsor+acknowledged%22+label%3A%222+%28Med+Risk%29%22%2C%223+%28High+Risk%29%22+)
- [2023-04-party-findings](https://github.com/code-423n4/2023-04-party-findings/issues?q=label%3A%22sponsor+acknowledged%22+label%3A%222+%28Med+Risk%29%22%2C%223+%28High+Risk%29%22+)
- [2022-09-party-findings](<https://github.com/code-423n4/2022-09-party-findings/issues?q=label%3A"sponsor+acknowledged"+label%3A"2+(Med+Risk)"%2C"3+(High+Risk)"+>)

## Areas of Interest

A common theme we’ve noticed through our past C4 audits (we’ve had 4 now) is that the top findings were exploits introduced in one part of the protocol caused by a code change made in another related but often unsuspecting part of the protocol.

As the focus on this release was the `Party` contract, and all the contracts it inherits, we would _highly_ recommend you at least be aware of each of these related contracts:

- `TokenDistributor` (see [here](#automated-findings--publicly-known-issues) for known issues)
- `InitialETHCrowdfund`
- All gatekeeper contracts
- All operator contracts
- All proposal contracts

If you want to take it a step further, you might ask yourself whether changes in one part of the codebase that effect another in turn introduce any unexpected interactions in yet another part of the codebase that interacts with the part before.

In addition, it may we worthwhile to check all voting power accounting is correct and items owned by a Party (e.g. ERC721s, ERC20s) are not ruggable.

## Additional Context

-	Parties on our protocol may interact with any ERC20 tokens.
-	In terms of ERC721, our protocol should be able to interact with any ERC721 that a group of individuals may find it reasonable and worthwhile to contribute ETH to acquire, like NFTs that have sufficient liquidity listed on platforms like OpenSea. This would not, for example, include unknown malicious ERC721 implementations with no market.
-	The code deployment of the protocol is targeted for two blockchains: Ethereum Mainnet and Base Mainnet.
- The protocol includes two primary trusted roles, each with unique capabilities and responsibilities:
  -	**Hosts**: A role in the Party for trusted addresses that grants the ability to unilaterally veto proposals in the Party and configure Rage Quit. Each Host may or may not be a member (i.e. have non-zero voting power in the Party).
  -	**Authorities**: A privileged role with root access to the Party. Can perform sensitive operations like minting and burning new memberships in a Party and updating the voting power of members. Authorities are expected to be trusted smart contracts, not EOAs.
-	DOS attacks are not as relevant to our protocol. If there's a scenario where a DOS can occur, a minimum duration of 15 minutes should be demonstrated for a finding to be considered. However, this duration is somewhat arbitrary and findings may make arguments lesser durations if they can be justified.
-	The protocol is designed to comply with several Ethereum Improvement Proposals (EIPs). Following contracts should be in compliance:
  - `PartyGovernance`: Should comply with `ERC4906`
  - `PartyGovernance`: Should comply with `ERC165`
  - `PartyGovernanceNFT`: Should comply with `ERC2981`
  - `PartyGovernanceNFT`: Should comply with `ERC721`
  - `PartyGovernanceNFT`: Should comply with `ERC165`
  - `ProposalExecutionEngine`: Should comply with `ERC1271`
  - `OffChainSignatureValidator`: Should comply with `ERC1271`

## Scoping Details

```
- If you have a public code repo, please share it here:  https://github.com/PartyDAO/party-protocol
- How many contracts are in scope?:   8
- Total SLoC for these contracts?:  1563
- How many external imports are there?: 4
- How many separate interfaces and struct definitions are there for the contracts within scope?:  19
- Does most of your code generally use composition or inheritance?:   Inheritance
- How many external calls?:   7
- What is the overall line coverage percentage provided by your tests?: 62
- Is this an upgrade of an existing system?: True;
  - 1. Add EIP-1271 signing capability to parties.
  - 2. Skip veto period for a proposal when all hosts accept.
  - 3. Add the ability for governance parameters to be set in a proposal.
  - 4. Use ERC-1167 minimal proxies.
  - 5. Expand the abilities of the authority role.
- Check all that apply (e.g. timelock, NFT, AMM, ERC20, rollups, etc.): ERC-20 Token, Uses L2, NFT, Timelock function
- Is there a need to understand a separate part of the codebase / get context in order to audit this part of the protocol?: False
- Please describe required context:
- Does it use an oracle?:  No
- Describe any novel or unique curve logic or mathematical models your code uses: No novel
- Is this either a fork of or an alternate implementation of another project?:   False
- Does it use a side-chain?:
- Describe any specific areas you would like addressed:
```

## Quickstart Command

Here's a one-liner to immediately get started with the codebase. It will clone the project, build it, run every test, and display gas reports:

```jsx
export ETH_RPC_URL='<your_alchemy_mainnet_url_here>' && rm -Rf 2023-10-party || true && git clone https://github.com/code-423n4/2023-10-party -j8 --recurse-submodules && cd 2023-10-party && foundryup && forge install && yarn install && forge test -f $ETH_RPC_URL --gas-report
```

## Testing

### Run tests (except fork tests):

```bash
forge test -vv
# If you want gas reports:
forge test --gas-report -vv
```

### Run forked tests

```bash
forge test --mc ForkedTest --fork-url $YOUR_RPC_URL -vv
```

### Run all tests

```bash
forge test --fork-url $YOUR_RPC_URL -vv
```
