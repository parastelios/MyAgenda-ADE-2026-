# Availability source review

Human-readable audit for [event-sources.json](event-sources.json). Research date: 2026-10-07. Nothing in `index.html` or `availability.json` was changed by this pass.

## How to read event-sources.json

Keys are the stable event IDs from `eventsData`. Each event has:

- `primary`: the most event-specific official source found. `type` is `ticket_shop`, `official_event_page`, `ade_page`, `free_rsvp`, `generic_homepage` or `unknown`. `status_detection` is `structured`, `html_signal`, `manual` or `none`.
- `primary.confidence` (`high`, `medium`, `low`, `none`) is the confidence that the URL is the page for **this exact event** (right artist or show, day, venue, time). It is not a statement about how easy detection is; that is `status_detection`.
- `status_detection` is `html_signal` only where a real sold-out marker was seen on that provider. Providers that only load JavaScript, block scripts, or disallow automated access in `robots.txt` are `none`.
- `ade_url`: the ADE 2026 program page for the event, when one exists.
- `resale`: the TicketSwap mapping (see below). `other_resale_url` is a non-TicketSwap resale page.
- `current_links`: the `eventLink` / `ticketLink` in `index.html` right now. `proposed_links`: suggested changes, with the reason.
- `primary.observed_2026_10_07`: what the source showed on the research date, where it could be read.

## Summary (67 events)

Free events are kept separate and are not searched for resale (the app marks 11 events as free).

| Category | Events | IDs |
|---|---|---|
| High-confidence primary URL | 19 | 6, 8, 9, 11, 12, 17, 20, 21, 22, 23, 31, 33, 40, 49, 51, 52, 60, 62, 63 |
| ...of which status can be read automatically (`html_signal`) | 10 | 8, 9, 11, 12, 17, 21, 22, 23, 40, 60 |
| ...high-confidence URL but no automatic reading (JavaScript shop, blocked, or disallowed) | 9 | 6, 20, 31, 33, 49, 51, 52, 62, 63 |
| High-confidence TicketSwap mapping | 0 | none |
| Both primary and TicketSwap high-confidence | 0 | none |
| Primary only (high-confidence) | 19 | same as the first row |
| TicketSwap only | 0 | none |
| Free / manual | 11 | 2, 3, 4, 7, 10, 16, 19, 28, 35, 47, 61 |
| Needs verification (medium or low confidence) | 36 | 1, 5, 13, 14, 15, 18, 24, 25, 26, 27, 29, 30, 32, 36, 37, 38, 39, 41, 42, 43, 44, 45, 46, 48, 50, 53, 54, 55, 56, 57, 58, 59, 64, 65, 66, 67 |
| No usable source | 1 | 34 (plus 47, which is also free) |

The 11 free events are counted once, under Free / manual. The other 56 split into 19 high-confidence, 36 needing verification and 1 with no usable source.

### Can primary availability be checked automatically with high confidence?

**About 15%: 10 of 67 events.** These are events where the right page was matched exactly and the provider shows a readable per-event marker: 8, 9, 11, 12, 17, 21, 22, 23, 40, 60.

A realistic ceiling is **about 27% (18 of 67)**, if the 8 medium-confidence matches are verified: 24, 25, 39, 44, 45, 50, 55, 59. Beyond that, the remaining events sit behind JavaScript-only shops (Weeztix, cm.com, Celebratix), shops that disallow automated access (Stager, Paylogic, the Into the Woods shop), or sites that block scripts (RA, fourvenues), or they have no event-specific page at all.

Caveats on the 15%:

- Detection can only produce `soldout` or `available`. `free` and `mychoice` (Tickets Already Bought) stay manual. Events 9 and 17 are in the list but are `mychoice` in the app, so a manual override must win there.
- For Awakenings (9) the 'available' state is inferred from the absence of a 'Sold out' label. For the Gashouder shop (12, 22) no 'available' page was seen, so only the sold-out marker is confirmed.
- Thuishaven, Het Sieraad, Free Your Mind and Lofi show an explicit TICKETS label, which is a positive available signal.
- These are markers on today's HTML. Any site redesign breaks them, so each needs a guard that falls back to the previous value when a marker is missing.
- Only robots.txt was checked, not each site's terms of use.

## What the providers expose

