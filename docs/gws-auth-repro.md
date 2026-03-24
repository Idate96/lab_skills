# Google Workspace CLI Auth Repro Log

Last updated: 2026-03-24T23:37:17+01:00

This is a living runbook for reproducing the Google Workspace CLI (`gws`) setup on this machine.

## Goal

Set up broad Google Workspace access for `gws` under:

- Google account: `lterenzi@leggedrobotics.com`
- Machine: `/home/lorenzo`
- OS: Ubuntu 22.04.5 LTS

This log records:

- what was installed
- what commands were run
- what state was reached
- what manual Google Console steps remain
- follow-up configuration needed for Google Chat

## Current status

Completed:

- `gws` installed and runnable
- `gcloud` installed and runnable
- `gcloud` authenticated as `lterenzi@leggedrobotics.com`
- dedicated GCP project created: `gws-lterenzi-20260324`
- 22 Google Workspace APIs enabled for that project
- Google Auth Platform branding configured
- OAuth Desktop client created
- `gws auth login --full` completed successfully
- encrypted OAuth credentials stored locally

Not completed yet:

- nothing blocking basic `gws` usage

## Versions

- `gws 0.20.0`
- `Google Cloud SDK 562.0.0`

## Commands executed so far

### 1. Install `gws`

```bash
npm install -g @googleworkspace/cli
```

Verification:

```bash
which gws
gws --version
gws auth status
```

Observed state after install:

- `gws` available at `/home/lorenzo/.nvm/versions/node/v20.19.4/bin/gws`
- no existing `~/.config/gws/client_secret.json`
- no existing stored `gws` credentials

### 2. Install `gcloud`

```bash
sudo apt-get update
sudo apt-get install -y ca-certificates curl gnupg
curl -fsSL https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo gpg --dearmor -o /usr/share/keyrings/cloud.google.gpg
echo 'deb [signed-by=/usr/share/keyrings/cloud.google.gpg] https://packages.cloud.google.com/apt cloud-sdk main' | sudo tee /etc/apt/sources.list.d/google-cloud-sdk.list
sudo apt-get update
sudo apt-get install -y google-cloud-cli
```

Verification:

```bash
gcloud --version
```

### 3. Authenticate `gcloud`

Started:

```bash
gws auth setup --login
```

Inside the TUI:

- selected Google account `lterenzi@leggedrobotics.com`
- selected `Create new project`
- entered project id `gws-lterenzi-20260324`
- selected all 22 APIs in the API selection step

This caused `gws` to use `gcloud` login behind the scenes.

Verification:

```bash
gcloud auth list
gcloud config get-value account
```

Observed state:

- active account: `lterenzi@leggedrobotics.com`

### 4. Project creation

Verification:

```bash
gcloud projects describe gws-lterenzi-20260324 --format='yaml(projectId,projectNumber,lifecycleState,createTime,name)'
gcloud config get-value project
```

Observed state:

- project id: `gws-lterenzi-20260324`
- project number: `241142192893`
- lifecycle: `ACTIVE`
- create time: `2026-03-24T21:03:28.710Z`
- active project: `gws-lterenzi-20260324`

### 5. API enablement

This step was performed inside the `gws auth setup --login` TUI by selecting all APIs.

Observed result:

- `22 enabled, 0 skipped`

## Manual step currently blocking completion

`gws auth setup --login` reached the final step and stopped because it needs a manual OAuth Desktop client.

The interactive terminal session is waiting for:

- OAuth Client ID
- OAuth Client Secret

### Browser pages already opened

Consent screen:

- `https://console.cloud.google.com/apis/credentials/consent?project=gws-lterenzi-20260324`

Credentials page:

- `https://console.cloud.google.com/apis/credentials?project=gws-lterenzi-20260324`

### Required console actions

1. Open the consent screen for project `gws-lterenzi-20260324`.
2. If not configured yet, set user type to `External`.
3. Save through the consent-screen flow.
4. If prompted, add `lterenzi@leggedrobotics.com` as a test user.
5. Open the Credentials page.
6. Click `Create Credentials`.
7. Select `OAuth client ID`.
8. Choose application type `Desktop app`.
9. Copy the generated `Client ID`.
10. Copy the generated `Client Secret`.
11. Paste both back into the waiting `gws auth setup --login` session.

### Branding values used

During Google Auth Platform setup, the app branding value chosen was:

- app name: `gws-lterenzi`

Recommended matching values for reproducibility:

