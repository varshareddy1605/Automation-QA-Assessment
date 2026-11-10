# Task 2 - n8n "Morning Brief" Workflow

Posts a digest of the most-starred GitHub repos for a topic (`automation`) to Discord, with a README spotlight for the top repo and a defined error path.

## APIs used and why
- **GitHub Search API** (`/search/repositories?q=topic:automation&sort=stars&order=desc`): free, needs no key for light use, and returns star counts, a clean numeric field for sorting and for the IF threshold.
- **GitHub README endpoint** (`/repos/{owner}/{repo}/readme`): second call, enriches the #1 repo with a short excerpt. Same host, different endpoint.
- **Discord webhook**: free, no OAuth setup.

## Flow
Schedule (every 1 hour, for testing) or Manual Run -> **GitHub Search** -> **Top 5** (Code) -> **Fetch README** -> **Build Digest** (Code) -> **IF top repo > 50,000 stars** -> Hot Digest / Standard Digest -> **Send Digest** (Discord).

## What the transformation does
- **Top 5 (Code node):** drops archived repos, sorts by stars descending, keeps the top 5, and reshapes each to `{full_name, stars, url, language, description}`. Descriptions are shortened to about 100 characters, cutting at a word boundary and adding "..." so they never end mid-word.
- **Build Digest (Code node):** strips markdown and HTML from the README, keeps about 280 characters, and builds the numbered list. Output stays well under Discord's 2,000-character limit.

## Conditional branch
- **True (top repo > 50,000 stars):** "Hot" digest = top 5 + spotlight with README excerpt.
- **False:** shorter "Standard" digest = top 5 only.
The threshold is a judgement call: 50k separates established giants from rising projects. Edit the IF node to exercise the other branch.

## Error handling
- **GitHub Search** and **Top 5** use *On Error -> Continue (using error output)*. A failure (rate limit, timeout, bad URL, empty result) goes to **Format Error**, then **Send Error Alert** posts the reason and a timestamp to Discord. Search also retries once after 2 s.
- **Fetch README** uses *Continue (regular output)*: if it fails, the digest still goes out with "(README unavailable)".
- Nothing fails silently: every failure either alerts Discord or degrades gracefully, and all runs stay in the n8n execution log.

## Credentials
No secrets in the exported JSON. Both Discord nodes reference a **Discord Webhook** credential stored in n8n's Credentials store. After import, create it with your webhook URL and select it in **Send Digest** and **Send Error Alert**.

## How to run and test
1. Import `Task2_Workflow_Varsha.json` and set the Discord credential on both Discord nodes.
2. Click **Execute workflow** from Manual Run; a digest should appear in Discord.
3. *Other branch:* raise the IF threshold (e.g. 500000) and run again; the Standard digest is sent.
4. *Error path:* change the Search URL host to an invalid one and run; a "Morning Brief failed" alert is sent. Restore the URL afterwards.

## Screenshots
`screenshots/canvas.png` (workflow canvas) and `screenshots/execution.png` (successful execution with output data).