| Provider | Events | What was seen | Verdict |
|---|---|---|---|
| Awakenings (`awakenings.com`) | 9, 21 (45, 59 medium) | Every ADE page lists all ADE shows and adds 'Sold out' only to sold-out ones. | `html_signal`, one fetch covers all shows |
| Gashouder shop (vivenu) | 12, 22 (18 once its link is found) | Page title ends '[Sold Out]'; body says 'Sold out'. | `html_signal` |
| DGTL (`dgtl-festival.com`) | 40 (24, 55 medium) | Page title starts 'SOLD OUT - ' on all three pages checked. | `html_signal` |
| Thuishaven | 8, 60 | Listing rows carry TICKETS or SOLD OUT next to date and title. | `html_signal` |
| Het Sieraad | 17 (37 low) | Listing rows carry TICKETS or SOLD OUT. | `html_signal` |
| Free Your Mind | 11, 23 (54 low) | Home page list carries TICKETS or SOLD OUT per show. | `html_signal` |
| Lofi | 44 (64 low) | Listing rows carry Tickets or Sold out. | `html_signal` |
| Intercell | 50 | Event page shows a 'Sold out' label. | `html_signal` |
| Mojo (KI/KI) | 25, 39 | One page for both days; one 'UITVERKOCHT' label seen. | day attribution unverified |
| Weeztix, cm.com | 8, 13, 20, 23, 44 | Pages are JavaScript shells with no readable text. | `none` |
| Loveland | 20, 52, 63 | Ticket pages load a widget; no status text. | `none` |
| Celebratix, fourvenues | 62, 65 | HTTP 403 to scripts. | `none` |
| Stager, Paylogic, Into the Woods shop | 17, 51, 49 | robots.txt disallows automated access. | `none` (not fetched) |
| Resident Advisor | 6, 10, 26, 31 | HTTP 403 to scripts. | `none` |
| weticket (Skatecafe) | 33 | Server-rendered, but no sold-out marker has been seen on this provider. | `none` until a marker is seen |

## Observed on 2026-10-07 versus the app

The sources disagree with `availability.json` for these events. Nothing was changed; this is for the curator to review.

| ID | Event | App says | Source shows | Match confidence |
|---|---|---|---|---|
| 12 | Armin van Buuren & Benwal • Gashouder | available | soldout | high |
| 24 | DGTL ADE Thursday • NDSM Docklands | available | soldout | medium |
| 40 | DGTL Friday Night • NDSM Warehouse | available | soldout | high |
| 45 | Awakenings Saturday Daytime • Gashouder | available | soldout | medium |
| 50 | Intercell x Levenslang • Former Maximum-Security | available | soldout | medium |
| 55 | DGTL Saturday Showcase • NDSM Warehouse | available | soldout | medium |
| 60 | Thuishaven ADE Sunday Closing • Heated Hangars | available | soldout | high |

Events 8, 11, 23 and 44 show available and agree with the app. Events 21 and 59 show sold out and agree. Event 17 shows tickets on sale, but the app has it as `mychoice` (tickets already bought), which a ticket site cannot know, so that is not a conflict.

## TicketSwap

**No high-confidence TicketSwap mapping could be made.**

- TicketSwap returns a bot check (HTTP 202 or 403) to scripts, and the same to the fetch tool. It was not bypassed.
- Search results are mostly 2021-2025 event pages or evergreen festival landing pages. None could be verified as the 2026 edition with the same date.
- 14 events carry a low-confidence candidate: 9, 11, 21, 23, 24, 25, 39, 40, 45, 49, 52, 54, 55, 59. These are shared landing pages (`awakenings-ade`, `dgtl-ade`, `kiki-ade`, `free-your-mind-ade`, `909-loveland-ade`, `into-the-woods-ade`). One page can cover several days, so a festival-level count must not be read as one event's availability.
- No TicketSwap search URLs are stored. Three events currently use search URLs as their `ticketLink` (17, 18, 21), and 34, 38 and 59 use the generic `ticketswap.com/ade`.
- The Gashouder resale page for event 22 (`event.resale.gashouder.nl`) is stored as `other_resale_url`; it returns 403 to scripts.

## Broken or moved URLs you asked about

`Applied` means the fix is already in `index.html` (commit `ee95b50`, live on GitHub Pages).

