# Automation & QA Developer - Take-Home Assessment

**Candidate:** Varsha
**Walkthrough video :** https://drive.google.com/file/d/1ksx61SmH06r6bQhe70PtuuOJNof-lPFQ/view?usp=sharing

## Repository contents
```
README.md                                   this overview
task1/Task1_QA_Report_Varsha.pdf            QA report (bug table, root-cause analysis, evidence)
task2/Task2_Workflow_Varsha.json            n8n workflow (GitHub digest to Discord)
task2/Task2_README.md                       APIs, transformation, error path
task2/screenshots/                          canvas.png, execution.png
bonus/Bonus_UptimeMonitor_Varsha.json       n8n uptime monitor
bonus/Bonus_README.md                       how the monitor works
bonus/screenshots/                          workflow.png
```

## Task 1 - Web App QA & Debug Report
**App tested:** RealWorld Svelte demo (https://realworld.svelte.dev/), manual testing of sign-up, login, create/view/edit/delete article and logout. Seven issues are documented with steps, expected vs actual, severity and suspected cause.

Key findings:
- **Critical:** the article page returns HTTP 500 (a server-side failure, since only the page document is requested). This blocks reading, editing, deleting and the post-publish redirect.
- **Critical:** registering with an existing username/email succeeds and locks the original owner out. This is the subject of the root-cause analysis.
- **High:** publishing shows a 500 even though the article is saved.
- **Medium/Low:** slow responses (10-12 s), a generic login error, a one-character password accepted, and no email verification.

Causes are stated as hypotheses where I had no server access.

## Task 2 - n8n API Integration Workflow
Schedule/Manual trigger -> GitHub Search API -> Code node (top 5) -> README endpoint (enrichment) -> IF top repo > 50,000 stars -> Discord digest. Failures in the search or transform step route to a Discord alert, and a README failure degrades gracefully. No secrets are stored in the JSON; the Discord webhook lives in n8n's Credentials store. Details are in `task2/Task2_README.md`.

## Bonus - Uptime Monitor
Pings the Task 1 app every 5 minutes (home page and an article page), treats anything other than HTTP 200 as down, retries once, records response time, alerts the same Discord channel, and posts a daily summary. It correctly alerted on the article-page failure found in Task 1. Details are in `bonus/Bonus_README.md`.

## Running the workflows
1. In n8n, import each JSON via **Workflows -> Import from file**.
2. Create a **Discord Webhook** credential with your own webhook URL and select it in the Discord nodes.
3. Click **Execute workflow** from the Manual Run node.

AI assistance (Claude) was used for drafting and review, as the brief allows. I can explain every decision in the follow-up call.
