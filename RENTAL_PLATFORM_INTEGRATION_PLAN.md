# Short-Term Rental Integration Plan

Goal: a single website that aggregates listings and unifies messaging across the major short-term rental (STR) marketplaces (Airbnb, Vrbo, Booking.com, and adjacent channels).

The hardest part of this project is not the code — it's getting sanctioned API access. Most of the big STR platforms do not offer open developer APIs; access is gated behind a partner/connectivity program that expects you to be an established Property Management System (PMS), channel manager, or host with a real portfolio. Phase 0 exists to prevent you from building against an API you can't legally ship on.

---

## Phase 0 — Reality check on API access (do this first, before any code)

Before chasing credentials, confirm which path you're on. Each platform only hands out access to one of a few archetypes:

1. **Host / Property Manager** — you own or manage listings. Some platforms give this persona limited API access.
2. **PMS / Channel Manager** — you build software that other hosts use to manage their listings. This is where the real connectivity APIs live, but approval is slow and has business-viability gates.
3. **Travel/OTA Partner** — you resell inventory (redistribution). Different agreements entirely.
4. **End-user consumer app** — essentially unsupported by Airbnb/Vrbo/Booking. Scraping is against ToS and will get the account banned.

Decide which archetype best describes the product. For a unified host dashboard with messaging, archetype #2 (PMS/channel manager) is the only path that gets you official access to all three big platforms — and it's a multi-month approval process. If this is for a handful of your own listings, archetype #1 plus a commercial channel manager (Hostaway / Hospitable / Guesty) as a middleware is dramatically faster.

**Deliverable for this phase:** a one-pager that states which archetype you're pursuing and the implications (timeline, portfolio requirements, certification scope).

---

## Phase 1 — Create host accounts and list at least one real property

You cannot apply for connectivity/partner APIs without an active host account and, in most cases, live listings. Set these up first:

- **Airbnb** — sign up at airbnb.com, complete host onboarding, publish at least one listing, get through a couple of bookings to build account standing. API partner applications reference your host account.
- **Vrbo / Expedia Group** — register at vrbo.com, then request access to **Expedia Partner Central** for the same property.
- **Booking.com** — register the property via join.booking.com → this creates an **Extranet** account (this is the host-facing portal that connectivity APIs authenticate against).
- **Google Vacation Rentals** — requires a Google Hotel Center / Travel Partner account; only useful later if you want to syndicate.
- **TripAdvisor Rentals / Holiday Lettings** — mostly migrated into Vrbo; list once under Vrbo and it fans out.
- **Agoda Homes, Marriott Homes & Villas, Plum Guide** — optional; deprioritize until the big three are working.

**Deliverable:** one real listing active on Airbnb, Vrbo, and Booking.com, each with a verified host identity.

---

## Phase 2 — Apply for official API access

Applications take weeks to months. Start them in parallel as soon as Phase 1 is active.

### 2a. Airbnb
- There is **no public Airbnb API**. The only sanctioned programmatic access is the **Airbnb Software Partner** program (partners.airbnb.com → "Become a software partner").
- Two partner tracks exist: **PMS Software Partner** (calendar, listings, reservations, messaging) and **Marketing/Preferred Software Partner**.
- Requirements are non-trivial: signed NDA, technical integration spec review, production certification, a minimum portfolio under management (historically ~100+ listings connected within a set window), and SOC2-ish security posture.
- If you don't qualify, the realistic workaround is to integrate via an approved partner (see Phase 3).

### 2b. Vrbo / Expedia Group
- Program name: **Vrbo Software Integration / Expedia Group Connectivity**.
- Two APIs matter: **Property API** (listings, rates, availability) and **Reservation/Message API**.
- Apply via the connectivity portal at the Expedia Partner Solutions site. Expect a technical questionnaire, a sandbox certification period, and a required set of certification test cases you must pass against mock listings before production credentials are issued.