| ID | Problem | Finding | Status |
|---|---|---|---|
| 9 | Awakenings ticket link 404 | Correct page: awakenings.com/en/events/2026/10/ade-opening-night/397108/ (verified, 200). | Applied |
| 14 | adamhotel.nl does not resolve | No Loft listing matches Thu 12:00-17:00. Fallback: theloftamsterdam.com (low). | Proposed, low confidence |
| 22 | ADE page 404 | Event exists as 'Gashouder Presents: I Hate Models & Nico Moreno Invite' (ADE 2804806); Gashouder ticket page says sold out. | Applied |
| 24 | ADE page 404; dgtl.nl moved | ADE page 2803914 and dgtl-festival.com/.../dgtl-ade-thursday/ exist. ADE time 17:00-23:00 vs the app's 23:00-04:00. | Links applied; time conflict open |
| 28 | Certificate error | veronicaschip.nl redirects to hetveronicaschip.nl (verified). | Applied |
| 31 | ADE page 404 | The 2026 page has a new id: ritter-butzke-x-casa-ade-cruise/2894214 (exact match); ticket shop is RA. | Proposed (ADE link not yet applied) |
| 32 | ADE page 404 | Match: Breakfast Club /w Kiosk Radio (ADE 2824185). ADE time Fri 18:00-04:00 vs the app's 10:00-18:00. | Link applied; time conflict open |
| 33 | ADE page 404 | Match: PIP GOES SKATECAFE (ADE 2811268), exact time; real shop skatecafe.weticket.io. | ADE link applied; shop link proposed |
| 40 | dgtl.nl moved | dgtl-festival.com/en/dgtl-ade/dgtl-ade-friday-night/ (event-specific). | Domain applied; specific link proposed |
| 42 | Domain does not resolve | No DNS records at 1.1.1.1 or 8.8.8.8. ADE's only Amsterdam Techno Sessions listing is the Thursday show (event 26). Fallback: clubjohndoe.nl (low). | Proposed, low confidence |
| 45 | awakenings.com redirect | Closest show is Joris Voorn (Sat 24 Oct, Sugarfactory). The app says Gashouder, so medium confidence. | Proposed, medium confidence |
| 46 | Certificate error | Same as 28: hetveronicaschip.nl. deephouseamsterdam.com returns 403 to scripts. | Ticket link applied |
| 47 | Domain does not resolve | No DNS records, and nothing found on ADE. No replacement. | Open; consider removing the link |
| 49 | intothewoodsfestival.nl down (Cloudflare 1016) | intothewoods.nl works. ADE has a Saturday page (2826667, exact match) and a ticket shop URL. | Domain applied; specific links proposed |
| 55 | dgtl.nl moved | Closest DGTL show is Novah & Friends (Sat 23:59-07:30). Medium confidence. | Domain applied; specific link proposed |

## Proposed corrections to `ticketLink` / `eventLink`

From `proposed_links` in `event-sources.json`. Events listed under 'already applied' show the final intended value; the rest are not in `index.html` yet.

