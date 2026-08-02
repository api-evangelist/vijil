---
name: Run an adversarial red-team campaign with Vijil
description: Launch a Red Team campaign against a registered agent, poll status, and download normalized results.
api: openapi/vijil-openapi-original.json
operations:
  - jwt_login_v1_auth_jwt_login_post
  - list_tools_v1_tools_get
  - create_campaign_v1_campaigns_post
  - get_campaign_status_v1_campaigns__campaign_id__get
  - get_campaign_results_v1_campaigns__campaign_id__results_get
  - download_campaign_results_v1_campaigns__campaign_id__results_download_get
---

# Run an adversarial red-team campaign with Vijil

Drive Vijil's Red Team product to probe a registered agent with adversarial
attacks and collect normalized findings.

## Steps

1. **Authenticate** — `POST /v1/auth/jwt/login`
   (`jwt_login_v1_auth_jwt_login_post`); use the JWT as a Bearer token.

2. **List available red-team tools** — `GET /v1/tools`
   (`list_tools_v1_tools_get`) to see supported attack categories.

3. **Create a campaign** — `POST /v1/campaigns`
   (`create_campaign_v1_campaigns_post`) with `{team_id, agent_id, ...}`.
   Returns a `campaign_id`; the campaign runs asynchronously.

4. **Poll status** — `GET /v1/campaigns/{campaign_id}`
   (`get_campaign_status_v1_campaigns__campaign_id__get`) until complete.

5. **Get results** — `GET /v1/campaigns/{campaign_id}/results`
   (`get_campaign_results_v1_campaigns__campaign_id__results_get`) for the
   normalized findings, or download the raw JSON via
   `GET /v1/campaigns/{campaign_id}/results/download`
   (`download_campaign_results_v1_campaigns__campaign_id__results_download_get`).

## Conventions & error handling
- Auth: JWT Bearer (`authentication/vijil-authentication.yml`).
- Campaigns are asynchronous — poll status; do not block on create.
- Validation errors return the `422` FastAPI envelope
  (`errors/vijil-problem-types.yml`); a running campaign can be stopped with
  `cancel_campaign_v1_campaigns__campaign_id__cancel_post`.
