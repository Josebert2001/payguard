# PayGuard: Nigerian product feasibility

Research date: 14 November 2026.

## Assessment

**Conditional go for customer validation and a controlled pilot. Production readiness and willingness to pay remain unproven.**

PayGuard could become a Nigerian commerce fraud product. The strongest initial proposition is screening seller conversations and recipient details for a marketplace or commerce operator that can act before payment. Keep the initial use case narrow: for example, a platform handling purchases of used phones and electronics, provided interviews establish sufficient fraud exposure and access to evidence.

The durable advantage would need to come from verified local outcomes, useful integrations, and measurable improvements to decisions. AI explanations and a public account blacklist are readily replicated. Start with a partner whose workflow supplies both transaction context and confirmed outcomes.

This assessment combines repository inspection and public-source research. It does not include customer interviews, supplier quotations, a penetration test, independent model evaluation, or a definitive regulatory classification. Competitor features below are vendor descriptions; their coverage, accuracy, customers, and commercial availability were not independently tested. No application code was changed or deployed for this assessment.

## Evidence of demand, with limits

The CBN's December 2025 Financial Stability Report, section 8.4, records **NGN25.85 billion in fraud losses** at year-end 2025. Internet and mobile platforms accounted for approximately **70% of losses**, with social engineering, phishing, and identity compromise among the drivers. This supports the relevance of payment fraud prevention. It does not establish the size of PayGuard's addressable market: institutional fraud losses include problems that seller-chat screening cannot solve. [CBN report](https://www.cbn.gov.ng/Out/2026/FPRD/FSR%20DECEMBER%202025%20EDITION%20-%20FINAL%201.07.2026.pdf)

Whether an initial customer bears fraud losses directly, spends heavily on dispute handling, or loses buyers' trust must be established. A platform that bears none of these costs may see the product as optional. Build a market estimate from reachable businesses, their check volumes, and demonstrated budgets rather than applying a percentage to national fraud losses.

## Competition

| Provider | Publicly described offering | Implication |
| --- | --- | --- |
| GoWaly, formerly Scamrader | Free individual recipient checks and evidence-based reporting; its developer page advertises recipient-risk APIs but currently requests waitlist registration | Direct overlap with the proposed account-reputation roadmap. Do not assume its API is generally available. |
| Prembly Fraud Lens | Shared fraud intelligence, evidence-backed reports, identity screening and investigation workflows | A larger competitor or potential supplier. Data access and permitted reuse require commercial verification. |
| NIBSS Hawk | Centralized fraud monitoring across participating institutions | Bank-facing fraud infrastructure already exists. PayGuard needs a complementary, demonstrable capability. |

Sources: [GoWaly consumer product](https://gowaly.com/), [GoWaly developer status](https://gowaly.com/developers), [Prembly Fraud Lens documentation](https://docs.prembly.com/guides/products/fraud/fraud-lens), [NIBSS description of Hawk](https://nibss-plc.com.ng/making-change-premier-oiwoh/).

The proposed differentiator is **conversation context plus recipient evidence at a decision point**: pressure tactics, third-party payment requests, impersonation, unusually cheap offers, and relevant verified history, explained in English or Nigerian Pidgin. This is a hypothesis to benchmark against existing tools and a generic language-model baseline, not a proven exclusive capability.

## What the repository already provides

- Text and optional screenshot intake, Nigerian account-number extraction, and English/Pidgin explanations.
- Deterministic rules and entity intelligence, with optional AI additions that cannot lower the baseline risk.
- A server-to-server checkout verification endpoint and server-enforced PayPal order gating.
- Useful engineering foundations: repository boundaries, migrations, connection pooling, failure handling, and recorded automated checks.

Sources: `README.md`, `docs/ARCHITECTURE.md`, `src/domain/signals.py`, `src/services/screening.py`, and `src/services/checkout.py`. Existing test and performance results are repository records; they were not rerun for this research.

## Launch blockers and required changes

1. **Detection quality is not established.** `docs/results/evaluation.json` records five flagged and five allowed scam messages, plus five allowed safe messages: 50% scam recall on only 15 examples. Zero false positives among five safe examples provides very little assurance. The README also discloses prior exposure to the corpus. This is a limited recorded result, not a current independent production estimate, and does not establish live-Gemini performance. Build a fresh, adjudicated dataset with realistic safe transactions, local phrasing, ambiguous cases, and separate holdout data. Measure precision, recall, coverage, review volume, and performance by scam category.

2. **Reports can poison the reputation system.** `src/api/routes.py:167` accepts reports without an authenticated reporter; `src/adapters/db2.py:256` escalates a non-trusted entity at three reports. Repeated unverified submissions can therefore affect another user's verdict. Replace count-based blocking with evidence review, reporter verification, deduplication, independent corroboration, an audit trail, and correction/appeal handling.

3. **Sensitive records lack application-level access control.** Screening history, individual screening records, and analyst routes have no user/tenant authorization in `src/api/routes.py`. Read-only SQL restrictions do not provide data isolation. Before real data, add authenticated ownership checks, tenant separation, admin roles, and scoped partner credentials. Protect operational endpoints too.

4. **Nigerian recipients need a structured identity.** Accounts currently become ten-digit text entities; `PlatformCheckIn` has an account number without a bank code, and velocity is keyed to payee email. Use bank/institution code plus account number, preserve leading zeros, normalize phones, and distinguish platform seller age from bank-account age. Retain unknown/insufficient-evidence outcomes.

5. **The payment integration needs a local pilot design.** NGN is absent from `PAYPAL_CURRENCIES` in `src/services/screening.py`. Add a suitable local PSP integration if payment execution is needed, or let the partner retain its existing payment flow and consume risk advice. PayGuard can enforce a decision only where the platform integrates it; a standalone checker cannot stop an unrelated bank transfer.

6. **Measure outcomes accurately.** `MONEY_PROTECTED` currently sums blocked screening amounts in `src/adapters/db2.py:371`. That is screened value, not demonstrated savings; it can include duplicate checks and false positives. Link checks to unique transactions, actual interventions, and confirmed outcomes before making savings claims.

Paystack documents Nigerian account resolution using an account number and bank code, returning account details. Its richer account-validation capability on that page is South Africa-specific. Account resolution provides identity context; it does not prove seller honesty or provide a national fraud history. Obtain provider approval for the intended usage and verify terms, quotas, coverage, and costs. [Paystack documentation](https://paystack.com/docs/identity-verification/verify-account-number/)

## Initial product and customers

Recommended first buyer: an operator with a reachable decision-maker, repeated seller transactions, access to relevant chats, and a real fraud/dispute burden. A small marketplace or managed commerce service is a candidate. Large banks and consumer subscriptions are later hypotheses; neither should be required for the first validation.

The first product should return a short risk explanation and evidence status, offer a review queue, and record the eventual outcome. Keep consumer checking available as a possible acquisition channel, while testing business subscriptions or metered API use as revenue. Obtain chats through an authorized integration or deliberate user submission; do not assume access to private WhatsApp or Instagram conversations.

An account with no reports must remain **unknown** rather than become verified-safe. Allow reviewers to contest a recommendation. Initially run in shadow mode, where recommendations are observed without automatically changing payment outcomes, then move to human-reviewed interventions when evidence supports it.

## Regulatory and privacy route

For validation, prefer a software service supplying risk evidence while a licensed partner handles payments and funds. **Not holding money does not automatically settle licensing.** Scope payment initiation, gateway functionality, escrow, data access, and each party's responsibilities with Nigerian fintech counsel before live operation. CBN publishes distinct payment-provider categories and requirements. [CBN payment-service framework and provider register](https://www.cbn.gov.ng/PaymentsSystem/PSPs.html)

Chats, phone numbers, account details and allegations can involve personal data. Establish lawful processing and sharing grounds, transparent notices, retention limits, appropriate security, and processor contracts. Assess the need for a data protection impact assessment and applicable registration. Significant solely automated decisions raise section 37 safeguards; external AI processing also requires assessment of cross-border transfer grounds. A submitting user's consent does not automatically resolve processing rights for everybody named in a screenshot. [Nigeria Data Protection Act 2023](https://ndpc.gov.ng/wp-content/uploads/2024/03/Nigeria_Data_Protection_Act_2023.pdf), [NDPC GAID 2025](https://ndpc.gov.ng/wp-content/uploads/2025/03/NDP-ACT-GAID-2025-MARCH-20TH.pdf)

The CBN sandbox's current Non-VASP track explicitly includes fraud prevention and real-time risk analytics. It permits suitably prepared startups without existing financial licences to apply, subject to readiness requirements. Consider it where controlled testing and regulatory engagement fit the model; admission is not automatic and does not substitute for full commercial authorization. [CBN sandbox](https://sandbox.cbn.gov.ng/)

## Revenue and economics to test

Test a paid, limited-volume business pilot at **NGN50,000–150,000 per month**. This is an interview/negotiation hypothesis, not a researched market price or revenue forecast. Set usage limits and quote expensive screenshot/AI work separately where necessary.

Ten customers paying NGN100,000 would produce NGN1 million monthly revenue before all costs. This arithmetic says nothing about customer acquisition or profitability. Track verification/data fees, AI/OCR usage, infrastructure and database licensing/support, human review, customer support, and sales costs. At pilot scale, manual work may dominate. Compare the fee with independently evidenced savings in fraud, review time, disputes, or buyer conversion.

## Ninety-day validation plan

| Period | Work | Decision evidence |
| --- | --- | --- |
| Days 1–14 | Interview 15–20 buyers, sellers, commerce operators and fraud staff; review actual workflows and anonymized cases | A specific buyer, recurring cost, usable data rights, and at least two credible pilot prospects |
| Days 15–35 | Resolve report abuse and access-control gaps; structure Nigerian recipient identities; establish privacy/data agreements and an independently labelled holdout | A defensible pilot boundary and a benchmark showing where the system helps or fails |
| Days 36–65 | Run with 2–3 partners in shadow mode; compare with current practice and simple baselines | Precision/recall with uncertainty, coverage, false positives, reviewer time, latency and cost per check |
| Days 66–90 | Introduce controlled human-reviewed warnings where justified; seek paid conversion | At least two paying partners and an observed operational or fraud benefit exceeding delivery costs |

These counts are proposed validation targets, not statistical proof. Set accuracy and review-capacity thresholds with partners before testing; warn-only and hard-block policies require different evidence. Obtain adjudicated outcomes and allow time for delivery disputes to mature. Stopping a payment does not itself prove that a scam was prevented.

Proceed to broader investment only if partners pay, data access is lawful and sustainable, and useful decisions outperform their existing process without unacceptable harm to legitimate sellers. If those conditions fail, narrow the use case or change the business model before adding more features.
