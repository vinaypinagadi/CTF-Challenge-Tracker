---
name: ctf-challenge-tracker
description: Evidence-backed discovery and dashboard tracking for online CTF competitions, company security challenges, and hackathons. Use when the user wants to find, filter, verify, monitor, or participate in CTF events from anywhere, especially with regional/time-zone eligibility and recurring refreshes.
---

# CTF Challenge Tracker

Use this skill to maintain a current, evidence-backed feed of online CTF competitions and company-run security challenges. Produce a dashboard-ready JSON feed and, when the host supports local files or HTML artifacts, render the bundled static dashboard generator.

This skill is portable across Manus, Claude, ChatGPT, Gemini, Cursor, Windsurf, Cline, and other tool-using AI agents. The host agent must supply browsing/search, HTTP, or connector tools; the skill does not claim that one tool can crawl the entire internet.

## Core promise and limits

- Discover from a maintained source registry plus user-provided company pages, event calendars, RSS/Atom feeds, APIs, and search results.
- Include only events supported by an official or reputable source URL and a recorded verification timestamp.
- Distinguish `online`, `hybrid`, and `in_person`; for online events, show whether the event can be joined from the user's configured region/time zone.
- Never invent an event, organizer, date, prize, eligibility rule, or registration URL. Use `not stated` when an official source omits a fact.
- Do not submit registrations, solve challenges, attack systems, or bypass access controls. Participation remains a user action through the official event link.
- Do not promise exhaustive internet coverage. Report the sources checked, search window, and any blind spots.

## Operating workflow

Follow these steps on every run or refresh:

1. **Load preferences.** Read the user's location or IANA time zone, regions of interest, online/in-person mode, skill tags, minimum/maximum event dates, notification destination, and cadence. If location is missing, ask once or default to `UTC` while clearly labeling the assumption.
2. **Collect candidates.** Read [references/source-registry.md](references/source-registry.md), query the applicable sources, and add user-provided company domains. Prefer official event pages, organizer APIs, RSS/Atom feeds, and well-known event calendars over snippets.
3. **Normalize.** Map every candidate to the schema in [references/event-schema.json](references/event-schema.json). Preserve the original URL, source name, organizer, dates, time zone, mode, eligibility, registration URL, and last-seen timestamp.
4. **Verify.** Open the source page or API response. Confirm the page is reachable, the event is current, and the registration or participation destination is reachable. Record `verified_at`, `verification_evidence`, and a `verification_status` of `verified`, `partially_verified`, or `rejected`.
5. **Deduplicate.** Merge exact or near-duplicate events by canonical URL, organizer + title + start date, or an explicit event ID. Keep all source URLs in `alternate_sources`.
6. **Classify.** Assign `mode`, `scope` (`company`, `community`, `academic`, or `unknown`), tags, difficulty when stated, and `remote_eligibility`. Convert dates to ISO 8601 with the official event time zone; do not silently convert a date-only event into a precise time.
7. **Apply regional filters.** For `online` events, mark `joinable_from_user_region` when the rules do not exclude the configured region. For `hybrid` or `in_person`, check venue/eligibility explicitly. Use `unknown` when the rules are insufficient; do not infer permission from the event title.
8. **Rank and output.** Sort by registration status and soonest verified start, then by source quality and user tag match. Exclude rejected events from the dashboard; retain them in a refresh log with the rejection reason.
9. **Render.** Write the normalized list to `ctf_events.json`. If Python is available, run `scripts/build_dashboard.py --input ctf_events.json --output ctf_dashboard.html --timezone <user-timezone>`. The dashboard must show source evidence and direct official registration/participation links.
10. **Report coverage.** Summarize sources checked, number found/verified/rejected/duplicate, next refresh time, and any missing credentials or inaccessible sources.

## Dashboard requirements

The generated dashboard should provide:

- Search by title, organizer, company, tag, and difficulty.
- Filters for region eligibility, online/hybrid/in-person mode, scope, registration status, and date window.
- Cards with title, organizer, mode, start/end, official source, registration link, region result, tags, prize/format when stated, verification timestamp, and evidence link.
- A clear `Participate` or `Register` link that opens the official destination; never auto-click or submit it.
- A compact “coverage and freshness” panel showing last refresh, sources checked, and verification counts.

Use the provided generator for a deterministic artifact. If the host has a richer web-app builder, the same JSON schema can back an interactive app; preserve the fields and safety rules above.

## 3-hour refresh

A recurring refresh is a separate automation setup. Use the host's scheduler or GitHub Actions with a 3-hour cadence, persist `ctf_events.json` and a refresh log, and make the job idempotent. Recommended generic interval: `0 */3 * * *` in UTC, or an equivalent every-3-hours interval supported by the host. Do not create the schedule merely to perform a one-time search. When a source requires login, rate limits, a CAPTCHA, or an API key, record the source as unavailable and continue with the remaining sources.

For Manus scheduling, use the automation/scheduling owner rather than embedding a polling loop in the skill. The scheduled instruction should say: “Refresh the CTF feed using the CTF Challenge Tracker skill, preserve the configured region/time zone, verify all included links, atomically update the dashboard, and report coverage and failures.”

## Standard response shape

Return a concise summary followed by the artifact paths or links. Include:

- `last_refresh_at`, `next_refresh_at` (if scheduled), `user_timezone`, and `coverage`
- counts for `verified`, `partially_verified`, `rejected`, and `duplicates`
- a table or dashboard link for the top events
- explicit notes for assumptions, inaccessible sources, and events with unknown eligibility

Read the source registry when discovering events, and read the event schema when normalizing or validating data. Keep those references authoritative; do not duplicate their full contents in the response.
