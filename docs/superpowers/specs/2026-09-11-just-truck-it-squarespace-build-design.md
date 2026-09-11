# Just Truck It — Squarespace Site & Dispatch Academy Build

## Context

Just Truck It Dispatching (freight dispatch service for owner-operators/small
carriers) needs its approved 5-page marketing site plus a paid "Dispatch
Academy" course built out in a live Squarespace 7.1 account. Everything is
already designed and approved (branding, copy, layout, HTML mockups) — this
is an implementation job, not a redesign.

Source materials (all in this directory):
- `Home Page V4.html`, `Dispatch Services v2.html`, `Dispatch Academy v3.html`,
  `About V2.html`, `Contact V2.html` — approved visual/design references for
  the 5 marketing pages. **These are references only and must not be pasted
  into Squarespace as full-page Code Blocks** — client requirement.
- `dispatch-academy-module-01-interactive-preview.html` — visual/style
  reference for one lesson's feel. Its custom JS sidebar/progress-dots/
  checkboxes are not required to be recreated; native Squarespace Course
  functionality should be used instead.
- `Just_Truck_It_Dispatch_Academy_Vendor_Course_Content.docx` — authoritative
  build sheet: full lesson content for Modules 01–15, the 45-question final
  assessment, the vendor-only answer key, and certificate wording rules.
- `IMG-...WA0000.jpg` (logo), `IMG-...WA0001.jpg` (founder photo) — brand
  assets for use across the site.

Client instruction, verbatim from the build sheet: "Use Module 01 as the
reusable visual/structural lesson template... I will populate modules 2–15
separately using provided content." So this build's scope for the Academy is
the course shell + Module 01 template, not writing out all 15 modules.

Client also stated Acuity booking + payment are "already embedded" in the
Squarespace site — treated as pre-existing infrastructure to build around,
not something this build stands up. **Unconfirmed**: whether the $350 Academy
purchase runs through Squarespace's own Member Areas/Commerce checkout or
through Acuity's payment processing — this must be confirmed with the client
before the paywall is wired up (see Open Questions).

## Goals

- Rebuild the 5 approved marketing pages natively in Squarespace 7.1 /
  Fluid Engine, matching the mockups' layout, copy, and brand styling.
- Build the Dispatch Academy as one paid ($350) Squarespace Course:
  Module 01 as the master lesson template, a course shell for Modules
  02–15, and a final assessment + certificate flow.
- Do this with minimal custom CSS/JS — native blocks wherever Squarespace
  supports the requirement.

## Non-Goals

- Writing lesson content for Modules 02–15 (client does this themselves,
  using the Module 01 template).
- Building new booking/payment infrastructure (Acuity + payment already
  exist; this build only needs to connect the Academy paywall to them).
- Recreating the interactive preview's custom sidebar/progress-dot/checkbox
  JavaScript — native Course navigation/progress replaces it.

## Key Finding: Assessment & Certificate Need a Third-Party Tool

Squarespace's native Courses/Member Areas has no built-in quiz, grading, or
certificate functionality (confirmed via research; Squarespace has only
hinted this is "in the works"). The build sheet requires a 45-question
graded assessment (80% to pass) with a **hidden, vendor-only answer key**,
plus certificate issuance on passing. A fully custom Squarespace Code Block
quiz was rejected because client-side scoring logic can't reliably hide the
answer key from a determined student — unacceptable given the explicit
"vendor-only" requirement.

**Decision: embed ProProfs Quiz Maker** (~$20/mo Business plan) via iframe/
code block for the final assessment. It natively supports scored MCQ exams,
keeps the answer key server-side (never exposed to test-takers), and
auto-issues a branded PDF certificate on passing — satisfying the
requirement without custom grading code. (Alternative considered: ClassMarker,
~$80/mo, more "corporate compliance exam" polish, rejected on cost grounds
pending client confirmation — see Open Questions.)

## Course Structure

Mapped onto Squarespace's native Course collection (Chapter → Lessons,
sidebar navigation, progress bar, "Complete and Continue" button):

