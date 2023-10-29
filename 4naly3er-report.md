# Report


## Gas Optimizations


| |Issue|Instances|
|-|:-|:-:|
| [GAS-1](#GAS-1) | Using bools for storage incurs overhead | 5 |
| [GAS-2](#GAS-2) | Cache array length outside of loop | 7 |
| [GAS-3](#GAS-3) | For Operations that will not overflow, you could use unchecked | 210 |
| [GAS-4](#GAS-4) | Don't initialize variables with default value | 4 |
| [GAS-5](#GAS-5) | Functions guaranteed to revert when called by normal users can be marked `payable` | 3 |
| [GAS-6](#GAS-6) | Use shift Right/Left instead of division/multiplication if possible | 1 |
| [GAS-7](#GAS-7) | Use != 0 instead of > 0 for unsigned integer comparison | 6 |
### <a name="GAS-1"></a>[GAS-1] Using bools for storage incurs overhead
Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas), and to avoid Gsset (20000 gas) when changing from ‘false’ to ‘true’, after having been ‘true’ in the past. See [source](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27).

*Instances (5)*:
```solidity
File: contracts/party/PartyGovernance.sol

141:         mapping(address => bool) hasVoted;

194:     bool public emergencyExecuteDisabled;

206:     mapping(address => bool) public isHost;

```

```solidity
File: contracts/party/PartyGovernanceNFT.sol

59:     mapping(address => bool) public isAuthority;

```

```solidity
File: contracts/utils/Implementation.sol

15:     bool public initialized;

```

### <a name="GAS-2"></a>[GAS-2] Cache array length outside of loop
If not cached, the solidity compiler will always read the length of the array during each iteration. That is, if it is a storage array, this is an extra sload operation (100 additional extra gas for each iteration except for the first) and if it is a memory array, this is an extra mload operation (3 additional gas for each iteration except for the first).

*Instances (7)*:
```solidity
File: contracts/crowdfund/InitialETHCrowdfund.sol

260:         for (uint256 i; i < args.recipients.length; ++i) {

```

```solidity
File: contracts/party/PartyGovernance.sol

305:         for (uint256 i = 0; i < govOpts.hosts.length; ++i) {

```

```solidity
File: contracts/party/PartyGovernanceNFT.sol

102:             for (uint256 i; i < authorities.length; ++i) {

272:         for (uint256 i; i < tokenIds.length; ++i) {

378:             for (uint256 i; i < withdrawTokens.length; ++i) {

391:                 for (uint256 j; j < tokenIds.length; ++j) {

408:             for (uint256 i; i < withdrawTokens.length; ++i) {

```

### <a name="GAS-3"></a>[GAS-3] For Operations that will not overflow, you could use unchecked

*Instances (210)*:
```solidity
File: contracts/crowdfund/InitialETHCrowdfund.sol

4: import { ETHCrowdfundBase } from "./ETHCrowdfundBase.sol";

5: import { ProposalStorage } from "../proposals/ProposalStorage.sol";

5: import { ProposalStorage } from "../proposals/ProposalStorage.sol";

6: import { LibAddress } from "../utils/LibAddress.sol";

6: import { LibAddress } from "../utils/LibAddress.sol";

7: import { LibRawResult } from "../utils/LibRawResult.sol";

7: import { LibRawResult } from "../utils/LibRawResult.sol";

8: import { LibSafeCast } from "../utils/LibSafeCast.sol";

8: import { LibSafeCast } from "../utils/LibSafeCast.sol";

9: import { Party, PartyGovernance } from "../party/Party.sol";

9: import { Party, PartyGovernance } from "../party/Party.sol";

10: import { Crowdfund } from "../crowdfund/Crowdfund.sol";

10: import { Crowdfund } from "../crowdfund/Crowdfund.sol";

11: import { MetadataProvider } from "../renderers/MetadataProvider.sol";

11: import { MetadataProvider } from "../renderers/MetadataProvider.sol";

12: import { IGateKeeper } from "../gatekeepers/IGateKeeper.sol";

12: import { IGateKeeper } from "../gatekeepers/IGateKeeper.sol";

13: import { IGlobals } from "../globals/IGlobals.sol";

13: import { IGlobals } from "../globals/IGlobals.sol";

14: import { IERC721 } from "../tokens/IERC721.sol";

14: import { IERC721 } from "../tokens/IERC721.sol";

173:                 0, // Mint a new party card for the contributor.

173:                 0, // Mint a new party card for the contributor.

211:         for (uint256 i; i < numContributions; ++i) {

211:         for (uint256 i; i < numContributions; ++i) {

212:             ethAvailable -= args.values[i];

260:         for (uint256 i; i < args.recipients.length; ++i) {

260:         for (uint256 i; i < args.recipients.length; ++i) {

268:             valuesSum += args.values[i];

357:         for (uint256 i; i < numRefunds; ++i) {

357:         for (uint256 i; i < numRefunds; ++i) {

377:         uint256 authoritiesLength = opts.authorities.length + 1;

379:         for (uint i = 0; i < authoritiesLength - 1; ++i) {

379:         for (uint i = 0; i < authoritiesLength - 1; ++i) {

379:         for (uint i = 0; i < authoritiesLength - 1; ++i) {

382:         authorities[authoritiesLength - 1] = address(this);

```

```solidity
File: contracts/party/PartyGovernance.sol

4: import { ITokenDistributor } from "../distribution/ITokenDistributor.sol";

4: import { ITokenDistributor } from "../distribution/ITokenDistributor.sol";

5: import { ReadOnlyDelegateCall } from "../utils/ReadOnlyDelegateCall.sol";

5: import { ReadOnlyDelegateCall } from "../utils/ReadOnlyDelegateCall.sol";

6: import { IERC721 } from "../tokens/IERC721.sol";

6: import { IERC721 } from "../tokens/IERC721.sol";

7: import { IERC20 } from "../tokens/IERC20.sol";

7: import { IERC20 } from "../tokens/IERC20.sol";

8: import { IERC721Receiver } from "../tokens/IERC721Receiver.sol";

8: import { IERC721Receiver } from "../tokens/IERC721Receiver.sol";

9: import { ERC1155TokenReceiverBase } from "../vendor/solmate/ERC1155.sol";

9: import { ERC1155TokenReceiverBase } from "../vendor/solmate/ERC1155.sol";

9: import { ERC1155TokenReceiverBase } from "../vendor/solmate/ERC1155.sol";

10: import { LibERC20Compat } from "../utils/LibERC20Compat.sol";

10: import { LibERC20Compat } from "../utils/LibERC20Compat.sol";

11: import { LibRawResult } from "../utils/LibRawResult.sol";

11: import { LibRawResult } from "../utils/LibRawResult.sol";

12: import { LibSafeCast } from "../utils/LibSafeCast.sol";

12: import { LibSafeCast } from "../utils/LibSafeCast.sol";

13: import { IERC4906 } from "../utils/IERC4906.sol";

13: import { IERC4906 } from "../utils/IERC4906.sol";

14: import { IGlobals } from "../globals/IGlobals.sol";

14: import { IGlobals } from "../globals/IGlobals.sol";

15: import { LibGlobals } from "../globals/LibGlobals.sol";

15: import { LibGlobals } from "../globals/LibGlobals.sol";

16: import { IProposalExecutionEngine } from "../proposals/IProposalExecutionEngine.sol";

16: import { IProposalExecutionEngine } from "../proposals/IProposalExecutionEngine.sol";

17: import { LibProposal } from "../proposals/LibProposal.sol";

17: import { LibProposal } from "../proposals/LibProposal.sol";

18: import { ProposalStorage } from "../proposals/ProposalStorage.sol";

18: import { ProposalStorage } from "../proposals/ProposalStorage.sol";

19: import { Implementation } from "../utils/Implementation.sol";

19: import { Implementation } from "../utils/Implementation.sol";

20: import { Party } from "./Party.sol";

125:         uint96 votes; // -1 == vetoed

125:         uint96 votes; // -1 == vetoed

125:         uint96 votes; // -1 == vetoed

305:         for (uint256 i = 0; i < govOpts.hosts.length; ++i) {

305:         for (uint256 i = 0; i < govOpts.hosts.length; ++i) {

374:         return (snap.isDelegated ? 0 : snap.intrinsicVotingPower) + snap.delegatedVotingPower;

434:             uint256 mid = (low + high) / 2;

434:             uint256 mid = (low + high) / 2;

440:                 low = mid + 1;

445:         return high == 0 ? type(uint256).max : high - 1;

468:             --numHosts;

468:             --numHosts;

558:         proposalId = ++lastProposalId;

558:         proposalId = ++lastProposalId;

634:         uint96 votingPower = getVotingPowerAt(msg.sender, values.proposedTime - 1, snapIndex);

635:         values.votes += votingPower;

637:             ++values.numHostsAccepted;

637:             ++values.numHostsAccepted;

809:             uint256 cancelTime = values.executedTime + cancelDelay;

918:                 (hintIndex == snapsLength - 1 || snaps[hintIndex + 1].timestamp > timestamp)

918:                 (hintIndex == snapsLength - 1 || snaps[hintIndex + 1].timestamp > timestamp)

939:         _adjustVotingPower(from, -powerI192, address(0));

956:             intrinsicVotingPower: (oldSnap.intrinsicVotingPower.safeCastUint96ToInt192() +

989:                 delegatedVotingPower: oldDelegateSnap.delegatedVotingPower -

1002:             uint96 newDelegateDelegatedVotingPower = newDelegateSnap.delegatedVotingPower +

1008:                 newDelegateDelegatedVotingPower -= oldSnap.intrinsicVotingPower;

1034:             VotingPowerSnapshot memory lastSnap = voterSnaps[n - 1];

1036:                 voterSnaps[n - 1] = snap;

1049:             snap = voterSnaps[n - 1];

1090:             if (pv.passedTime + gv.executionDelay <= t) {

1105:         if (pv.proposedTime + gv.voteDuration <= t) {

1115:         uint256 acceptanceRatio = (totalVotes * 1e4) / totalVotingPower;

1115:         uint256 acceptanceRatio = (totalVotes * 1e4) / totalVotingPower;

1134:         return (uint256(voteCount) * 1e4) / uint256(totalVotingPower) >= uint256(passThresholdBps);

1134:         return (uint256(voteCount) * 1e4) / uint256(totalVotingPower) >= uint256(passThresholdBps);

```

```solidity
File: contracts/party/PartyGovernanceNFT.sol

4: import "../utils/LibSafeCast.sol";

4: import "../utils/LibSafeCast.sol";

5: import "../utils/LibAddress.sol";

5: import "../utils/LibAddress.sol";

6: import "openzeppelin/contracts/interfaces/IERC2981.sol";

6: import "openzeppelin/contracts/interfaces/IERC2981.sol";

6: import "openzeppelin/contracts/interfaces/IERC2981.sol";

7: import "../globals/IGlobals.sol";

7: import "../globals/IGlobals.sol";

8: import "../tokens/IERC721.sol";

8: import "../tokens/IERC721.sol";

9: import "../vendor/solmate/ERC721.sol";

9: import "../vendor/solmate/ERC721.sol";

9: import "../vendor/solmate/ERC721.sol";

10: import "./PartyGovernance.sol";

11: import "../renderers/RendererStorage.sol";

11: import "../renderers/RendererStorage.sol";

34:     uint40 private constant ENABLE_RAGEQUIT_PERMANENTLY = 0x6b5b567bfe; // uint40(uint256(keccak256("ENABLE_RAGEQUIT_PERMANENTLY")))

34:     uint40 private constant ENABLE_RAGEQUIT_PERMANENTLY = 0x6b5b567bfe; // uint40(uint256(keccak256("ENABLE_RAGEQUIT_PERMANENTLY")))

35:     uint40 private constant DISABLE_RAGEQUIT_PERMANENTLY = 0xab2cb21860; // uint40(uint256(keccak256("DISABLE_RAGEQUIT_PERMANENTLY")))

35:     uint40 private constant DISABLE_RAGEQUIT_PERMANENTLY = 0xab2cb21860; // uint40(uint256(keccak256("DISABLE_RAGEQUIT_PERMANENTLY")))

102:             for (uint256 i; i < authorities.length; ++i) {

102:             for (uint256 i; i < authorities.length; ++i) {

125:         return ""; // Just to make the compiler happy.

125:         return ""; // Just to make the compiler happy.

131:         return ""; // Just to make the compiler happy.

131:         return ""; // Just to make the compiler happy.

138:         return (address(0), 0); // Just to make the compiler happy.

138:         return (address(0), 0); // Just to make the compiler happy.

157:             totalVotingPower == 0 ? 0 : (votingPowerByTokenId[tokenId] * 1e18) / totalVotingPower;

157:             totalVotingPower == 0 ? 0 : (votingPowerByTokenId[tokenId] * 1e18) / totalVotingPower;

179:         if (totalVotingPower != 0 && totalVotingPower - mintedVotingPower_ < votingPower_) {

181:                 votingPower_ = totalVotingPower - mintedVotingPower_;

187:             tokenId = ++tokenCount;

187:             tokenId = ++tokenCount;

189:         mintedVotingPower += votingPower_;

216:         if (totalVotingPower != 0 && totalVotingPower - mintedVotingPower_ < votingPower) {

218:                 votingPower = totalVotingPower - mintedVotingPower_;

223:         mintedVotingPower += votingPower;

224:         uint256 newIntrinsicVotingPower = votingPowerByTokenId[tokenId] + votingPower;

238:         mintedVotingPower -= votingPower;

239:         votingPowerByTokenId[tokenId] -= votingPower;

241:         _adjustVotingPower(ownerOf(tokenId), -votingPower.safeCastUint96ToInt192(), address(0));

249:         _getSharedProposalStorage().governanceValues.totalVotingPower += votingPower;

257:         _getSharedProposalStorage().governanceValues.totalVotingPower -= votingPower;

272:         for (uint256 i; i < tokenIds.length; ++i) {

272:         for (uint256 i; i < tokenIds.length; ++i) {

290:             totalVotingPowerBurned += votingPower;

295:             _adjustVotingPower(owner, -votingPower.safeCastUint96ToInt192(), address(0));

304:         mintedVotingPower -= totalVotingPowerBurned;

378:             for (uint256 i; i < withdrawTokens.length; ++i) {

378:             for (uint256 i; i < withdrawTokens.length; ++i) {

391:                 for (uint256 j; j < tokenIds.length; ++j) {

391:                 for (uint256 j; j < tokenIds.length; ++j) {

393:                     withdrawAmounts[i] += (balance * getVotingPowerShareOf(tokenIds[j])) / 1e18;

393:                     withdrawAmounts[i] += (balance * getVotingPowerShareOf(tokenIds[j])) / 1e18;

393:                     withdrawAmounts[i] += (balance * getVotingPowerShareOf(tokenIds[j])) / 1e18;

404:             _getSharedProposalStorage().governanceValues.totalVotingPower -= totalVotingPowerBurned;

408:             for (uint256 i; i < withdrawTokens.length; ++i) {

408:             for (uint256 i; i < withdrawTokens.length; ++i) {

413:                 uint256 fee = (amount * feeBps_) / 1e4;

413:                 uint256 fee = (amount * feeBps_) / 1e4;

416:                     amount -= fee;

499:         assert(false); // Will not be reached.

499:         assert(false); // Will not be reached.

```

```solidity
File: contracts/proposals/ProposalExecutionEngine.sol

4: import "../utils/Implementation.sol";

4: import "../utils/Implementation.sol";

5: import "../utils/LibRawResult.sol";

5: import "../utils/LibRawResult.sol";

6: import "../globals/IGlobals.sol";

6: import "../globals/IGlobals.sol";

7: import { IERC1271 } from "openzeppelin/contracts/interfaces/IERC1271.sol";

7: import { IERC1271 } from "openzeppelin/contracts/interfaces/IERC1271.sol";

7: import { IERC1271 } from "openzeppelin/contracts/interfaces/IERC1271.sol";

9: import "./IProposalExecutionEngine.sol";

10: import "./ListOnOpenseaProposal.sol";

11: import "./ListOnOpenseaAdvancedProposal.sol";

12: import "./ListOnZoraProposal.sol";

13: import "./FractionalizeProposal.sol";

14: import "./ArbitraryCallsProposal.sol";

15: import "./ProposalStorage.sol";

16: import "./DistributeProposal.sol";

17: import "./AddAuthorityProposal.sol";

18: import "./OperatorProposal.sol";

19: import { SetSignatureValidatorProposal } from "./SetSignatureValidatorProposal.sol";

20: import { SetGovernanceParameterProposal } from "./SetGovernanceParameterProposal.sol";

```

```solidity
File: contracts/proposals/ProposalStorage.sol

4: import "./IProposalExecutionEngine.sol";

5: import "../utils/LibRawResult.sol";

5: import "../utils/LibRawResult.sol";

```

```solidity
File: contracts/proposals/SetGovernanceParameterProposal.sol

4: import { ProposalStorage } from "./ProposalStorage.sol";

5: import { IProposalExecutionEngine } from "./IProposalExecutionEngine.sol";

```

```solidity
File: contracts/proposals/SetSignatureValidatorProposal.sol

4: import { IERC1271 } from "openzeppelin/contracts/interfaces/IERC1271.sol";

4: import { IERC1271 } from "openzeppelin/contracts/interfaces/IERC1271.sol";

4: import { IERC1271 } from "openzeppelin/contracts/interfaces/IERC1271.sol";

5: import { IProposalExecutionEngine } from "./IProposalExecutionEngine.sol";

```

```solidity
File: contracts/signature-validators/OffChainSignatureValidator.sol

4: import { IERC1271 } from "openzeppelin/contracts/interfaces/IERC1271.sol";

4: import { IERC1271 } from "openzeppelin/contracts/interfaces/IERC1271.sol";

4: import { IERC1271 } from "openzeppelin/contracts/interfaces/IERC1271.sol";

5: import { Strings } from "openzeppelin/contracts/utils/Strings.sol";

5: import { Strings } from "openzeppelin/contracts/utils/Strings.sol";

5: import { Strings } from "openzeppelin/contracts/utils/Strings.sol";

6: import { Party } from "../party/Party.sol";

6: import { Party } from "../party/Party.sol";

59:         uint96 signerVotingPowerBps = party.getVotingPowerAt(signer, uint40(block.timestamp)) *

74:                 signerVotingPowerBps / totalVotingPower >= thresholdBps)

```

### <a name="GAS-4"></a>[GAS-4] Don't initialize variables with default value

*Instances (4)*:
```solidity
File: contracts/crowdfund/InitialETHCrowdfund.sol

379:         for (uint i = 0; i < authoritiesLength - 1; ++i) {

```

```solidity
File: contracts/party/PartyGovernance.sol

305:         for (uint256 i = 0; i < govOpts.hosts.length; ++i) {

432:         uint256 low = 0;

1054:         uint256 flags = 0;

```

### <a name="GAS-5"></a>[GAS-5] Functions guaranteed to revert when called by normal users can be marked `payable`
If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.

*Instances (3)*:
```solidity
File: contracts/party/PartyGovernance.sol

855:     function disableEmergencyExecute() external onlyPartyDaoOrHost {

```

```solidity
File: contracts/party/PartyGovernanceNFT.sol

479:     function addAuthority(address authority) external onlySelf {

```

```solidity
File: contracts/proposals/ProposalExecutionEngine.sol

207:     function cancelProposal(uint256 proposalId) external onlyDelegateCall {

```

### <a name="GAS-6"></a>[GAS-6] Use shift Right/Left instead of division/multiplication if possible

*Instances (1)*:
```solidity
File: contracts/party/PartyGovernance.sol

434:             uint256 mid = (low + high) / 2;

```

### <a name="GAS-7"></a>[GAS-7] Use != 0 instead of > 0 for unsigned integer comparison

*Instances (6)*:
```solidity
File: contracts/crowdfund/InitialETHCrowdfund.sol

141:         if (initialContribution > 0) {

224:         if (ethAvailable > 0) payable(msg.sender).transfer(ethAvailable);

331:         if (amount > 0) {

```

```solidity
File: contracts/party/PartyGovernance.sol

1126:         return snapshotNumHosts > 0 && snapshotNumHosts == numHostsAccepted;

```

```solidity
File: contracts/party/PartyGovernanceNFT.sol

415:                 if (fee > 0) {

426:                 if (amount > 0) {

```


## Low Issues


| |Issue|Instances|
|-|:-|:-:|
| [L-1](#L-1) | Empty Function Body - Consider commenting why | 1 |
| [L-2](#L-2) | Initializers could be front-run | 6 |
| [L-3](#L-3) | Unsafe ERC20 operation(s) | 2 |
| [L-4](#L-4) | Use of ecrecover is susceptible to signature malleability | 1 |
### <a name="L-1"></a>[L-1] Empty Function Body - Consider commenting why

*Instances (1)*:
```solidity
File: contracts/crowdfund/InitialETHCrowdfund.sol

99:     constructor(IGlobals globals) ETHCrowdfundBase(globals) {}

```

### <a name="L-2"></a>[L-2] Initializers could be front-run
Initializers could be front-run, allowing an attacker to either set their own values, take ownership of the contract, and in the best case forcing a re-deployment

*Instances (6)*:
```solidity
File: contracts/crowdfund/InitialETHCrowdfund.sol

108:     function initialize(

118:         _initialize(

```

```solidity
File: contracts/party/PartyGovernance.sol

270:     function _initialize(

```

```solidity
File: contracts/party/PartyGovernanceNFT.sol

81:     function _initialize(

92:         PartyGovernance._initialize(

```

```solidity
File: contracts/proposals/ProposalExecutionEngine.sol

123:     function initialize(

```

### <a name="L-3"></a>[L-3] Unsafe ERC20 operation(s)

*Instances (2)*:
```solidity
File: contracts/crowdfund/InitialETHCrowdfund.sol

224:         if (ethAvailable > 0) payable(msg.sender).transfer(ethAvailable);

```

```solidity
File: contracts/party/PartyGovernanceNFT.sol

454:         super.transferFrom(owner, to, tokenId);

```

### <a name="L-4"></a>[L-4] Use of ecrecover is susceptible to signature malleability
The built-in EVM precompile ecrecover is susceptible to signature malleability, which could lead to replay attacks.Consider using OpenZeppelin’s ECDSA library instead of the built-in function.

*Instances (1)*:
```solidity
File: contracts/signature-validators/OffChainSignatureValidator.sol

58:         address signer = ecrecover(hash, v, r, s);

```