- user support email: `lterenzi@leggedrobotics.com`
- developer contact email: `lterenzi@leggedrobotics.com`

### OAuth client created

In `Clients`, a desktop OAuth client was created with:

- client display name: `gws-lterenzi-desktop`
- client type: `Desktop app`
- client id: `<redacted-desktop-client-id>`
- client JSON downloaded to:
  - `/home/lorenzo/Downloads/client_secret_<redacted>.json`
- client JSON installed to:
  - `/home/lorenzo/.config/gws/client_secret.json`

### Final `gws` login command used

```bash
gws auth login --full
```

Observed result:

- account: `lterenzi@leggedrobotics.com`
- status: `success`
- encrypted credentials saved to `/home/lorenzo/.config/gws/credentials.enc`
- encryption backend: OS keyring + local `.encryption_key`

Scopes granted:

- `https://www.googleapis.com/auth/drive`
- `https://www.googleapis.com/auth/spreadsheets`
- `https://www.googleapis.com/auth/gmail.modify`
- `https://www.googleapis.com/auth/calendar`
- `https://www.googleapis.com/auth/documents`
- `https://www.googleapis.com/auth/presentations`
- `https://www.googleapis.com/auth/tasks`
- `https://www.googleapis.com/auth/pubsub`
- `https://www.googleapis.com/auth/cloud-platform`
- `openid`
- `https://www.googleapis.com/auth/userinfo.email`
- `https://www.googleapis.com/auth/userinfo.profile`

### UI note: old labels vs new labels

Google currently mixes older and newer wording in different docs and pages.

You might see:

- `Google Auth Platform` instead of `OAuth consent screen`
- `Clients` instead of `Credentials`
- `Audience` as a separate section instead of a single `External/Internal` chooser page

So if you do not see a literal `External` button immediately, the equivalent flow is usually:

1. Open `Google Auth Platform`.
2. Complete `Branding` if requested.
3. Open `Audience`.
4. Set the app to `External` there, or confirm that it is already `External`.
5. Add `lterenzi@leggedrobotics.com` under `Test users` if that section appears.
6. Open `Clients`.
7. Click `Create Client`.
8. Choose `Desktop app`.

## Exact machine state right now

### `gcloud auth list`

- active account: `lterenzi@leggedrobotics.com`

### `gws auth status`

Final working state:

- `auth_method: oauth2`
- `user: lterenzi@leggedrobotics.com`
- `project_id: gws-lterenzi-20260324`
- `client_config_exists: true`
- `encrypted_credentials_exists: true`
- `encryption_valid: true`
- `has_refresh_token: true`
- `token_valid: true`
- `storage: encrypted`

## Google Chat app configuration

### Why this became necessary

Basic Docs, Drive, Sheets, and Forms access worked after `gws auth login --full`.

Later, when trying to send a direct Google Chat message with `gws`, the toolchain hit two issues:

- `gws chat +send` returned `Google Chat app not found`
- raw Chat API calls returned `insufficient authentication scopes`

The first error means that enabling the API was not enough for the `gws` Chat helper. The project also needs a configured Chat app in the Google Cloud console.

Google's current docs refer to this as:

- `Enable and configure the Google Chat API`
- configure a Chat app with a `name`, `avatar URL`, and `description`

Official references used for this section:

- Google Chat auth guide:
  - https://developers.google.com/workspace/chat/authenticate-authorize-chat-user
- Chat API / Chat app configuration overview:
  - https://developers.google.com/workspace/chat

### Direct console page for this project

For project `gws-lterenzi-20260324`, open:

- `https://console.cloud.google.com/apis/api/chat.googleapis.com/hangouts-chat?project=gws-lterenzi-20260324`

If that lands on the API overview instead of the configuration form, look for one of:

- `Configuration`
- `Google Chat API`
- `Chat API configuration`

in the left nav or on-page tabs.

### Minimum configuration to save

On the Chat API configuration page, fill the minimum required fields:

1. Turn on the Google Chat API for the project if it is not already enabled.
2. Open the Chat app configuration form.
3. Set:
   - `App name`: `gws-lterenzi-chat`
   - `Avatar URL`: any stable public `https://...` square image URL
   - `Description`: `Workspace CLI Chat helper for Lorenzo`
4. Leave interactive features off unless a later workflow explicitly needs them.
5. Save the configuration.

Notes:

- For the current goal, this does not need slash commands, event subscriptions, or an HTTP endpoint.
- The configuration is mainly to make the project a recognized Chat app so Chat API operations have app metadata to attach to them.
- If a later workflow needs the Chat app itself to appear in Chat search or receive user messages, return here and enable `Interactive features`, then set `Visibility` to include `lterenzi@leggedrobotics.com`.
- If the intended exposure is only direct messages with the Chat app, Google documents that this can be configured without enabling group-space installation.

### If the goal is to read messages and reply as a Chat app

If the goal is not just API access, but an actual Chat app that users can message and that can respond, the minimal config above is not enough.

In that case, on the same `Configuration` page:

1. Turn on `Interactive features`.
2. Under `Functionality`, leave direct messaging enabled.
3. Only enable `Join spaces and group conversations` if the app should be addable to rooms as well.
4. Under `Connection settings`, choose exactly one backend:
   - `HTTP endpoint URL` if you have a real HTTPS service
   - `Apps Script` if the app logic is implemented in Apps Script
   - `Cloud Pub/Sub topic name` only if you are intentionally building around Pub/Sub delivery
5. Under `Triggers`, select the events the app must handle:
   - `Message` if users should DM the app or mention it and get a reply
   - `Added to space` if the app should react when installed or added
6. Under `Visibility`, add `lterenzi@leggedrobotics.com` so the app can be found and tested in Chat.
7. Save.

Important constraint:

- a Chat app cannot read arbitrary human-to-human direct messages
- it can only receive interaction events that are sent to the Chat app itself
- examples:
  - a direct message with the Chat app
  - a space where the Chat app was added and then mentioned
  - an `added to space` event

So if the intent is "Codex should read my existing DM thread with Manthan and reply there", that is not the Chat app model. For that, user-authenticated Chat API calls are needed instead of Chat app interaction triggers.

### User-authenticated Chat scopes

After configuring the Chat app, re-run `gws` auth with Chat scopes.

The working Chat scope set used during testing was:

```bash
gws auth login --scopes https://www.googleapis.com/auth/chat.messages,https://www.googleapis.com/auth/chat.messages.create,https://www.googleapis.com/auth/chat.spaces.readonly
```

Important caveat:

- this reauth replaced the earlier broad Docs/Drive token
- after a Chat-only login, Chat calls may work but Docs/Drive calls can lose access

So if you need both Chat and Docs/Drive in one `gws` session, reauth again with the union of scopes you actually need.

### Stale token-cache quirk

One concrete quirk showed up while testing Chat:

- `gws auth status` showed the new Chat scopes correctly
- but `gws chat ...` still returned `insufficient authentication scopes`

The cause was a stale local token cache at:

- `~/.config/gws/token_cache.json`

The successful recovery path was:

```bash
cp ~/.config/gws/token_cache.json ~/.config/gws/token_cache.json.$(date +%Y%m%dT%H%M%S).bak
rm ~/.config/gws/token_cache.json
gws auth status
```

After clearing the cache, the Chat error changed from a fake scope failure to the real missing-configuration error:

- `Google Chat app not found`

That is a useful diagnostic:

- `insufficient authentication scopes` after a successful reauth can still mean `stale token cache`
- `Google Chat app not found` means the OAuth part is good enough to reach Chat, but the project still lacks Chat app configuration

### Practical DM lookup note

For direct-message API calls, Google Chat identifies users as:

- `users/{chat_user_id}`
- or, for external/Google-account users, an email alias such as `users/EMAIL@DOMAIN`

So the reliable lookup path is not just a display name like `Mantah`; it is a concrete Chat user identity.

### Current reproducibility status

As of this run:

- project exists and is configured for broad Workspace use
- Chat-specific OAuth scopes were granted successfully
- Chat app metadata has been configured successfully in Cloud Console
- Chat DM read/send works through `gws`
- `scope_count: 14`

### Waiting interactive session

The earlier `gws auth setup --login` session was no longer active by the time the OAuth client JSON had been downloaded.

The successful fallback path was:

1. copy downloaded client JSON to `~/.config/gws/client_secret.json`
2. run `gws auth login --full`
3. complete consent in browser
4. let `gws` store encrypted credentials

## Notes

- An earlier attempt mentioned `maximilian.kra0@gmail.com`, but that was confirmed to be a mistake and should be ignored.
- The intended and authenticated Google account is `lterenzi@leggedrobotics.com`.
- The likely next step after entering the OAuth client values is a `gws auth login` flow that stores credentials under `~/.config/gws/`.