| ID | Proposed eventLink | Proposed ticketLink | Reason |
|---|---|---|---|
| 2 | `ADE:zwart-goud-ade-day-1/2889003/` | `ADE:zwart-goud-ade-day-1/2889003/` | Current eventLink (events.musicofourdesire.com) returns 403 to scripts and is a third-party aggregator; the ADE page is official. |
| 7 | `ADE:the-social-hub-presents-off-the-record-with-kevin-saunderson/29051` | `ADE:the-social-hub-presents-off-the-record-with-kevin-saunderson/29051` | More specific than the generic thesocialhub.co homepage. |
| 9 | keep | `www.awakenings.com/en/events/2026/10/ade-opening-night/397108/` | Current ticketLink www.awakenings.com/en/events/ade/ returns 404. Already applied in `index.html`. |
| 14 | keep | `theloftamsterdam.com/` | Current ticketLink adamhotel.nl does not resolve. Fallback only, low confidence. |
| 17 | keep | `het-sieraad.nl/` | Current ticketLink is a TicketSwap search URL, which should not be stored. |
| 18 | `ADE:gashouder-presents-eric-prydz/2834978/` | `ADE:gashouder-presents-eric-prydz/2834978/` | Current eventLink is a dead ADE page (404); ticketLink is a TicketSwap search URL. |
| 20 | `ADE:paul-kalkbrenner-live-x-loveland/2846832/` | keep | Current eventLink is a dead ADE page (404). Already applied in `index.html`. |
| 21 | `ADE:awakenings-ade-drumcode/2806492/` | `www.awakenings.com/en/events/2026/10/drumcode/397113/` | Current ticketLink is a TicketSwap search URL. |
| 24 | `ADE:dgtl-ade-thursday/2803914/` | `dgtl-festival.com/en/dgtl-ade/dgtl-ade-thursday/` | Current eventLink is a dead ADE page (404); dgtl.nl moved to dgtl-festival.com. Event link already applied. |
| 28 | `hetveronicaschip.nl/` | `hetveronicaschip.nl/` | Old domain has a certificate error. Already applied in `index.html`. |
| 31 | `ADE:ritter-butzke-x-casa-ade-cruise/2894214/` | `ra.co/events/2426450` | Current ADE page (id 2824510) returns 404; the 2026 page has a new id. |
| 33 | `ADE:skatecafe-x-pip-den-haag/2811268/` | `skatecafe.weticket.io/ade-pip-goes-skatecafe` | Current ADE page returns 404. ADE link already applied; the weticket link is the real shop. |
| 40 | `dgtl-festival.com/en/dgtl-ade/dgtl-ade-friday-night/` | `dgtl-festival.com/en/dgtl-ade/dgtl-ade-friday-night/` | dgtl.nl moved. `index.html` currently points at the festival homepage; this page is event-specific. |
| 42 | `clubjohndoe.nl/` | `clubjohndoe.nl/` | Current domain does not resolve. Fallback only, low confidence. |
| 45 | keep | `www.awakenings.com/en/events/2026/10/joris-voorn-a-trip-to-galaxy/3972` | awakenings.com redirects to www.awakenings.com/en/. Medium confidence. |
| 46 | keep | `hetveronicaschip.nl/` | Old domain has a certificate error. Already applied in `index.html`. |
| 49 | `ADE:into-the-woods-ade-festival-saturday/2826667/` | `tickets.intothewoods.nl/e1d62665744343338282d223b8af47e6/tickets/e6c19` | Old domain intothewoodsfestival.nl is down (Cloudflare 1016). Domain swap to intothewoods.nl already applied; this is more specific. |
| 55 | `dgtl-festival.com/en/dgtl-ade/dgtl-ade-novah-and-friends/` | `dgtl-festival.com/en/dgtl-ade/dgtl-ade-novah-and-friends/` | dgtl.nl moved. Medium confidence on which DGTL show this is. |
| 59 | keep | `www.awakenings.com/en/events/2026/10/sunday-sessions/397253/` | Current ticketLink is generic ticketswap.com/ade. Medium confidence. |

## All 67 events

`Conf` is primary match confidence. `Det` is status detection. `TS` is the TicketSwap mapping confidence (candidates are low and unverified).

