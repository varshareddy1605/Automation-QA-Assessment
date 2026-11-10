# Bonus - Uptime Monitor

Monitors the app tested in Task 1 (https://realworld.svelte.dev/) and posts to the same Discord channel as Task 2.

**Flow:** Every 5 Minutes (or Manual Run) -> **Check Sites** (Code) -> IF alert needed -> **Send Alert** (Discord).
**Daily summary:** Daily 9am -> **Build Summary** (Code) -> **Send Summary** (Discord).

## How it works
- **Checks:** the home page and one article page, in parallel, with a 10 s timeout. A Code node makes the requests so it can time each one precisely.
- **Rule:** anything other than HTTP 200 (including a timeout or network error) counts as down.
- **Retry:** a non-200 result is retried once after 3 s before it counts, to avoid false alarms.
- **Response time:** recorded for every check and included in alerts.
- **Alert noise control:** alert on the first failure, repeat a reminder every 12 checks (about 1 hour) while still down, and send a "recovered" message when it returns to 200.
- **Daily summary:** uptime %, average and slowest response time per target, then counters reset.

## Notes
- Counters and failure state use n8n workflow static data, which only persists when the workflow is **published/active**. Manual test runs always start fresh, so a failing target alerts every time.
- The article page is a known failure from Task 1 (HTTP 500 after about 12 s), so it should trigger a real alert.
- Credential: same Discord Webhook credential as Task 2; none stored in the JSON.
