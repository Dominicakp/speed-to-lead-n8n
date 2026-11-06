# Speed-to-Lead Pipeline (n8n + Salesforce + Slack)

An automation that catches a website form submission, validates it, creates a Lead in Salesforce, and alerts sales on Slack with a link to the record. If Salesforce fails, the lead is saved to a Google Sheet so nothing is lost.

**Result:** 31 seconds from form submit to Slack alert.

Read the full story of how I built it, including what broke: [LINK TO YOUR MEDIUM ARTICLE]

## How it works

```
Tally form → n8n webhook → clean data → validate
    → valid: create Salesforce Lead → Slack alert with link
    → Salesforce fails: save to Google Sheet (fallback)
    → invalid: save to Rejected tab
```

1. **Webhook** receives the form submission.
2. **Edit Fields** trims spaces and lowercases the email.
3. **IF** checks the email format, last name, and company.
4. **Salesforce** creates a Lead with Lead Source set to "Web".
5. **Slack** posts the lead details with a link to the record.
6. **Google Sheets** logs failed and rejected leads.

## Tools used

- [n8n](https://n8n.io) (workflow engine)
- Salesforce Developer Edition (free CRM)
- Slack (alerts)
- Google Sheets (fallback log)
- [Tally](https://tally.so) (form)
- ngrok (public tunnel for local n8n)

## Files in this repo

| File | Purpose |
|---|---|
| `speed-to-lead-workflow.json` | The n8n workflow. Import it into your own n8n. |
| `sample-payload-tally.json` | Example of what Tally sends |
| `sample-payload-curl.sh` | A test command to simulate a form submission |
| `screenshots/` | Images of the workflow and results |

## How to set it up

1. Open n8n and create a blank workflow.
2. Click **⋯ → Import from file** and choose `speed-to-lead-workflow.json`.
3. Create your own credentials for **Salesforce**, **Slack**, and **Google Sheets**. No credentials are included in this repo.
4. Create a Google Sheet with the columns: `timestamp`, `first_name`, `last_name`, `email`, `company`, `phone`, `message`, `error`. Add a second tab called `Rejected`.
5. In the **Slack** node, choose your channel. In the message text, replace `YOUR-ORG-DOMAIN` with your Salesforce domain.
6. In the **Edit Fields** node, make sure the question labels match your form exactly.
7. Test with `sample-payload-curl.sh` using your Test URL, then publish the workflow and connect your form to the Production URL.

## Known limitations

- Runs on a local machine in my setup, so it stops if the computer sleeps
- No duplicate check
- Matches form answers by label, so rewording a question breaks the mapping
- No owner assignment
- The fallback doesn't send an alert

## Planned improvements

- [ ] Check Salesforce for an existing email before creating a lead
- [ ] Match answers by question key instead of label
- [ ] Alert Slack when the fallback fires
- [ ] Round-robin lead assignment
- [ ] Move to an always-on host

## License

MIT