| ID | Title | Primary | Type | Conf | Det | TS | Notes |
|---|---|---|---|---|---|---|---|
| 1 | Loveland Day One • ADE Opening | loveland.nl/ade/ | generic_homepage | low | none | none | No Loveland ADE show found for Wed 21 Oct 12:00-17:00 at De Marktkantine (ADE and loveland.nl list 11 shows, none match). Needs verification that... |
| 2 | Zwart Goud ADE Instore Sessions | amsterdam-dance-event.nl/en/program/2026/zwart | ade_page | medium | manual | none | ADE: Zwart Goud ADE Day 1, Wed 21 Oct 16:00-21:00 at Zwart Goud (app says 12:00-17:00, from 16:00). Free store session. Free in the app; keep manual. |
| 3 | Jeff Mills: The Night Watch (Screening) | amsterdam-dance-event.nl/en/program/2026/audio | ade_page | low | manual | none | ADE lists this Rijksmuseum show on Thu 22, Fri 23, Sat 24 and Sun 25 Oct 12:00-16:00 only, not Wed 21. Day differs from the app. Ticketing unclear... |
| 4 | ADE Lab Discovery & Gear Playground | amsterdam-dance-event.nl/en/ade-lab/ | official_event_page | medium | manual | none | ADE's official ADE Lab page. Generic, not a single ticketed event. Free in the app; keep manual. |
| 5 | OGUZ: One Million Black Tour Amsterdam | amsterdam-dance-event.nl/en/program/2026/one-m | ade_page | medium | none | none | ADE: OGUZ One Million Black Tour, Wed 21 Oct 22:00-01:00 at WestWeelde (Klonneplein). App says De Unie, 17:00-23:00. Venue and time conflict. ADE... |
| 6 | The Early Shift • Pizza & Rave Sessions | nl.ra.co/events/2531051 | ticket_shop | high | none | none | ADE: The Early Shift, Wed 21 Oct 18:00-00:00 at Toekomstmuziek. Ticket shop is Resident Advisor, which returns 403 to scripts. |
| 7 | Off The Record: Kevin Saunderson Special | amsterdam-dance-event.nl/en/program/2026/the-s | ade_page | medium | manual | none | ADE: Off the Record with Kevin Saunderson, Wed 21 Oct 19:00-22:00 at The Social Hub. Free in the app; keep manual. |
| 8 | In Trance We Trust • Thuishaven Showcase | thuishaven.nl | official_event_page | high | html_signal | none | Thuishaven's listing has a row 'In Trance we Trust ... 21-10-2026 21.00-04.00' with a TICKETS or SOLD OUT label. Match by date and title. Ticket... |
| 9 | 📌 Awakenings Opening Night • Warehouse | awakenings.com/en/events/2026/10/ade | official_event_page | high | html_signal | low | Awakenings event page. Every Awakenings ADE page lists all ADE shows and labels sold-out ones 'Sold out'; Opening Night has no label. |
| 10 | Lobster invites Bisque, Mella Dee & Samu | amsterdam-dance-event.nl/en/program/2026/lobst | ade_page | medium | manual | none | ADE: Lobster invites Bisque, Kyra Khaldi, Mella Dee b2b Samuel Deep, Wed 21 Oct 22:00-07:00 at BRET (app says 23:00-07:00). No ticket link on the... |
| 11 | Free Your Mind x TeleTech ADE Kickoff | freeyourmindfestival.nl | official_event_page | high | html_signal | low | Free Your Mind's home page lists '21 oct Free Your Mind x Teletech ADE, United Studio's, 10:00 pm' with a TICKETS or SOLD OUT label. |
| 12 | Armin van Buuren & Benwal • Gashouder | tickets.gashouder.nl/s/fUPFba10 | ticket_shop | high | html_signal | none | Gashouder's own ticket page. Page title carries '[Sold Out]' and the body says 'Sold out'. |
| 13 | OUTKZT 2026 • Boat Rave | shop.weeztix.com/a426b640-2859-11f1-a9 | ticket_shop | medium | none | none | ADE: OUTKZT 2026, Wed 21 Oct 16:00-04:00 at Het Veronica Schip. App says Thu 12:00-17:00, so the day differs. Weeztix is a JavaScript shell with... |
| 14 | The Loft Sessions • 16th Floor Penthouse | theloftamsterdam.com | generic_homepage | low | none | none | Cannot tie this event to a specific Loft listing (Thu 12:00-17:00 on the 16th floor). The Loft's own site lists ADE shows with 'Sold out' labels,... |
| 15 | OT301 ADE Underground Sessions | ot301.nl | generic_homepage | low | none | none | No ADE listing found for OT301 on ADE 2026. Generic homepage. |
| 16 | BOMA x Nude Project In-Store Rave | amsterdam-dance-event.nl/en/program/2026/boma- | ade_page | high | manual | none | ADE: NUDE PROJECT xxx BOMA, Thu 22 Oct 18:00-21:00 at Nude Project Store. In-store, free. Free in the app; keep manual. |
| 17 | 📌 Miss Monique presents Siona Records | het-sieraad.nl | official_event_page | high | html_signal | none | Het Sieraad's listing has 'ADE / Miss Monique presents Siona ... THU 22 OCT 17:00-22:30' with TICKETS or SOLD OUT. Shop behind it is Stager... |
| 18 | Awakenings • Eric Prydz Sunset Session | amsterdam-dance-event.nl/en/program/2026/gasho | ade_page | medium | none | none | ADE: Gashouder Presents Eric Prydz, Thu 22 Oct 18:00-22:00 at Gashouder (right event). The ADE page has no ticket-shop link. Gashouder uses vivenu... |
| 19 | Five Pizzas x Kitchen Frequencies | amsterdam-dance-event.nl/en/program/2026/five- | ade_page | high | manual | none | ADE: Five Pizzas ADE Session, Thu 22 Oct 17:30-22:00. Free in the app; keep manual. |
| 20 | Paul Kalkbrenner LIVE x Loveland | shop.tickets.cm.com/77c1c778-023b-1153-0a | ticket_shop | high | none | none | ADE: Paul Kalkbrenner LIVE x Loveland, Thu 22 Oct 19:00-22:30 at Theater Amsterdam (app says 17:00-22:30). The shop is a JavaScript shell with no... |
| 21 | Awakenings • Drumcode Showcase | awakenings.com/en/events/2026/10/dru | official_event_page | high | html_signal | low | Awakenings Drumcode page. The show list marks it 'Sold out'. Same list covers all Awakenings ADE shows. |
| 22 | I Hate Models & Nico Moreno Invite • Gas | tickets.gashouder.nl/s/hDH1zEBO | ticket_shop | high | html_signal | none | Gashouder ticket page; title '[Sold Out]'. Curator-provided resale page: event.resale.gashouder.nl (returns 403 to scripts). |
| 23 | Free Your Mind x TeleTech • 4 The People | freeyourmindfestival.nl | official_event_page | high | html_signal | low | Free Your Mind home page lists '22 oct Free Your Mind x Teletech: 4 the People, Hemkade 48, 6:00 pm' with TICKETS or SOLD OUT. The shop is cm.com... |
| 24 | DGTL ADE Thursday • NDSM Docklands | dgtl-festival.com/en/dgtl-ade/dgtl-ade- | official_event_page | medium | html_signal | low | DGTL page title starts with 'SOLD OUT - ' when sold out. ADE lists DGTL Thursday 17:00-23:00 at NDSM Warehouse; the app says 23:00-04:00 (peak... |
| 25 | KI/KI 5 Hours Non-Stop • Ziggo Dome | mojo.nl/concerten/kiki | official_event_page | medium | html_signal | low | Promoter page for KI/KI 5 Hours on 22 and 23 Oct at Ziggo Dome. One page, two day sections, with a 'Kaarten UITVERKOCHT' label that appears once.... |
| 26 | Amsterdam Techno Sessions • Late Basemen | ra.co/events/2519124 | ticket_shop | medium | none | none | ADE: Amsterdam Techno Sessions x Paradox Music, Thu 22 Oct 22:00-08:00 at Club John Doe. App times (04:00-08:00+) look like the late part only. RA... |
| 27 | The Loft Friday Sessions • 16th-Floor Pa | theloftamsterdam.com | generic_homepage | low | none | none | No matching Loft listing for Fri 12:00-17:00. The Loft's own site lists ADE shows with 'Sold out' labels, but none matches this event. |
| 28 | Vinyl & Waves Session • Het Veronica Sch | hetveronicaschip.nl | generic_homepage | low | manual | none | No ADE listing found for a vinyl session at Het Veronica Schip. veronicaschip.nl redirects to hetveronicaschip.nl. Free in the app; keep manual. |
| 29 | Audio-Visual Experience • NDSM Loods | ndsmloods.nl | generic_homepage | low | none | none | No ADE listing found. Generic homepage. |
| 30 | ADE Lab Friday Sessions • Westergas | amsterdam-dance-event.nl/en/ade-lab/ | official_event_page | low | manual | none | Generic ADE Lab page. No separate Friday listing found. |
| 31 | Ritter Butzke x Casa ADE Cruise • Sailin | ra.co/events/2426450 | ticket_shop | high | none | none | ADE: Ritter Butzke x Casa ADE Cruise, Fri 23 Oct 14:00-21:00 at SUPPER Cruise (exact match). RA is blocked to scripts. |
| 32 | Breakfast Club x Kiosk Radio • Beach Pav | amsterdam-dance-event.nl/en/program/2026/break | ade_page | medium | none | none | ADE: Breakfast Club /w Kiosk Radio, Fri 23 Oct 18:00-04:00 at Pllek. App says 10:00-18:00, so the time differs. No ticket link on the ADE page. |
| 33 | PIP Goes Skatecafe • Halfpipe Skatepark  | skatecafe.weticket.io/ade-pip-goes-skatecaf | ticket_shop | high | none | none | ADE: PIP GOES SKATECAFE, Fri 23 Oct 16:00-04:00 at Skatecafe (exact match). The shop page is server-rendered, but no sold-out marker has been seen... |
| 34 | Awakenings presents KNTXT / Filth on Aci | awakenings.com/en/ | generic_homepage | none | none | none | No Awakenings, KNTXT or Filth on Acid listing found for Fri 18:00-22:30 at Gashouder on ADE or awakenings.com. Awakenings' 2026 ADE shows are at... |
| 35 | Craft Pizza & Beats • Five Pizzas Store  | amsterdam-dance-event.nl/en/program/2026/five- | ade_page | low | manual | none | The only Five Pizzas ADE page is the Thursday session (event 19); no Friday pop-up found. Free in the app; keep manual. |
| 36 | KiNK Live x Loveland • Mediahaven Studio | loveland.nl/ade/ | generic_homepage | low | none | none | No KiNK Live show among Loveland's 11 ADE listings; Friday has Mahmut Orhan and Paradise. Needs verification. |
| 37 | Dynamic Melodic Live • Het Sieraad Glass | het-sieraad.nl | official_event_page | low | html_signal | none | Closest Het Sieraad listing is 'ROSE RINGED PRESENTS HRMNY', Fri 23 Oct 17:00-23:00 (app: 18:00-22:30, 'Dynamic Melodic Live'). The title does not... |
| 38 | Exhale by Amelie Lens • Sugarfactory Ref | audio-obscura.com/ade-x-exhale/ | official_event_page | low | none | none | Exhale by Amelie Lens exists at Audio Obscura (G-Star RAW), Fri 23 Oct 16:00-23:00, but the app says Sugarfactory 23:00-07:00. Venue and time... |
| 39 | KI/KI 5 Hours - Day 2 • Ziggo Dome | mojo.nl/concerten/kiki | official_event_page | medium | html_signal | low | Same promoter page as event 25; this is the 'vrijdag 23 oktober' section. Day attribution of the sold-out label needs verification. |
| 40 | DGTL Friday Night • NDSM Warehouse | dgtl-festival.com/en/dgtl-ade/dgtl-ade- | official_event_page | high | html_signal | low | DGTL ADE Friday Night, Fri 23 Oct 23:30-06:30 at NDSM Warehouse. Title starts 'SOLD OUT - ' when sold out. |
| 41 | United Techno Heavy Hitters • United Stu | unitedtechno.com | generic_homepage | low | none | none | No United Techno ADE listing found on ADE. Generic homepage. |
| 42 | Amsterdam Techno Sessions Day 3 • Club J | clubjohndoe.nl | generic_homepage | low | none | none | No ADE listing for an Amsterdam Techno Sessions after-hours at Club John Doe on Fri. amsterdam-techno-sessions.com does not resolve (no DNS... |
| 43 | Radion Friday Afterhours • The Concrete  | radion.amsterdam | generic_homepage | low | none | none | No ADE listing for a Friday afterhours at Radion. Radion sells via Stager (disallowed by robots.txt). |
| 44 | Lofi Friday Late Session • Intimate Acid | lofi.amsterdam | official_event_page | medium | html_signal | none | Lofi's site lists 'ADE / VBX Friday 23.10.2026' with Tickets or Sold out. ADE page: VBX x Lofi, Fri 23 Oct 23:30-08:00. App says 04:00-09:00+... |
| 45 | Awakenings Saturday Daytime • Gashouder | awakenings.com/en/events/2026/10/jor | official_event_page | medium | html_signal | low | Closest Awakenings show: Joris Voorn, Sat 24 Oct 13:00-21:30 at Sugarfactory. App says 'Saturday Daytime' at Gashouder 12:00-22:00. Day and... |
| 46 | Deep House Amsterdam Showcase • Het Vero | hetveronicaschip.nl | generic_homepage | low | none | none | No ADE listing found for Deep House Amsterdam at Het Veronica Schip. deephouseamsterdam.com returns 403 to scripts. veronicaschip.nl redirects to... |
| 47 | Rhythm Control Instores • Record Store P | - | unknown | none | none | none | Rhythm Control Records: rhythmcontrol.nl does not resolve (no DNS records) and nothing was found on ADE. Free in the app. Needs verification that... |
| 48 | Warehouse Elementenstraat Special • Audi | elementenstraat.nl | generic_homepage | low | none | none | No ADE listing found. Generic homepage (its RA link is not checkable). |
| 49 | Into the Woods ADE Festival • Enchanted  | tickets.intothewoods.nl/e1d62665744343338282d | ticket_shop | high | none | low | ADE: Into the Woods ADE Festival Saturday, Sat 24 Oct 12:00-23:00 (exact match). The shop host's robots.txt disallows automated access. Official... |
| 50 | Intercell x Levenslang • Former Maximum- | intercell.events/events/intercell-x-er | official_event_page | medium | html_signal | none | Intercell x Eris Drew & Octo Octa ADE By Day, Sat 24 Oct 14:00-23:00 at Bajes Amsterdam (a former prison). Time matches the app; the app's title... |
| 51 | 📌 Johan Cruijff ArenA Main Event • AMF S | shop.paylogic.com/5fedd8b869f24d9a9204e | ticket_shop | high | none | none | ADE: AMF at Johan Cruijff ArenA, Sat 24 Oct 21:00-05:00. The Paylogic shop's robots.txt disallows automated access. Tickets already bought... |
| 52 | Loveland Saturday • Mediahaven Studios | loveland.nl/ade/909-x-loveland/ti | ticket_shop | high | none | low | ADE: 909 x Loveland, Sat 24 Oct 12:00-22:00 at Mediahaven (time and venue match the app). The ticket page loads a widget with no readable status. |
| 53 | OT301 Saturday Live • Gritty Pre-Party | ot301.nl | generic_homepage | low | none | none | No ADE listing found for OT301 Saturday. Generic homepage. |
| 54 | Free Your Mind Saturday Rave • Warehouse | freeyourmindfestival.nl | official_event_page | low | html_signal | low | Free Your Mind lists many ADE shows (Free Your Mind Club, Hemkade, United Studio's). None clearly matches 'Saturday Rave' at H7 Warehouse. ADE has... |
| 55 | DGTL Saturday Showcase • NDSM Warehouse | dgtl-festival.com/en/dgtl-ade/dgtl-ade- | official_event_page | medium | html_signal | low | Closest DGTL show: Novah & Friends, Sat 24 Oct 23:59-07:30 at NDSM Warehouse (app: 22:00-07:00 'Showcase'). Title starts 'SOLD OUT - '. |
| 56 | Radion Saturday Night / Afterhours • Cha | radion.amsterdam | generic_homepage | low | none | none | No ADE listing found for a Saturday afterhours at Radion. |
| 57 | Club John Doe Extended Sessions • Hard L | clubjohndoe.nl | generic_homepage | low | none | none | No matching Club John Doe ADE listing found. |
| 58 | Shelter Amsterdam Late Run • Intimate Me | shelteramsterdam.nl | generic_homepage | low | none | none | No matching Shelter listing found. Shelter sells via eventix/fourvenues. |
| 59 | Awakenings Sunday Closing Marathon • Gas | awakenings.com/en/events/2026/10/sun | official_event_page | medium | html_signal | low | Closest Awakenings show: Sunday Sessions, Sun 25 Oct 14:00-21:30 at Sugarfactory. App says 'Sunday Closing Marathon' at Gashouder 14:00-23:59.... |
| 60 | Thuishaven ADE Sunday Closing • Heated H | thuishaven.nl | official_event_page | high | html_signal | none | Thuishaven's listing row '25 OCT / Sunday w/ Polyamor presents Davyboi invites ... 25-10-2026 13.00-23.00' carries a SOLD OUT label. Time matches... |
| 61 | The Social Hub City • Free Recovery Chil | thesocialhub.co | generic_homepage | low | manual | none | No matching ADE listing for the Sunday recovery session. Free in the app; keep manual. |
| 62 | Breakfast Club: Second Wind • Free Break | shop.celebratix.io | ticket_shop | high | none | none | ADE: Breakfast Club: Second Wind, Sun 25 Oct 09:00-19:00 at Radion. Celebratix returns 403 to scripts. |
| 63 | Reinier Zonneveld 8hrs LIVE x Loveland • | loveland.nl/ade/reinier | ticket_shop | high | none | none | ADE: Reinier Zonneveld 8hrs LIVE x Loveland, Sun 25 Oct 13:00-22:30 at Mediahaven. The ticket page loads a widget with no readable status. |
| 64 | Lofi Sunday Closing Session • Raw Wareho | lofi.amsterdam | official_event_page | low | html_signal | none | Lofi's closest listing is 'ADE / CODA Sunday 25.10.2026'. The app's 'Sunday Closing Session' (17:00-23:00+) is not clearly the same. Needs... |
| 65 | Shelter Sunday Night Special • Undergrou | shelteramsterdam.nl | generic_homepage | low | none | none | ADE has 'Apollonia curates VBX' at Shelter, Sun 25 Oct 23:00-09:00 (app: 21:00-05:00). Times differ, so not matched. Shelter sells via fourvenues... |
| 66 | Radion Sunday Afters • The Absolute Bast | radion.amsterdam | generic_homepage | low | none | none | No matching Radion Sunday listing found. |
| 67 | Club John Doe Closing Hours • Intimate C | clubjohndoe.nl | generic_homepage | low | none | none | No matching Club John Doe listing found (ADE has 'Endless Records x UNDRGRND' there on Sun 20:00-04:00). |

## Method and limits

- ADE program pages came from ADE's public sitemap (1,527 2026 program URLs) and 210 fetched pages. The sitemap is incomplete (some live pages are missing), so a missing slug was never treated as proof.
- Providers were probed with one request per page, a descriptive User-Agent, a 1+ second delay, and a robots.txt check first. The 72 checkable URLs in `event-sources.json` all return 200; 9 were skipped because they block scripts or disallow automated access.
- Many app events look like composites of several real listings (the title, time and venue do not match any single ADE entry). Those are marked low or medium instead of guessing.
- Where the app's time or venue conflicts with ADE's, the app was not changed.
