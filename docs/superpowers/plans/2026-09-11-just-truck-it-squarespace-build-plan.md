# Just Truck It — Squarespace Site & Dispatch Academy Build Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Rebuild Just Truck It Dispatching's approved 5-page marketing site and stand up a paid ($350) 15-module "Dispatch Academy" course, natively in a live Squarespace 7.1 account.

> **Scope correction (confirmed against the client's actual freelance job brief,
> screenshotted 2026-09-12 — see `docs/superpowers/content/client-brief-source-of-truth.md`):**
> the client's stated scope of work is the 5-page site, native forms + a
> consultation/booking option, the $350 paywall/course page, and **course
> structure plus ONE reusable Module 01 lesson template** — nothing more. The
> client explicitly said she populates Modules 02–15 herself. **A graded
> 45-question final assessment and certificate is NOT in the client's stated
> scope** — that content exists only in the vendor docx, not in what she
> actually asked for. Task 3 (ProProfs), and the Lesson 16 "Final Assessment"
> pieces of Tasks 9-10, are now treated as **optional/out-of-scope-pending-
> confirmation**, not as required blocked work. Do not spend the ProProfs
> budget or build Lesson 16 unless the client explicitly confirms she wants
> the final assessment built as part of this engagement.

**Architecture:** No code repository exists for the live site — Squarespace has no CLI/API surface this plan can drive directly, so every live-site task is executed through Squarespace's own web editor via browser automation (Playwright), with actions visible as they happen. Content that doesn't require live-site access (lesson copy, marketing-page copy) is prepared first as local files so it's ready to paste in the moment credentials arrive. If the client does confirm she wants the final assessment built, its grading/certificate logic would be delegated to ProProfs Quiz Maker (embedded via iframe) because Squarespace has no native quiz/certificate support and a hand-rolled quiz can't reliably hide the answer key — but that entire path is on hold until she says so.

**Tech Stack:** Squarespace 7.1 (Fluid Engine, native Course collection, Member Areas commerce), Playwright browser automation, Markdown content files, git. ProProfs Quiz Maker only if the client confirms the final-assessment scope.

**Spec:** `docs/superpowers/specs/2026-09-11-just-truck-it-squarespace-build-design.md`

## Global Constraints

