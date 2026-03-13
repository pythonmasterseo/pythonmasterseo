Logic Vulnerability Review: WORM Privacy Protocol
Auditor: pythonmasterseo
Date: 13 March 2026
Target: WORM Privacy
Type: Logic Flaw / Privacy
Severity: High
Status: Reported to Team (Pending Response)

1. Executive Summary
During a review of the WORM privacy protocol, a critical logic flaw was identified in the spendCoin function. This vulnerability affects the core ownership mechanism of BETH coins.

Note: The vulnerable codebase was previously audited by Hashlock and yAudit. This finding highlights a logic gap that appears to have been overlooked in previous security reviews.

2. Vulnerability Overview
Component: WORM.sol (spendCoin function)

Summary:A logic flaw prevents the correct update of the coinOwner mapping during the spending process. This breaks the intended off-chain transfer model and can lead to fund loss for the new owner if a private key sale occurs.

Root Cause:The contract updates the coin source (coinSource) but fails to update the owner reference (coinOwner) for the new remaining coin.

3. Responsible Disclosure
To protect user funds and the integrity of the privacy protocol, I have redacted the specific technical details and Proof of Concept.

A full report has been sent to the lead maintainer (keyvankambakhsh@gmail.com).

[Technical details and Proof of Concept will be published here upon team confirmation or fix]


4. Keywords
web3 security worm protocol privacy smart contract audit logic flaw hashlock yaudit pythonmasterseo `zero knowledge