### 2c. Booking.com
- Program: **Booking.com Connectivity Partner** (connectivity.booking.com).
- Must choose a provider type: **Property Connectivity Provider** (PMS/channel manager) or **Distribution Partner**. Pick PMS.
- Certification is the most rigorous of the three: sandbox, functional test cases per API (Rooms, Rates, Availability, Reservations, Messaging, Reviews, Opportunity Center), and a compliance review.
- Booking.com has a first-class **Messaging API** — important for the messaging half of this project.

### 2d. Channel-manager / aggregator APIs (the practical fallback)
If the direct-partner path is too slow, these let you reach all the big OTAs through one integration:
- **Hostaway API** — public developer portal, OAuth, covers Airbnb/Vrbo/Booking via their own certifications.
- **Hospitable (formerly Smartbnb) Public API** — strong on Airbnb messaging.
- **Guesty for Hosts / Guesty Pro Open API** — broader coverage, heavier onboarding.
- **Lodgify, OwnerRez, iGMS** — similar tradeoffs.

A realistic v1 strategy: connect your listings to one channel manager, integrate against *its* API, and defer direct OTA partnerships until the product has traction.

**Deliverable:** sandbox credentials (or channel-manager API key) for at least one platform, plus written status for each direct-partner application (submitted / awaiting review / certified).

---

## Phase 3 — Map the messaging surface

Messaging is the hardest unification problem because each platform exposes it differently:

| Platform | Inbound | Outbound | Attachments | Webhook/push? |
|---|---|---|---|---|
| Airbnb (via partner API or Hospitable) | Yes | Yes | Limited | Webhook |
| Booking.com Messaging API | Yes | Yes | Yes | Webhook (pull fallback) |
| Vrbo/Expedia Messaging | Yes | Yes | Limited | Webhook |
| Direct (your own site) | Email/SMS | Email/SMS | Yes | n/a |

Document per platform: auth model (OAuth vs API key vs signed header), rate limits, message templating rules, prohibited content (phone numbers, off-platform links — these will get accounts suspended), and retention/PII rules.

**Deliverable:** a `docs/messaging-matrix.md` capturing the above as ground truth before schema design.

---

## Phase 4 — Architecture (only once Phase 2 is unblocking)

Sketch once you actually have one working credential. Expected shape:

- **Ingestion workers** per platform — normalize listings, reservations, messages into internal models.
- **Canonical data model** — `Listing`, `Reservation`, `Guest`, `Thread`, `Message`, with a `source_platform` + `source_id` on each.
- **Outbound dispatcher** — takes a canonical message and routes it to the right platform client, handling per-platform content restrictions.
- **Webhook receiver** — single endpoint that dispatches by platform with HMAC verification.
- **Frontend** — unified inbox + listing dashboard. Static site on GitHub Pages won't suffice once auth and webhooks are in the loop; plan on a real backend (e.g. a small Node/Go service behind Cloudflare, or a serverless setup).

---

## Phase 5 — MVP build order

1. One platform end-to-end (recommend **Booking.com** via connectivity sandbox, or **Hospitable** if going the channel-manager route — both have the cleanest messaging APIs).
2. Unified inbox UI against that single source.
3. Second platform added — this is where the canonical model gets stress-tested.
4. Listing sync (read-only first, then two-way).
5. Third platform, then polish.

Do not try to light up all three directly at once; the certification loops are sequential in practice.

---

## Phase 6 — Compliance and ToS guardrails

- No scraping. Every one of these platforms' ToS prohibits it, and they actively ban.
- Store messages encrypted at rest; most partner agreements require it.
- Guest PII: treat as GDPR/CCPA-covered from day one.
- Don't strip platform branding from messages in the unified inbox — some partner agreements require attribution.
- If you use a channel manager as middleware, their ToS flows through to you — read it.

---

## Immediate next actions (this week)

1. Pick the archetype (Phase 0) and write it down.
2. Create Airbnb, Vrbo, and Booking.com host accounts and publish one listing on each.
3. Submit the Booking.com Connectivity Partner application and the Vrbo/Expedia Connectivity application — these have the longest lead time.
4. In parallel, sign up for a **Hostaway** or **Hospitable** developer account as the fallback path so you're not blocked on certification.
5. Start `docs/messaging-matrix.md` using the public docs for each platform, even before credentials arrive.