- Squarespace 7.1 / Fluid Engine only; native blocks wherever possible, minimal custom CSS/JS (per client requirement).
- The client's own job brief (not just the vendor docx) is the source of truth for scope. It says "set up forms and a consultation/booking option" as work to be **built**, not as a reference to an existing embed — do not assume a live Acuity embed already exists on the site until Task 4's audit confirms one. If Task 4 finds no existing booking embed, Tasks 12 and 14 build a new consultation/booking option (native Squarespace Scheduling block, or Acuity if that's what the client wants) instead of "preserving" anything.
- The 5 HTML mockup files in this directory are **visual references only** — never paste them into Squarespace as full-page Code Blocks.
- Module 01 is the **master lesson template only**. Modules 02–15 are **out of scope** — the client populates those themselves.
- The vendor-only final-assessment answer key must **never** be exposed to students at any point in the build.
- Certificate wording must never describe it as a professional license, government certification, college degree, or industry accreditation (client's explicit rule).
- Course content carries the training disclaimer: "This course is educational and does not provide legal, tax, insurance, or regulatory advice. Freight markets and requirements change. Verify current requirements with official sources, brokers, insurers, and qualified professionals as appropriate."
- Tuition is $350 flat for all 15 modules — one purchase, not per-module.
- Passing score is 80% on the 45-question final assessment.
- Never touch Squarespace billing or domain settings.
- Any Squarespace login used for this build must be temporary/rotatable, and rotated again at the end of each work session.
- Several tasks below are **blocked** on specific answers from the client (spec's Open Questions list) or on live-site access — each blocked task states exactly what it's waiting on. Do not guess past a block; surface it back to the human instead.

---

## Task 1: Extract Module 01 lesson content into a paste-ready file

**Files:**
- Create: `docs/superpowers/content/module-01-lesson-content.md`

**Interfaces:**
- Consumes: `Just_Truck_It_Dispatch_Academy_Vendor_Course_Content.docx` (source text, already extracted once to `/tmp/.../docx_extracted.txt` during research — re-extract fresh here so the task is self-contained)
- Produces: a Markdown file with one heading per native Squarespace Lesson content block, ready to paste in Task 8

- [ ] **Step 1: Re-extract the docx to plain text**

Run:
```bash
cd "/home/diolichat/truck" && python3 - <<'EOF'
import zipfile, re, html
z = zipfile.ZipFile("Just_Truck_It_Dispatch_Academy_Vendor_Course_Content.docx")
xml = z.read('word/document.xml').decode('utf-8')
text = re.sub(r'<w:p [^>]*>|<w:p>', '\n', xml)
text = re.sub(r'<w:tab/>', '\t', text)
text = re.sub(r'<w:br/>', '\n', text)
text = re.sub(r'<[^>]+>', '', text)
text = html.unescape(text)
open('/tmp/docx_extracted.txt', 'w').write(text)
EOF
```
Expected: file `/tmp/docx_extracted.txt` created, ~48KB.

- [ ] **Step 2: Write the Module 01 content file**

Create `docs/superpowers/content/module-01-lesson-content.md` with this exact content:

```markdown
# Module 01 Lesson Content — paste-ready for Squarespace Lesson editor

> Source: Just_Truck_It_Dispatch_Academy_Vendor_Course_Content.docx, "MODULE 01".
> This is the MASTER TEMPLATE — build this Lesson first in Squarespace, then the
> client duplicates its block structure for Modules 02–15 using their own content.
> Each `##` heading below = one Text block (or Text+Image where noted) in the Lesson.

## Module Introduction

MODULE 01 — INTRODUCTION TO FREIGHT DISPATCHING

This module focuses on what freight dispatching is, what a dispatcher does
every day, the difference between a dispatcher and a freight broker, how
dispatchers get paid, and the complete load cycle.

## What You'll Learn

- What freight dispatching is
- What a dispatcher does every day
- The difference between a dispatcher and a freight broker
- How dispatchers get paid
- The complete load cycle

## The Lesson

**What Is Freight Dispatching?**

Freight dispatching is the administrative and operational support provided
to a motor carrier. A dispatcher helps the carrier locate freight, reviews
load opportunities, communicates with brokers and shippers, assists with
rate negotiation when authorized, sends load information to drivers,
follows active loads, organizes paperwork, and helps plan the truck's next
move.

**Who's In Charge?**

The dispatcher works for the carrier - not the broker. The carrier remains
responsible for the truck, driver, safety, legal compliance, operating
authority, insurance, and the final decision to accept or reject a load.

**How Dispatchers Get Paid**

A dispatcher may be paid a percentage of the loads they dispatch or a flat
fee. In this course, the example business uses a 10% dispatch fee. Gross
load revenue is not the same as carrier profit because the carrier still
has fuel, insurance, truck payments, maintenance, payroll, taxes, tolls,
and other operating expenses.

**What Good Dispatching Looks Like**

Your job as a dispatcher is not simply to find the highest number on the
load board. Your job is to help the carrier make informed decisions.
Before you search, you need to know where the truck is, when it will be
empty, what equipment it has, where the carrier wants to go, how much
deadhead is acceptable, and what the carrier considers an acceptable rate.

## Key Takeaways

- A dispatcher supports the carrier; the carrier retains final load authority.
- The dispatcher is not automatically a freight broker.
- Dispatch compensation may be percentage-based or a flat fee; this course uses 10% as its example.
- Gross load revenue is not the same as carrier profit.
- Good load searching begins with the truck's actual location, availability, equipment and carrier preferences.

## Real-World Application

Imagine a carrier calls and says, "Find me something good." Before opening
a load board, identify the truck's location and empty time, equipment,
preferred direction, acceptable deadhead and rate expectations. Those
answers shape the search.

## Put It Into Practice

1. Explain five dispatcher responsibilities in your own words.
2. Explain why the carrier - not the dispatcher - should make the final load decision.
3. Write the load cycle in order: Search -> Evaluate -> Broker Call -> Negotiate -> Carrier Approval -> Rate Confirmation -> Pickup -> Delivery -> POD -> Invoice -> Reload.

## Check Your Understanding

1. What could go wrong in this part of the dispatch process?
2. What information would you verify before moving forward?
3. Who has final decision-making authority on a load?

## Self-Check

Explain the complete load cycle out loud from memory without looking back at the lesson.

## Module Complete

You've completed Module 01. Continue to Module 02.

**Training Disclaimer:** This course is educational and does not provide
legal, tax, insurance, or regulatory advice. Freight markets and
requirements change. Verify current requirements with official sources,
brokers, insurers, and qualified professionals as appropriate.
```

- [ ] **Step 3: Verify completeness against the source**

Run:
```bash
cd "/home/diolichat/truck" && grep -c "^##" docs/superpowers/content/module-01-lesson-content.md
```
Expected: `9` (one per hierarchy section: Module Introduction, What You'll Learn, The Lesson, Key Takeaways, Real-World Application, Put It Into Practice, Check Your Understanding, Self-Check, Module Complete).

Then manually diff the prose against `/tmp/docx_extracted.txt` lines 26-63 to confirm no wording was altered.

- [ ] **Step 4: Commit**

```bash
cd "/home/diolichat/truck" && git add docs/superpowers/content/module-01-lesson-content.md && git commit -m "Add paste-ready Module 01 lesson content for Squarespace"
```

---

## Task 2: Extract the 45-question final assessment bank for ProProfs import

**Files:**
- Create: `docs/superpowers/content/vendor-only-final-assessment-bank.md`

**Interfaces:**
- Consumes: `Just_Truck_It_Dispatch_Academy_Vendor_Course_Content.docx` ("FINAL ASSESSMENT" and "VENDOR-ONLY FINAL ASSESSMENT ANSWER KEY" sections)
- Produces: a vendor-only question+answer file used by Task 3 to build the ProProfs exam. This file must never be linked from any student-facing page or embed.

- [ ] **Step 1: Write the question bank file**

Create `docs/superpowers/content/vendor-only-final-assessment-bank.md`:

```markdown
# VENDOR-ONLY — Final Assessment Question Bank + Answer Key

**Do not publish, embed, or link this file anywhere student-facing.**
Source: Just_Truck_It_Dispatch_Academy_Vendor_Course_Content.docx.
Used only to build the ProProfs Quiz Maker exam (Task 3). 45 multiple-choice
questions, 80% required to pass.

| # | Question | A | B | C | D | Correct |
|---|---|---|---|---|---|---|
| 1 | What is the primary role of a freight dispatcher? | Perform truck repairs | Issue operating authority to carriers | Support the motor carrier with load searching, communication, coordination and paperwork | Own the freight being transported | C |
| 2 | Who has final authority to accept or reject a load? | The dispatcher alone | The receiver | The carrier or its authorized representative | The load board | C |
| 3 | Which statement is correct? | Every dispatcher is a broker | A dispatcher guarantees carrier profit | A dispatcher owns every truck dispatched | A dispatcher is not automatically a freight broker | D |
| 4 | Who physically provides the motor-carrier transportation service? | The factoring company | The dispatcher | The motor carrier | The load board | C |
| 5 | What is a consignee? | The party or location receiving the freight | A fuel-card provider | The truck's insurance agent | The dispatcher | A |
| 6 | What can a factoring company help a carrier do? | Receive funds sooner on eligible receivables, subject to its agreement | Set federal regulations | Obtain a CDL | Repair a trailer | A |
| 7 | Which trailer is temperature controlled? | Dry van | Step deck | Reefer | Flatbed | C |
| 8 | Which is an example of open-deck equipment? | Cargo van | Flatbed | Reefer | Dry van | B |
| 9 | What does power only generally mean? | The carrier provides the tractor and pulls another party's trailer | The driver only hauls electricity | The trailer has no cargo | The carrier uses no tractor | A |
| 10 | Which document commonly summarizes a carrier's insurance coverage? | Certificate of Insurance (COI) | Rate confirmation | BOL | POD | A |
| 11 | Which tax form is commonly requested during carrier setup? | I-9 only | W-9 | W-2 only | 1098-T | B |
| 12 | When should normal load booking begin for a new carrier client? | After required onboarding and verification are completed | Before learning the equipment type | Before receiving carrier authorization | Immediately without documents | A |
| 13 | What is deadhead? | Miles traveled without a revenue-producing load | Miles driven only at night | Miles from shipper to receiver with freight | Miles excluded from an odometer | A |
| 14 | Why should a dispatcher consider the destination market? | It determines the driver's CDL class automatically | It can affect reload availability, downtime and future deadhead | It changes the truck's VIN | It eliminates insurance requirements | B |
| 15 | Which is a useful load-board filter? | Driver's favorite color | Truck radio station | Equipment type | Dispatcher's age | C |
| 16 | A load pays $2,000 for 800 loaded miles. What is the loaded RPM? | $3.20 | $2.50 | $2.00 | $1.60 | B |
| 17 | A load pays $1,800 with 700 loaded miles and 100 deadhead miles. What is the all-in RPM? | $3.00 | $1.80 | $2.57 | $2.25 | D |
| 18 | What is a 10% dispatch fee on $2,500? | $500 | $125 | $250 | $25 | C |
| 19 | Which is a professional broker-call opening? | Give me your best load right now. | I need money; what do you have? | Good morning, I'm calling about load 12345 from Atlanta to Nashville. Is it still available? | Book whatever you have without details. | C |
| 20 | Which information should be confirmed with a broker? | Pickup, delivery, commodity, weight, equipment, rate and special requirements | Only the broker's first name | Only the rate | Only the destination state | A |
| 21 | Should a dispatcher misrepresent a truck's location to secure a load? | Yes, whenever the rate is high | No | Yes, if the broker is busy | Only on Fridays | B |
| 22 | What is a counteroffer? | A proposed alternative to the offered rate or terms | A fuel receipt | An insurance cancellation | A signed POD | A |
| 23 | Who should approve the final negotiated load terms? | Another random carrier | The load board | The receiver's security guard | The carrier or authorized representative | D |
| 24 | Which factor may legitimately support a higher-rate request? | The driver's music preference | The dispatcher's favorite destination | Significant deadhead | The color of the tractor | C |
| 25 | What document commonly contains the broker's written load rate and terms? | Rate confirmation | Fuel receipt | Driver's license | Truck title | A |
| 26 | You negotiated $2,100 but the rate confirmation says $1,900. What should you do? | Ignore it and hope for $2,100 | Change the PDF yourself | Resolve the discrepancy with the broker before proceeding | Tell the driver after delivery | C |
| 27 | Does your internal load-authorization form replace the broker's rate confirmation? | Yes, always | Only for reefer loads | No | Only on weekends | C |
| 28 | What does ETA mean? | Estimated time of arrival | Electronic truck account | Equipment transport authorization | Estimated trailer amount | A |
| 29 | What is important to document for detention? | The truck's paint color | The dispatcher's commute | The driver's lunch order | Arrival/departure times and required supporting information | D |
| 30 | What comes first during a breakdown or emergency? | Finding a higher-paying load | Posting on social media | Safety | Calculating the dispatch fee | C |
| 31 | What does BOL stand for? | Booking Order List | Broker Operating License | Bill of Lading | Business Owner Ledger | C |
| 32 | What does POD stand for? | Proof of Delivery | Permit of Dispatch | Payment on Demand | Price of Diesel | A |
| 33 | What should you do if a POD image is unreadable? | Alter it yourself | Submit a blank page | Request a clear, complete copy | Discard all delivery paperwork | C |
| 34 | What does NOA commonly stand for in factoring? | Notice of Arrival | Number of Axles | Notice of Assignment | National Operator Account | C |
| 35 | What is quick pay? | An accelerated payment option that may involve a fee | A federal operating authority | A type of trailer | A free truck repair | A |
| 36 | At a 10% dispatch fee, what is the fee on $3,000? | $30 | $300 | $600 | $150 | B |
| 37 | Which is important to confirm for a reefer load? | Driver's shoe size | Truck's exterior color | Dispatcher's office temperature | Required temperature/set point | D |
| 38 | Which is an important flatbed consideration? | Freight dimensions and securement/tarp requirements | Passenger count | Reefer set point only | Cab upholstery | A |
| 39 | Why confirm box-truck or hotshot dimensions and payload? | To avoid calculating mileage | To determine the driver's age | To select a radio station | To determine whether the equipment is suitable for the shipment | D |
| 40 | Why start considering a reload before the current load delivers? | To avoid communicating with the carrier | To reduce potential downtime/deadhead and plan the next move | To cancel the current load | To change the carrier's authority | B |
| 41 | Is weekly gross revenue the same as profit? | Only for dry vans | No | Yes, always | Only when fuel is purchased | B |
| 42 | Why might a lower-paying first load sometimes be strategically better? | It may position the truck in a stronger reload market or better fit the carrier's schedule | Lower rates always create more profit | It removes all delivery appointments | It eliminates fuel expenses | A |
| 43 | Which onboarding order makes the most sense? | Delivery -> inquiry -> insurance -> search | Load search -> inquiry -> agreement -> consultation | Invoice -> load search -> consultation -> inquiry | Inquiry -> consultation -> agreement -> documents/verification -> load search | D |
| 44 | Why establish business hours and an after-hours policy? | To set clear service expectations and professional boundaries | To avoid helping active loads | To eliminate written agreements | To guarantee 24/7 routine calls | A |
| 45 | Should a dispatcher guarantee a carrier a specific weekly gross? | Yes, after one successful load | Yes, for every carrier | No | Yes, if the carrier has a reefer | C |

## Certificate copy (for ProProfs certificate template, Task 3)

"Just Truck It Dispatch Academy Certificate of Completion" — awarded to
students who complete the course requirements and score at least 80% on
this final assessment. Must not be described as a professional license,
government certification, college degree, or industry accreditation.
```

- [ ] **Step 2: Verify the question count and answer-key alignment**

Run:
```bash
cd "/home/diolichat/truck" && grep -c "^| [0-9]* |" docs/superpowers/content/vendor-only-final-assessment-bank.md
```
Expected: `45`.

Then cross-check the `Correct` column against `/tmp/docx_extracted.txt`'s "VENDOR-ONLY FINAL ASSESSMENT ANSWER KEY" line (`1. C 2. C 3. D 4. C 5. A 6. A 7. C 8. B 9. A 10. A 11. B 12. A 13. A 14. B 15. C 16. B 17. D 18. C 19. C 20. A 21. B 22. A 23. D 24. C 25. A 26. C 27. C 28. A 29. D 30. C 31. C 32. A 33. C 34. C 35. A 36. B 37. D 38. A 39. D 40. B 41. B 42. A 43. D 44. A 45. C`) question-by-question. Fix any mismatch before continuing.

- [ ] **Step 3: Commit**

```bash
cd "/home/diolichat/truck" && git add docs/superpowers/content/vendor-only-final-assessment-bank.md && git commit -m "Add vendor-only final assessment question bank for ProProfs import"
```

---

## Task 3: Stand up the ProProfs Quiz Maker exam

**OUT OF SCOPE PENDING CONFIRMATION.** The client's actual job brief never mentions a final assessment, quiz, or certificate — her stated scope stops at "course structure plus ONE reusable Module 01 lesson template." This task's entire premise (a 45-question graded exam) comes from the vendor docx, not from what she asked for. **Do not start this task at all until the client explicitly confirms she wants a final assessment/certificate built as part of this engagement** and approves the ~$20/mo ProProfs cost. Account creation/payment is also a billing action a human should perform directly rather than an automated agent — do not sign up or enter payment details on the client's behalf even after confirmation.

**Files:** none in this repo (external SaaS configuration) — record the resulting exam's shareable embed URL in `docs/superpowers/content/proprofs-exam-reference.md` once created.

**Interfaces:**
- Consumes: `docs/superpowers/content/vendor-only-final-assessment-bank.md` (Task 2)
- Produces: an embed URL/iframe snippet consumed by Task 10

- [ ] **Step 1: Confirm client approval and who holds the ProProfs account**

Do not proceed past this step until the human confirms: (a) the client approved the cost, and (b) whose email/billing the ProProfs account is created under. This is a business decision, not a technical one.

- [ ] **Step 2: Human creates the ProProfs Quiz Maker Business account**

The account owner signs up at proprofs.com's Quiz Maker product and selects the Business plan. This step is performed by the human, not by browser automation, since it involves entering payment details.

- [ ] **Step 3: Build the 45-question exam from the vendor bank**

Using `docs/superpowers/content/vendor-only-final-assessment-bank.md`, create one new quiz in ProProfs named "Just Truck It Dispatch Academy — Final Assessment" with all 45 questions, four options each, and the correct answer marked per the table. Set: passing score 80%, "show correct answers to test-taker" = OFF (this is what keeps the vendor answer key hidden), unlimited attempts (per spec's default retake policy), and certificate-on-pass enabled using the certificate copy from Task 2's file.

- [ ] **Step 4: Verify the answer key is not exposed**

Take the exam once as a test-taker end-to-end, deliberately answering a few questions wrong, and confirm the results screen shows only a pass/fail + score — never which answers were correct/incorrect or what the right answer was.

- [ ] **Step 5: Record the embed reference**

Create `docs/superpowers/content/proprofs-exam-reference.md`:
```markdown
# ProProfs Final Assessment — Embed Reference

Exam name: Just Truck It Dispatch Academy — Final Assessment
Embed URL: <paste the ProProfs-provided embed/share URL here once created>
Passing score: 80%
Retakes: unlimited
Certificate: auto-issued by ProProfs on pass
```

- [ ] **Step 6: Commit**

```bash
cd "/home/diolichat/truck" && git add docs/superpowers/content/proprofs-exam-reference.md && git commit -m "Record ProProfs final assessment embed reference"
```

---

## Task 4: Squarespace site audit (discovery pass)

**BLOCKED** by spec Open Question #2 — need the site URL and a temporary/rotatable login before this task can start.

**Files:** Create `docs/superpowers/content/squarespace-audit-findings.md`

**Interfaces:**
- Consumes: Squarespace login (human-provided, temporary)
- Produces: findings that Tasks 5-15 depend on (current plan tier, existing pages, existing Course/Commerce config, actual Acuity/payment wiring)

- [ ] **Step 1: Log in via browser automation**

Navigate to the provided Squarespace site's login URL and authenticate with the temporary credentials. Do not navigate into Billing or Domains during this pass.

- [ ] **Step 2: Inventory existing pages**

In the site's Pages panel, list every existing page, its type (regular page, Course, Store, etc.), and whether it appears to match one of the 5 target pages (Home, Dispatch Services, About, Contact, Dispatch Academy) or is unrelated/placeholder content.

- [ ] **Step 3: Inventory Commerce / Member Areas / Course setup**

Check Commerce settings and any existing Member Areas/Course product for: whether a paid Course product already exists, what price/plan is configured (if any), and whether checkout is wired to Stripe/PayPal.

- [ ] **Step 4: Inventory Acuity integration**

Locate the Acuity Scheduling block/embed on any existing page and confirm whether it also handles the $350 Academy payment, or is scoped only to consultation booking (this directly answers spec Open Question #3).

- [ ] **Step 5: Record findings**

Write `docs/superpowers/content/squarespace-audit-findings.md` with the plan tier, the page inventory table, the Commerce/Course findings, and the Acuity/payment finding. Flag explicitly which of the spec's Open Questions this audit resolves vs. still leaves open.

- [ ] **Step 6: Commit**

```bash
cd "/home/diolichat/truck" && git add docs/superpowers/content/squarespace-audit-findings.md && git commit -m "Record Squarespace site audit findings"
```

---

## Task 5: Confirm and configure the $350 Academy paywall mechanism

**BLOCKED** by Task 4 (audit findings) and spec Open Question #3 (payment routing confirmation from client).

**Files:** none local — live Squarespace Commerce/Member Areas configuration.

**Interfaces:**
- Consumes: Task 4 findings; client's answer on payment routing
- Produces: a configured one-time $350 paid access gate that Task 9's Course will be placed behind

- [ ] **Step 1: Apply the client's payment-routing answer**

If the client confirms the $350 charge should run through Squarespace's own Member Areas/Commerce checkout: in Commerce settings, create or edit the paid access plan to a one-time $350 price tied to the Dispatch Academy Course (built in Task 9). If the client confirms Acuity handles this charge instead: skip Squarespace-side payment configuration entirely and instead configure the Course's access gating to check for the Acuity purchase (exact mechanism depends on how Acuity's existing setup signals purchase — determine this from Task 4's findings before proceeding, since it is unknown at plan-writing time).

- [ ] **Step 2: Verify certificate/legal copy is attached to the plan description**

Add the tuition description "$350 total tuition for full access to all 15 Dispatch Academy modules" and the training disclaimer text (Global Constraints) to the plan/product description field.

- [ ] **Step 3: Test the gate with a $0 comp coupon**

Create a one-time 100%-off coupon, use it to complete a full test checkout as a fresh test member, and confirm access to the Course is granted immediately after checkout.

- [ ] **Step 4: Delete the test coupon**

Remove the 100%-off coupon created in Step 3 so it can't be reused live.

- [ ] **Step 5: Commit**

No local files changed by this task; if any configuration notes were added to the audit-findings file, commit those:
```bash
cd "/home/diolichat/truck" && git add docs/superpowers/content/squarespace-audit-findings.md && git commit -m "Note Academy paywall configuration decision" --allow-empty
```

---

## Task 6: Apply sitewide brand design tokens

**BLOCKED** by Task 4 (need live-site access to the Design panel).

**Files:** none local — live Squarespace Design panel (Site Styles / Custom CSS scoped to fonts+colors only).

**Interfaces:**
- Consumes: brand tokens defined below (from `Home Page V4.html`'s `:root` variables)
- Produces: sitewide color/font tokens that Tasks 7, 8, and 11-15 all build on top of

- [ ] **Step 1: Set the color palette**

In Squarespace's Design > Colors panel, set these as the site's core palette (sourced from `Home Page V4.html` lines 10-22):
- Black (background): `#0B0D0C`
- Charcoal (secondary surface): `#16181A`
- Charcoal 2 (tertiary surface): `#1E2123`
- Lime (primary accent): `#C6FF3D`
- Lime dim (accent hover): `#9FD400`
- White (text on dark): `#F6F6F2`
- Steel (muted text): `#8B918C`
- Steel dark (muted text, darker): `#4C534E`

- [ ] **Step 2: Set the typography**

In Design > Fonts, set: display/heading font to Oswald (weights 500/600/700, uppercase, tight letter-spacing to match the mockup), body font to Inter (weights 400/500/600/700), and add IBM Plex Mono (weights 500/600) as an available custom font for eyebrow/label text — matching `Home Page V4.html` lines 7-8 and 19-21.

- [ ] **Step 3: Screenshot and compare**

Take a screenshot of a default Squarespace page with the new tokens applied and visually compare heading/body rendering against `Home Page V4.html` opened in a browser, side by side, to confirm the fonts/colors match before building any page content on top.

- [ ] **Step 4: Commit**

No local files changed; skip commit for this task (live design-panel configuration only).

---

## Task 7: Build the Course Overview ("Start Here") page

**BLOCKED** by Task 4 and Task 6.

**Files:** none local — live Squarespace Course Overview page content.

**Interfaces:**
- Consumes: sitewide brand tokens (Task 6); docx "START HERE" section content
- Produces: the Course's landing page that Task 8's Lesson 1 links back to

- [ ] **Step 1: Create the Course collection**

In Squarespace's Pages panel, add a new Course collection named "Dispatch Academy."

- [ ] **Step 2: Populate the Course Overview page content**

Add this content to the Course Overview page, using native Text blocks:

```markdown
## Just Truck It Dispatch Academy

**START HERE**

Welcome to Just Truck It Dispatch Academy

This is a beginner-focused freight dispatching course organized into 15
modules. Work through the material at your own pace, in order, to build a
practical understanding of freight dispatching from the first load search
through load closeout, payment processes and dispatch-business operations.

### How This Course Works

- Work through Modules 1-15 in order.
- Complete the practice activities and self-checks in each module.
- Complete the 45-question final assessment after finishing all 15 modules.
- A score of at least 80% is required to meet the stated assessment requirement for the Certificate of Completion.

### Before You Begin

This course is educational and does not provide legal, tax, insurance, or
regulatory advice. Freight markets and requirements change. Verify current
requirements with official sources, brokers, insurers, and qualified
professionals as appropriate.
```

- [ ] **Step 3: Add the Start Course button and confirm the progress bar renders**

Confirm the native "Start Course" button and progress bar appear on the Overview page (Squarespace generates these automatically for Course collections) — no custom block needed.

- [ ] **Step 4: Commit**

No local files changed; skip commit for this task.

---

## Task 8: Build the Module 01 Lesson (master template)

**BLOCKED** by Task 1 (content) and Task 7 (Course collection must exist).

**Files:** none local — live Squarespace Lesson page content.

**Interfaces:**
- Consumes: `docs/superpowers/content/module-01-lesson-content.md` (Task 1); sitewide brand tokens (Task 6)
- Produces: the reusable Lesson block structure that (a) Task 9 duplicates empty for Lessons 2-15 and (b) the client duplicates with their own content for Modules 02-15

- [ ] **Step 1: Add a Chapter and the first Lesson**

Inside the "Dispatch Academy" Course, add one Chapter named "Dispatch Academy Course." Inside it, add a Lesson named "Module 01 — Introduction to Freight Dispatching."

- [ ] **Step 2: Build the Lesson content from the prepared file**

Paste each `##` section from `docs/superpowers/content/module-01-lesson-content.md` into its own Text block, in order, inside the Lesson editor: Module Introduction, What You'll Learn (as a bulleted list block), The Lesson, Key Takeaways (bulleted list), Real-World Application, Put It Into Practice (numbered list), Check Your Understanding (numbered list), Self-Check, Module Complete, Training Disclaimer.

- [ ] **Step 3: Confirm the native "Complete and Continue" button appears**

Confirm Squarespace's native Lesson navigation control appears at the bottom of the Lesson without any custom block added — this replaces the interactive preview's custom sidebar/progress-dot JS entirely (per spec Non-Goals).

- [ ] **Step 4: Visual QA against the interactive preview**

Open `dispatch-academy-module-01-interactive-preview.html` in a browser tab alongside the live Lesson page. Confirm heading hierarchy, section order, and brand styling feel consistent — without attempting to recreate its custom sidebar/checkboxes (per spec).

- [ ] **Step 5: Commit**

No local files changed; skip commit for this task.

---

## Task 9: Scaffold empty Lessons 02–15 (and, only if confirmed, the Final Assessment slot)

**BLOCKED** by Task 8 (Chapter must exist with Lesson 01 as the pattern to follow). Step 3 (the Lesson 16 slot) is additionally **out of scope pending confirmation** — see Task 3's note. Do Steps 1-2 and 4-5 regardless; skip Step 3 unless the client has confirmed she wants a final assessment.

**Files:** none local — live Squarespace Lesson shells.

**Interfaces:**
- Consumes: Task 8's Chapter structure
- Produces: 14 empty, correctly-named, correctly-ordered Lesson shells the client fills in themselves, plus (only if confirmed) the Lesson 16 slot Task 10 fills

- [ ] **Step 1: Add 14 empty Lessons**

In the "Dispatch Academy Course" Chapter, add 14 more Lessons, named and ordered exactly:
"Module 02 — Understanding The Trucking Industry", "Module 03 — Trucks, Trailers & Equipment", "Module 04 — Carrier Onboarding & Required Documents", "Module 05 — Load Boards & Finding Loads", "Module 06 — Rate Per Mile & Load Calculations", "Module 07 — Calling Freight Brokers", "Module 08 — Rate Negotiation", "Module 09 — Booking The Load & Rate Confirmations", "Module 10 — Managing An Active Load", "Module 11 — BOL, Delivery & POD", "Module 12 — Factoring, Invoicing & Payments", "Module 13 — Reefer, Flatbed, Box Truck & Hotshot Dispatching", "Module 14 — Building A Carrier's Week & Lane Planning", "Module 15 — Starting & Operating A Dispatch Business".

- [ ] **Step 2: Add one placeholder Text block per empty Lesson**

In each of the 14 Lessons, add a single Text block reading: "Content for this module goes here — duplicate the block structure from Module 01 and populate with this module's content from the vendor course-content document." This gives the client a clear starting point without guessing the template structure.

- [ ] **Step 3: Add the Lesson 16 slot (skip unless the client has confirmed the final-assessment scope — see Task 3)**

Add a 16th Lesson named "Final Assessment" with a single placeholder Text block: "Final assessment embed goes here (Task 10)." Order it immediately after Module 15.

- [ ] **Step 4: Confirm ordering and progress bar**

View the Course Overview page and confirm all 16 Lessons appear in the sidebar in the correct order, and the progress bar reflects 1/16 complete once Lesson 01 is marked done in a test pass.

- [ ] **Step 5: Commit**

No local files changed; skip commit for this task.

---

## Task 10: Embed the ProProfs exam as Lesson 16

**OUT OF SCOPE PENDING CONFIRMATION** (see Task 3) and additionally **BLOCKED** by Task 3 (exam must exist and have an embed URL) and Task 9 (Lesson 16 slot must exist). Do not start this task at all unless the client has confirmed she wants a final assessment built.

**Files:** none local — live Squarespace Lesson 16 content.

**Interfaces:**
- Consumes: `docs/superpowers/content/proprofs-exam-reference.md` (Task 3)
- Produces: the final, student-facing assessment experience

- [ ] **Step 1: Replace the Lesson 16 placeholder**

Remove the placeholder Text block from Lesson 16 ("Final Assessment"). Add a Text block above the embed with:

```markdown
## Final Assessment

You've completed all 15 training modules. This 45-question assessment
covers everything from the course. A score of at least 80% is required for
your Certificate of Completion. You may retake the assessment as many
times as needed.
```

- [ ] **Step 2: Add the ProProfs embed**

Add a Code Block (or Squarespace's native Embed block, whichever the ProProfs-provided snippet requires) containing the iframe embed code from `docs/superpowers/content/proprofs-exam-reference.md`'s "Embed URL." This is the one place in this build where an iframe/embed is appropriate custom code, since it's the minimal-necessary integration for functionality Squarespace doesn't natively provide (per spec's Key Finding).

- [ ] **Step 3: End-to-end test as a student**

As a test member who has completed Lesson 01 (and stubbed completion of 02-15 for test purposes), open Lesson 16, take the embedded exam, intentionally score below 80% once and confirm no certificate/pass message appears, then retake and score above 80% and confirm the ProProfs certificate is delivered.

- [ ] **Step 4: Commit**

No local files changed; skip commit for this task.

---

## Task 11: Rebuild the Home page

**BLOCKED** by Task 6 (brand tokens must be set first).

**Files:** Reference only (do not paste as code): `Home Page V4.html`

**Interfaces:**
- Consumes: sitewide brand tokens (Task 6); `Home Page V4.html` as visual reference; `IMG-...WA0000.jpg` (logo)
- Produces: the live Home page

- [ ] **Step 1: Open the mockup as a visual reference, not a source to paste**

Open `Home Page V4.html` in a browser tab. Do not view-source-and-paste; use it only to read section order, copy, and layout intent.

- [ ] **Step 2: Rebuild each section with native Fluid Engine blocks**

In the Squarespace Home page, add sections matching the mockup's structure in order: sticky header/nav (Squarespace's native site header, styled with brand tokens), hero (Text block for the "You Drive. We Handle The Rest." headline/subline/copy + two Button blocks for "Get Started"/"Book a Consultation" + an Image block placeholder where the mockup has a photography placeholder), and continue through the remaining sections visible in the mockup, matching copy verbatim from the mockup's Text content.

- [ ] **Step 3: Add the logo**

Upload `IMG-...WA0000.jpg` as the site logo in Design > Logo, sized to match the header proportions in the mockup.

- [ ] **Step 4: Visual diff at three breakpoints**

Take screenshots of the live Home page at desktop (1440px), tablet (768px), and mobile (390px) widths. Open `Home Page V4.html` at the same three widths. Compare side by side; fix any section where layout, spacing, or copy diverges.

- [ ] **Step 5: Commit**

No local files changed; skip commit for this task.

---

## Task 12: Rebuild the Dispatch Services page

**BLOCKED** by Task 6.

**Files:** Reference only: `Dispatch Services v2.html`

**Interfaces:**
- Consumes: sitewide brand tokens (Task 6); `Dispatch Services v2.html`
- Produces: the live Dispatch Services page

- [ ] **Step 1: Rebuild each section with native blocks**

Following `Dispatch Services v2.html`'s section order ("Dispatch Support For Owner-Operators & Small Carriers", "What Just Truck It Handles", "Getting Started With Dispatch", "No Forced Dispatch", "Common Questions", "Ready For Dispatch Support?"), rebuild each with native Text/Image/Button blocks, copying text verbatim.

- [ ] **Step 2: Handle the consultation/booking option per Task 4's findings**

The client's job brief lists "set up forms and a consultation/booking option" as work to build — don't assume it already exists. If Task 4's audit found a live Acuity embed already on this page, leave it in place exactly as-is (per the spec's Marketing Pages Approach) — do not rebuild or move it. If Task 4 found no existing booking embed, build a new consultation/booking option here (native Squarespace Scheduling block, or Acuity if the client specifically wants that) matching the mockup's "Book a Free Consultation" button placement.

- [ ] **Step 3: Visual diff at three breakpoints**

Same process as Task 11 Step 4, comparing against `Dispatch Services v2.html`.

- [ ] **Step 4: Commit**

No local files changed; skip commit for this task.

---

## Task 13: Rebuild the About page

**BLOCKED** by Task 6.

**Files:** Reference only: `About V2.html`

**Interfaces:**
- Consumes: sitewide brand tokens (Task 6); `About V2.html`; `IMG-...WA0001.jpg` (founder photo)
- Produces: the live About page

- [ ] **Step 1: Rebuild each section with native blocks**

Following `About V2.html`'s section order ("She Didn't Just Learn The Business. She Built Her Own.", "The Story Behind Just Truck It", "Need Dispatch Support?", "Want To Learn Dispatching?"), rebuild with native Text/Image/Button blocks, copying text verbatim.

- [ ] **Step 2: Add the founder photo**

Upload `IMG-...WA0001.jpg` into the Image block matching its placement in the mockup.

- [ ] **Step 3: Visual diff at three breakpoints**

Same process as Task 11 Step 4, comparing against `About V2.html`.

- [ ] **Step 4: Commit**

No local files changed; skip commit for this task.

---

## Task 14: Rebuild the Contact page

**BLOCKED** by Task 6.

**Files:** Reference only: `Contact V2.html`

**Interfaces:**
- Consumes: sitewide brand tokens (Task 6); `Contact V2.html`
- Produces: the live Contact page

- [ ] **Step 1: Rebuild each section with native blocks**

Following `Contact V2.html`'s section order ("What Can We Help You With?", "I Need Dispatch Support", "I Have An Academy Question", "Something Else"), rebuild using Squarespace's native Form block for the contact form (matching the field set implied by the mockup's three inquiry-type options), plus Text/Button blocks for the rest.

- [ ] **Step 2: Handle the consultation/booking option per Task 4's findings**

Same as Task 12 Step 2 — preserve an existing embed if Task 4 found one, otherwise build a new consultation/booking option here.

- [ ] **Step 3: Test-submit the form**

Submit a test entry through the live Form block and confirm it's received (in Squarespace's Form submissions or the connected notification email).

- [ ] **Step 4: Visual diff at three breakpoints**

Same process as Task 11 Step 4, comparing against `Contact V2.html`.

- [ ] **Step 5: Commit**

No local files changed; skip commit for this task.

---

## Task 15: Rebuild the Dispatch Academy landing page

**BLOCKED** by Task 6 and Task 7 (needs the Course's actual URL to link to).

**Files:** Reference only: `Dispatch Academy v3.html`

**Interfaces:**
- Consumes: sitewide brand tokens (Task 6); `Dispatch Academy v3.html`; live Course URL (Task 7)
- Produces: the live Dispatch Academy sales/landing page, which is a separate regular page from the Course Overview page itself

- [ ] **Step 1: Rebuild each section with native blocks**

Following `Dispatch Academy v3.html`'s section order ("Learn The Business.", "Built For People New To Dispatching", "A Practical Foundation In Freight Dispatching", "15-Module Beginner Freight Dispatcher Course", "Built To Be Practical, Not Just Theoretical", "$350 Total Tuition", "45-Question Final Assessment", "Complete The Course. Earn Your Certificate."), rebuild using native Text/Image/Button blocks, copying text verbatim.

- [ ] **Step 2: Point the enrollment button at the live Course**

Set the primary CTA button's link target to the actual Course Overview page URL from Task 7 (which is also where the $350 checkout happens, per Task 5).

- [ ] **Step 3: Visual diff at three breakpoints**

Same process as Task 11 Step 4, comparing against `Dispatch Academy v3.html`.

- [ ] **Step 4: Commit**

No local files changed; skip commit for this task.

---

## Task 16: End-to-end verification pass

**BLOCKED** by all preceding tasks being complete.

**Files:** Create `docs/superpowers/content/final-verification-report.md`

**Interfaces:**
- Consumes: the fully built live site (Tasks 4-15)
- Produces: a signed-off verification report

- [ ] **Step 1: Full student journey as a fresh test member**

Using a throwaway test email: land on the Dispatch Academy landing page (Task 15), click through to enroll, complete checkout with a fresh $0 comp coupon (create and delete it the same way as Task 5 Step 3-4), land on the Course Overview, complete Lesson 01, confirm the progress bar updates, click through the empty Lessons 02-15 to confirm they render without errors, reach Lesson 16, take the ProProfs exam, score above 80%, and confirm certificate delivery.

- [ ] **Step 2: Cross-page brand consistency check**

With all 5 marketing pages and the Course live, click through Home → Dispatch Services → Dispatch Academy → About → Contact in one pass and confirm the header/footer/nav and brand tokens are visually identical across all of them.

- [ ] **Step 3: Mobile pass**

Repeat Step 1's journey at a 390px mobile width end-to-end, confirming no layout breaks and the ProProfs embed remains usable on a small screen.

- [ ] **Step 4: Write the verification report**

Create `docs/superpowers/content/final-verification-report.md` listing each check from Steps 1-3, pass/fail, and any follow-up items still needed (e.g., anything still blocked on a client answer from the spec's Open Questions that couldn't be tested).

- [ ] **Step 5: Rotate credentials**

Per the spec's Build-Access Model, rotate the Squarespace login password now that this work session's build is complete.

- [ ] **Step 6: Commit**

```bash
cd "/home/diolichat/truck" && git add docs/superpowers/content/final-verification-report.md && git commit -m "Add final verification report for Squarespace build"
```
