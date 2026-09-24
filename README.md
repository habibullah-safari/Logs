# Logs
Daily engineering logs 

### SSH key
   - search
      - ls -la ~/.ssh
   - create
``` bash
ssh-keygen -t ed25519 -C "habibullah.safari7@gmail.com" 
``` 
   - Enter * 2
   - search again
      - ls -la ~/.ssh
   - check inside it again copy the key **.pub**  **the public one** to gitlab/github
```bash
cat ~/.ssh/id_ed25519_gitlab.pub 
```


### Mailing system OAuth
- Gmail OAuth2 setup for nilBeauti server
=====================================

Overview
--------
This project can send appointment confirmation emails using Gmail OAuth2. The client (account owner) must perform a one-time OAuth consent flow to provide a refresh token. The refresh token is stored as an environment secret (never commit it to source control).

Steps for the client (one-time)
-------------------------------
1. Create a Google Cloud project and enable the Gmail API:
   - Visit: https://console.cloud.google.com/apis/library/gmail.googleapis.com

2. Configure OAuth consent screen:
   - APIs & Services → OAuth consent screen
   - Choose "External" or "Internal" depending on the account and follow prompts.

3. Create OAuth credentials:
   - APIs & Services → Credentials → Create Credentials → OAuth client ID
   - Application type: "Desktop" or "Web" (either works for this flow).
   - For redirect URI you can use `urn:ietf:wg:oauth:2.0:oob` or a localhost callback.

4. Run the helper script locally to get a refresh token:
   - On your machine, in the repo `server` folder, set these env vars temporarily:

     ```bash
     export GMAIL_OAUTH_CLIENT_ID=871675984390-f1tlurlkinpi59r2kumtrde340in30i9.apps.googleusercontent.com
     export GMAIL_OAUTH_CLIENT_SECRET=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
     # optional: export GMAIL_OAUTH_REDIRECT_URI=http://localhost:3001/oauth2callback
     node scripts/get_refresh_token.mjs
     ```

   - Open the printed URL in a browser, authorize the app, and paste the code back into the script.
   - The script prints `GMAIL_OAUTH_REFRESH_TOKEN=...` — copy that value.

5. Add these environment variables to the Render server service (or your host):
   - `GMAIL_USER` = the sending Gmail address (e.g., no-reply@yourdomain.com or your@gmail.com)
   - `GMAIL_OAUTH_CLIENT_ID` = from step 3
   - `GMAIL_OAUTH_CLIENT_SECRET` = from step 3
   - `GMAIL_OAUTH_REFRESH_TOKEN` = refresh token from step 4

6. Restart/redeploy your Render server service so env vars are picked up.

Notes and security
------------------
- The refresh token allows sending as the `GMAIL_USER`. Treat it as a secret. The client should paste it into Render's environment variables and not share it.
- If using a Google Workspace account and internal users only, set the consent screen accordingly to avoid verification delays.
- For highest deliverability and easier management, transactional providers (Postmark/SendGrid/Amazon SES) are alternatives — they also require the client to create and supply a secret key.

If you want, I can prepare a short message you can send to the client with the exact copy/paste steps.