## Next update checklist

Useful follow-up additions:

- verify private Google Docs access with `gws docs documents get`
- document any services that still fail due to missing API enablement or missing scopes
- optionally export credentials for headless reuse if needed

## Confirmed working Google Chat flow

After saving the Chat app metadata on the Google Chat API `Configuration` tab, Chat started working through `gws`.

### Final prerequisites that mattered

1. `chat.googleapis.com` enabled in project `gws-lterenzi-20260324`
2. Chat app metadata saved in Cloud Console
3. `gws` reauthed with Chat scopes
4. stale local token cache removed once

### Chat app metadata actually required by `gws`

On:

- `https://console.cloud.google.com/apis/api/chat.googleapis.com/hangouts-chat?project=gws-lterenzi-20260324`

the following minimal configuration was enough:

- `Interactive features`: off
- `App name`: `gws-lterenzi-chat`
- `Avatar URL`: `https://upload.wikimedia.org/wikipedia/commons/5/58/GChat.png`
- `Description`: `Workspace CLI Chat helper for Lorenzo`

No connection settings or triggers were needed for the user-authenticated API path.

### Chat scopes used

```bash
gws auth login --scopes https://www.googleapis.com/auth/chat.messages,https://www.googleapis.com/auth/chat.messages.create,https://www.googleapis.com/auth/chat.spaces.readonly
```

### Broad reauth command that keeps Chat + Docs/Drive/Forms together

When later workflows needed Chat and the broader Workspace APIs at the same time, the working union command was:

```bash
gws auth login --scopes https://www.googleapis.com/auth/drive,https://www.googleapis.com/auth/spreadsheets,https://www.googleapis.com/auth/gmail.modify,https://www.googleapis.com/auth/calendar,https://www.googleapis.com/auth/documents,https://www.googleapis.com/auth/presentations,https://www.googleapis.com/auth/tasks,https://www.googleapis.com/auth/pubsub,https://www.googleapis.com/auth/cloud-platform,https://www.googleapis.com/auth/chat.messages,https://www.googleapis.com/auth/chat.messages.create,https://www.googleapis.com/auth/chat.spaces.readonly,https://www.googleapis.com/auth/chat.memberships.readonly,https://www.googleapis.com/auth/forms.body.readonly,https://www.googleapis.com/auth/forms.responses.readonly,https://www.googleapis.com/auth/directory.readonly
```

Observed persisted scopes after this reauth:

- `https://www.googleapis.com/auth/drive`
- `https://www.googleapis.com/auth/spreadsheets`
- `https://www.googleapis.com/auth/gmail.modify`
- `https://www.googleapis.com/auth/calendar`
- `https://www.googleapis.com/auth/documents`
- `https://www.googleapis.com/auth/presentations`
- `https://www.googleapis.com/auth/tasks`
- `https://www.googleapis.com/auth/pubsub`
- `https://www.googleapis.com/auth/cloud-platform`
- `https://www.googleapis.com/auth/chat.messages`
- `https://www.googleapis.com/auth/chat.messages.create`
- `https://www.googleapis.com/auth/chat.spaces.readonly`
- `https://www.googleapis.com/auth/chat.memberships.readonly`
- `https://www.googleapis.com/auth/forms.body.readonly`
- `https://www.googleapis.com/auth/forms.responses.readonly`
- `https://www.googleapis.com/auth/directory.readonly`
- `openid`
- `https://www.googleapis.com/auth/userinfo.email`
- `https://www.googleapis.com/auth/userinfo.profile`

This is the current recommended reauth command for this machine because it avoids bouncing back and forth between a Chat-only token and a Docs/Drive token.

The extra Chat + Directory scopes are needed for workflows such as:

- resolving an existing DM by user
- identifying a Workspace user through the directory before messaging them

### Browser launch note for OAuth consent

For this machine, `xdg-open` was not a reliable way to surface the OAuth consent page visibly.

The reliable path was:

1. start `gws auth login ...`
2. take the printed OAuth URL
3. launch it directly with Chrome

```bash
google-chrome '<oauth-url>' >/tmp/gws_oauth_chrome.log 2>&1 &
```

This reliably surfaced the waiting consent page for approval.

### One-time stale token cache reset

After the successful Chat reauth, `gws chat ...` still initially returned:

- `Request had insufficient authentication scopes.`

The fix was:

