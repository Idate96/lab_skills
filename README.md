# lab_skills

Shared lab automation skills and runbooks extracted from local Codex workflows.

## Included

- `skills/student-onboarding`
  - spreadsheet insertion from the top
  - shared Drive folder creation pattern
  - Euler cluster form automation
  - signed-in student-PC Google Form procedure
  - Fang Nan GitHub-group contact workflow through Chat API and email
- `skills/meeting-updates`
  - recurring Google Doc standup / meeting-note updates
- `docs/gws-auth-repro.md`
  - Google Workspace CLI setup, Chat API setup, and broad reauth workflow
- `docs/student-onboarding-runbook.md`
  - local onboarding runbook mirroring the skill

## External dependency for signed-in Google Forms

The student-PC form flow depends on the local Chrome CDP tool:

- repo: `git@github.com:Idate96/chrome-cdp-skill.git`
- key script: `skills/chrome-cdp/scripts/cdp`

That flow assumes:

- Chrome remote debugging is enabled at `chrome://inspect/#remote-debugging`
- the supervisor is already signed in to the required Google account in Chrome

## Notes

- This repo intentionally excludes local credential files and secret material.
- The shared auth runbook keeps workflow details but redacts the local OAuth client id.
