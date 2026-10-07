# ADE 2026 Interactive Schedule & Map

A static single-page guide to 67 curated events at Amsterdam Dance Event 2026: event cards, filters, a personal schedule and an interactive map. English and Greek.

Stack: one `index.html`, vanilla JavaScript, Tailwind CSS (CDN), Leaflet 1.9.4 + MarkerCluster, OpenStreetMap. No build step.

## Run locally

```sh
python3 -m http.server 8000
```

Then open http://localhost:8000/index.html.

## Notes

- `Going` means "I want to attend" (My Schedule). It does not imply a ticket.
- The internal availability value `mychoice` means "Tickets Already Bought", not `Going`.
- The Curator button is public; editing is protected by a PIN. Never put tokens or credentials in client-side code.
- Planned: move ticket availability into a separate `availability.json`, refreshed by a scheduled GitHub Action.