```bash
cp ~/.config/gws/token_cache.json ~/.config/gws/token_cache.json.$(date +%Y%m%dT%H%M%S).bak
rm ~/.config/gws/token_cache.json
gws auth status
```

After that, the next error became the correct one:

- `Google Chat app not found`

which confirmed the stale cache had been the earlier blocker.

### Confirmed DM read/send test

Existing DM space tested:

- `spaces/8DJZIcAAAAE`

Readback / space introspection succeeded with:

```bash
gws chat spaces get --params '{"name":"spaces/8DJZIcAAAAE"}'
```

Observed result:

- `spaceType: DIRECT_MESSAGE`
- `membershipCount.joinedDirectHumanUserCount: 2`
- `spaceUri: https://chat.google.com/dm/8DJZIcAAAAE?cls=11`

Message send succeeded with:

```bash
gws chat spaces messages create --params '{"parent":"spaces/8DJZIcAAAAE"}' --json '{"text":"Hi Manthan, please ignore the Euler access request for Lorenzo Madina / madina@ethz.ch. It was a test submission while I was validating the onboarding workflow. Thanks."}'
```

Observed result:

- created message name:
  - `spaces/8DJZIcAAAAE/messages/6cyTFHXlJP8.6cyTFHXlJP8`
- create time:
  - `2026-03-24T22:21:30.011471Z`
- sender type:
  - `HUMAN`

### Important conclusion

For the goal "read and reply in existing chats as Lorenzo", the correct model is:

- user-authenticated Chat API

not:

- interactive Chat app triggers

So for this use case:

- `Interactive features`: off
- `Connection settings`: none
- `Triggers`: none

The Chat app configuration is only needed because `gws` refuses to operate against Chat until the project has valid Chat app metadata.

## Editing the Menzi team-meeting doc

Private document used for verification and editing:

- doc id: `1NnnKeuBg6DJZtpL5gxA5A6kkEhDlZXGiFEkZpbGcAUw`
- title: `Menzi Muck team meetings`

### Read commands used

```bash
gws drive files get --params '{"fileId":"1NnnKeuBg6DJZtpL5gxA5A6kkEhDlZXGiFEkZpbGcAUw","supportsAllDrives":true,"fields":"id,name,mimeType,owners(displayName,emailAddress),webViewLink"}'
gws docs documents get --params '{"documentId":"1NnnKeuBg6DJZtpL5gxA5A6kkEhDlZXGiFEkZpbGcAUw","fields":"title,body/content"}'
```

### Insertion goal

Create a new standup skeleton for `LT` for the meeting on `25.03.26` in the current top-of-document 2026 section.

Target structure:

```text
25.03.26
* Updates:
* [LT]
18.03.26
```

### Important note about Docs indices

The first insertion used a stale index and split the existing `18.03.26` line.
This was fixed immediately by deleting the damaged range and reinserting the correct block.

### Final write requests used

Initial insertion attempt:

```bash
gws docs documents batchUpdate --params '{"documentId":"1NnnKeuBg6DJZtpL5gxA5A6kkEhDlZXGiFEkZpbGcAUw"}' --json '{"requests":[{"insertText":{"location":{"index":620},"text":"25.03.26\nUpdates:\n[LT]\n"}},{"createParagraphBullets":{"range":{"startIndex":629,"endIndex":643},"bulletPreset":"BULLET_DISC_CIRCLE_SQUARE"}}]}'
```

Repair and finalization:

```bash
gws docs documents batchUpdate --params '{"documentId":"1NnnKeuBg6DJZtpL5gxA5A6kkEhDlZXGiFEkZpbGcAUw"}' --json '{"requests":[{"deleteContentRange":{"range":{"startIndex":618,"endIndex":650}}},{"insertText":{"location":{"index":618},"text":"25.03.26\nUpdates:\n[LT]\n18.03.26\n"}},{"createParagraphBullets":{"range":{"startIndex":627,"endIndex":641},"bulletPreset":"BULLET_DISC_CIRCLE_SQUARE"}}]}'
gws docs documents batchUpdate --params '{"documentId":"1NnnKeuBg6DJZtpL5gxA5A6kkEhDlZXGiFEkZpbGcAUw"}' --json '{"requests":[{"deleteParagraphBullets":{"range":{"startIndex":618,"endIndex":627}}},{"deleteParagraphBullets":{"range":{"startIndex":641,"endIndex":650}}}]}'
```

### Verified final top block

```text
25.03.26
* Updates:
* [LT]
18.03.26
* Asphalt breaking planning
```
