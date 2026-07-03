# Send Automated Recruitment Rejection Emails with Google Sheets and Gmail at End-of-Day

Automatically reads a "Candidate Status" tab in Google Sheets every day at **18:00 Asia/Kolkata**, filters rows with exact (case-sensitive) rejection statuses and sends one personalized rejection email per candidate via SMTP (Gmail). It rate-limits sends, supports DRY_RUN previews and writes a timestamp back to `rejection_sent_at` to avoid duplicates.

## Who’s It For

- Recruiters needing consistent, respectful closure at day end.
- Teams tracking hiring outcomes in Google Sheets.
- Coordinators who prefer a scheduled, hands-off workflow with safeguards.

## How It Works

1. **Cron (18:00 IST)** triggers daily.
2. **Google Sheets Read** → loads **Candidate Status** tab.
3. **Filter** → keep rows where `status` matches `REJECT_STATUS_CSV` (exact match), with a valid `candidate_email` and empty `rejection_sent_at`.
4. **DRY_RUN?** If true → output preview only; if false → proceed.
5. **Rate limit** → wait `RATE_LIMIT_SECONDS` (default 10s) between emails.
6. **SMTP (Gmail)** → send personalized email per row using templates.
7. **Mark as sent** → write current timestamp to `rejection_sent_at`.

## How to Set Up

1. **Sheet & Columns**: Create a **Candidate Status** tab with:
   `candidate_name`, `candidate_email`, `role`, `status`, `recruiter_name`, `recruiter_email`, `company_name`, `interview_feedback` (optional), `template_variant` (optional), `language` (optional), `rejection_sent_at`
2. **Credentials**: Connect **Google Sheets (OAuth)** and **SMTP (Gmail)** in n8n (use an App Password if 2FA is enabled).
3. **Config (Set node)**:
   - `SPREADSHEET_ID`
   - `SOURCE_SHEET` = `Candidate Status`
   - `TIMEZONE` = `Asia/Kolkata`
   - `REJECT_STATUS_CSV` = e.g., `Rejected`
   - `SMTP_FROM` = e.g., `careers@company.com`
   - `SUBJECT_TEMPLATE` = `Regarding your application for {{role}} at {{company_name}}`
   - `HTML_TEMPLATE` / `TEXT_TEMPLATE`
   - `RATE_LIMIT_SECONDS` = `10`
   - `INCLUDE_WEEKENDS` = `true`
   - `DRY_RUN` = `false`
4. **Activate**: Enable the workflow.

## Requirements

- **[**n8n Account (Self-hosted or Cloud)**](https://n8n.partnerlinks.io/om1efg2qgvwi)**
- Google Sheet with the **Candidate Status** tab and columns above.
- SMTP (Gmail) account for sending.

## How to Customize

- **Statuses**: `REJECT_STATUS_CSV` supports comma-separated exact values (e.g., `Rejected,Not Selected`).
- **Templates**: Edit `SUBJECT_TEMPLATE`, `HTML_TEMPLATE`, and `TEXT_TEMPLATE`.
- **Variables**: `{{candidate_name}}`, `{{role}}`, `{{company_name}}`, `{{recruiter_name}}`, and optional `{{feedback_text}}` / `{{feedback_html}}` from `interview_feedback`.
- **Schedule**: Change the Cron time from 18:00 to your preferred hour.
- **Rate limit**: Tune `RATE_LIMIT_SECONDS` to match your SMTP provider's policy.
- **Preview**: Set `DRY_RUN=true` for a safe, no-send preview.

## Add-Ons

- **Dynamic Reply-To** per `recruiter_email`.
- **Localization/Variants** via `language` or `template_variant` columns.
- **Daily summary** email with sent/skip/error counts.
- **Validation & logging**: Log invalid emails to another sheet.
- **Gmail API**: Replace SMTP with Gmail nodes if preferred.

## Use Case Examples

- **Daily round-up**: 18:00 IST closure emails for all candidates marked `Rejected` today.
- **Multi-brand hiring**: Switch `company_name` per row and personalize subject lines.
- **Compliance/logging**: Run `DRY_RUN` each afternoon for review, then switch to live sends.

## Common Troubleshooting

| Issue | Possible Cause | Solution |
|--------|----------------|----------|
| No emails sent | `status` doesn't exactly match `REJECT_STATUS_CSV` or `candidate_email` is missing | Ensure the status is an exact, case-sensitive match and email is present |
| Duplicates | `rejection_sent_at` already contains a value | Verify `rejection_sent_at` is blank before the workflow runs |
| Blank variables | Required sheet fields are empty | Fill `candidate_name`, `role`, `company_name`, and `recruiter_name` |
| SMTP errors | Invalid credentials, sender permissions, or sending limits | Verify SMTP credentials and account limits |
| Timing issues | Incorrect timezone or Cron configuration | Confirm timezone is `Asia/Kolkata` and Cron is set to 18:00 |

## Need Help?

If you need help customizing this workflow, automating your recruitment processes, or integrating it with your HR systems, our **WeblineIndia** team is ready to assist. Explore our **[Process Automation Solutions](https://www.weblineindia.com/process-automation-solutions.html)** or connect with our **[n8n workflow development experts](https://www.weblineindia.com/n8n-automation/)** to build, customize, and scale your business automation with confidence.
