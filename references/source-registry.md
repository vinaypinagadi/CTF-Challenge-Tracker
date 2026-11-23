# CTF source registry

Use this as a starting registry, not as a claim of complete coverage. Check the official site and respect robots.txt, terms, rate limits, authentication, and API policies.

## Broad event calendars

- CTFtime: `https://ctftime.org/` — community calendar and event pages; cross-check against the organizer page when available.
- Major League Hacking: `https://mlh.io/` — student/community hackathons and related events; verify event-specific security challenge details.
- Devpost: `https://devpost.com/` — hackathons; filter for security/CTF language and verify the organizer page.
- Hacker101 CTF: `https://ctf.hacker101.com/` — ongoing web-security learning challenges; distinguish from time-bounded contests.

## Company and security-community sources

- Company security blogs, engineering blogs, and official event pages.
- HackerOne hackathons and events: `https://www.hackerone.com/` — verify the specific event and participation terms.
- Bug bounty platform event pages (only when they are explicitly a contest/CTF; do not classify ordinary bounty programs as CTFs).
- University, conference, and local security-community calendars supplied by the user.

## Discovery method

1. Start with the registry and user-provided company domains.
2. Search exact phrases such as `CTF`, `capture the flag`, `security challenge`, `cyber challenge`, and `jeopardy CTF`, combined with a current year/month and the target region or `online`.
3. Follow the official organizer page from an aggregator and use the organizer page as the canonical source when it contains the same event.
4. Re-check event pages on each refresh; do not keep an event solely because it appeared in an earlier run.
5. Maintain a local `sources.json` or equivalent if the user adds sources. Each source should record URL, type, access method, last checked time, status, and notes.

## Source-quality labels

- `official`: organizer or company-controlled event page/API.
- `reputable_calendar`: established event calendar linking to the organizer.
- `search_discovery`: search result used only to locate a page; never sufficient by itself.
- `user_submitted`: source supplied by the user; still verify it.

Use official sources for date, rules, eligibility, and registration whenever possible. If sources disagree, prefer the latest official organizer update and preserve the conflict in `notes`.
