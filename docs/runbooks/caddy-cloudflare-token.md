# Runbook: Caddy certificate renewal fails (Cloudflare token)

Date: 2026-11-07. Status: resolved.

**Summary.** `grafana.parzezal.dev` showed an expired certificate in Firefox because Caddy could not renew it. The Cloudflare API token Caddy uses for the DNS challenge was no longer valid, so every renewal since early October failed with HTTP 403. A new account token replaced it, and Caddy renewed the Grafana and metrics certificates after the container was recreated.

## Alert/Event

- Firefox: `SEC_ERROR_EXPIRED_CERTIFICATE` on `grafana.parzezal.dev`. HSTS blocks any bypass.
- Caddy logs: `could not get certificate from issuer ... HTTP 403: Code:9109 Message:Invalid access token`.

## Immediate Action

Check which certificates are affected. `metrics.parzezal.dev` was also overdue for renewal, so the cause was shared and not specific to Grafana.

## Diagnosis

Grafana's own logs were not the cause. The `reason=expired` lines are a search-index cache message.

Find the real error in Caddy's logs, with the routine renewal messages filtered out:

```bash
docker logs caddy 2>&1 | grep -v -E "needs renewal based on ARI|queuing for renewal|got renewal info|updated and stored ACME" | grep -i -E "grafana|metrics|error|renew|acme|obtain|challenge" | tail -30
```

Confirm the token is invalid at Cloudflare. For a user token:

```bash
curl https://api.cloudflare.com/client/v4/user/tokens/verify -H "Authorization: Bearer $CLOUDFLARE_API_TOKEN"
```

Result: `success: false`, `Invalid API Token`. The old token was a user token with no expiry. Why it became invalid is unknown: no `.env` in Git history, no revocation email, and no audit-log event found.

## Resolution

1. In Cloudflare, create an **account token** named `caddy-homelab-parzezal`.
   - Policy: `parzezal.dev` only.
   - Permissions: DNS Read, DNS Write, Zone Read.
   - Expiry: 2027-11-07. IP filtering: none.
2. Verify the new token with Cloudflare's token-verify call for account tokens.
3. Put the value in `.env` as `CLOUDFLARE_API_TOKEN`. Never commit it.
4. Recreate Caddy. A restart does not re-read `.env`. Run from `docker/`:

   ```bash
   docker compose up -d caddy
   ```

5. Check that the container has a value without printing it:

   ```bash
   docker exec caddy sh -c 'printf %s "$CLOUDFLARE_API_TOKEN" | wc -c'
   ```

6. Watch for a successful renewal:

   ```bash
   docker logs -f caddy 2>&1 | grep -i -E "grafana|obtain|renew|error|challenge"
   ```

Caddy retries on a 10-minute loop. Renewals for `grafana` and `metrics` succeeded.

## Prevention

- TODO: rotate the token before 2027-11-07. Set a calendar reminder for 2027-10-07.
- TODO: add a Blackbox Exporter probe for certificate expiry, so a failed renewal alerts weeks early (see ROADMAP).
- TODO: review the unrelated token in Cloudflare marked "created by Cloudflare Agent". Its origin is unknown.
- Caddy only logs the failure. Nothing warned of it for about a month.
