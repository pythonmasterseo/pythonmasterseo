Security Advisory: idOS Sale Withdrawal Logic Flaw
Auditor: pythonmasterseo Date: 12  March  2026 Target: idOS Network (Sale Contract) Severity: HighStatus: Fixed

1. Executive Summary
A logic vulnerability was identified in the Sale.sol contract of the idOS network. The withdraw function lacked a check to verify if the minTarget (Soft Cap) was reached. This allowed an admin to withdraw user funds from a failed sale, making refunds impossible for users.

2. Vulnerability Details
Title: Missing minTarget Validation in withdraw Function

Description:The sale contract implements a Soft Cap mechanism (minTarget). If the total raised funds are less than this target, the sale is considered failed, and users should be able to claim refunds.

However, the withdraw function allowed the admin to transfer all payment tokens (USDC) out of the contract without checking if totalUncappedAllocations >= minTarget.

Vulnerable Code:

function withdraw() external onlyRole(DEFAULT_ADMIN_ROLE) {    require(block.timestamp > end, "sale not ended yet");    require(!withdrawn, "already withdrawn");    require(custodian != address(0), CustodianNotSet());    // MISSING CHECK: require(totalUncappedAllocations >= minTarget, ...);    withdrawn = true;    // ... transfer funds to admin}
Impact:
If a sale failed to reach the minimum target, the admin could drain the contract balance. Users would have a valid refundAmount > 0, but the contract would hold 0 USDC, resulting in a complete loss of funds for investors.

3. Recommended Fix
Add a requirement to the withdraw function to ensure the sale was successful before allowing the admin to withdraw.

Fixed Code:

solidity

require(totalUncappedAllocations >= minTarget, "minTarget not reached");
4. Resolution & Timeline
Initial Discovery: Found vulnerability in public repository.
Responsible Disclosure: Details were redacted from public GitHub Issues. Encrypted report sent to Julio Santos (Co-founder) via PGP.
Team Response: The team acknowledged the vulnerability and promptly implemented the fix.
Fix Verified: The fix was merged in commit cbf79fd.
Note: No bug bounty was awarded as the specific contract was for a past sale event with no active funds at risk. The disclosure was handled professionally by the idOS team.

5. References
GitHub Issue: #25   https://github.com/idos-network/idos-sale/issues/25
Fix Commit: cbf79fd   https://github.com/idos-network/idos-sale/commit/cbf79fd5c4088a642c0d28b1fa69f4653d23ccdc

Keywords & Tags
web3 security smart contract audit solidity bug bounty vulnerability report idos network pythonmasterseo crypto security deFi audit logic flaw soft cap responsible disclosure pgp encryption