- **Course Overview page ("Start Here")** — course description, the
  welcome/how-it-works/before-you-begin copy from the docx, a "Start
  Course" button, progress bar.
- **One Chapter** ("Dispatch Academy Course") containing **15 Lessons**
  = Modules 01–15. Each Lesson follows the fixed hierarchy from the docx:
  Module Introduction → What You'll Learn → The Lesson → Key Takeaways →
  Real-World Application → Put It Into Practice → Check Your Understanding
  → Self-Check → Module Complete → training disclaimer. Module 01 is built
  first as the literal template (native Text/Image/Button blocks); the
  client duplicates its structure for Modules 02–15.
- **Lesson 16 = Final Assessment** — instructions + the embedded ProProfs
  quiz iframe. Passing (≥80%) triggers ProProfs' own certificate delivery
  directly to the student. Retake policy: **unlimited retakes** (default,
  since the docx specifies no retry limit — pending client confirmation).
- **Paywall**: the whole Course gated behind a one-time $350 charge via
  Squarespace's Member Areas paid-course checkout, kept separate from
  Acuity (consultation-booking calendar only) — **pending client
  confirmation** of which system actually processes the Academy payment.

## Build-Access Model

There is no code repository or CLI for the live Squarespace site — the only
way to place pages, blocks, or Course content is through Squarespace's own
web editor while authenticated. This build will be executed via browser
automation (Playwright), driven interactively so actions are visible as they
happen. Billing/domain settings will not be touched. Credentials should be
temporary/rotatable and rotated again once each work session ends.

Current live-site state is unknown and must be audited (read-only pass)
before any build steps: what pages/course/payment setup already exist,
current plan tier, and how Acuity/payment are actually wired.

## Marketing Pages Approach

Each of the 5 pages is rebuilt as native Fluid Engine sections (Text, Image,
Button, Spacer, Gallery blocks) matching the mockup's layout, spacing, and
copy section-by-section. Brand tokens (lime/black palette, Oswald/Inter
fonts) go into Squarespace's site-wide Design panel rather than per-page
custom code. The existing Acuity embed on Contact/Services is left as-is.

## Verification Plan

Once credentials are available and pages/course are built:
- Diff each rebuilt marketing page against its mockup at desktop/tablet/
  mobile widths.
- As a test member: enroll, progress through Module 01, confirm
  lesson-unlock and progress bar behavior, confirm the ProProfs quiz embed
  loads and grades correctly on mobile and desktop, run one certificate
  end-to-end.
- Confirm the $350 checkout charges correctly using a test/comp coupon
  rather than a real card.

## Homepage Music (resolved separately)

Client requested OutKast's "The Way You Move" as homepage background music.
A licensed copy of a commercial recording cannot be sourced by this build —
that requires a sync/public-performance license the client must obtain
independently. A click-to-play (not autoplay-with-sound, per browser policy)
music toggle scaffold has already been added to `Home Page V4.html`,
pointing at a placeholder `audio/homepage-theme.mp3`. Swapping in a licensed
file (or a royalty-free alternative) is all that remains once the client
decides.

## Open Questions for Client

1. Approve the ~$20/mo ProProfs Quiz Maker cost (or prefer ClassMarker at
   ~$80/mo)?
2. Squarespace site URL + access (temporary/rotatable login, or Contributor
   invite) — and what's already built there today?
3. Does the $350 Academy charge run through Squarespace Commerce/Member
   Areas, or through Acuity's payment processing?
4. Confirm modules 02–15 are still self-populated by the client using the
   Module 01 template (not in this build's scope).
5. Hard delivery deadline, if any (they asked us this and never got an
   answer back).
6. Certificate design specifics: logo placement, signature name/title.
7. Assessment retake policy — unlimited (default assumption) or limited?
8. Licensed audio file (or royalty-free alternative) for homepage music, or
   drop the feature.
9. Is a custom domain already connected, or still on a squarespace.com
   subdomain?
