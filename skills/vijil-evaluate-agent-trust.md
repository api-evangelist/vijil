---
name: Evaluate an agent's trust score with Vijil
description: Register an AI agent, run a Diamond trust Evaluation across safety/security harnesses, poll to completion, and retrieve the Trust Score and report.
api: openapi/vijil-openapi-original.json
operations:
  - jwt_login_v1_auth_jwt_login_post
  - get_my_teams_v1_users_me_teams_get
  - create_agent_configuration_v1_agent_configurations__post
  - list_harnesses_v1_harnesses__get
  - create_evaluation_v1_evaluations__post
  - get_evaluation_v1_evaluations__evaluation_id__get
  - get_evaluation_results_v1_evaluation_results__evaluation_id__results_get
  - generate_report_on_demand_v1_evaluations__evaluation_id__report_post
---

# Evaluate an agent's trust score with Vijil

Use the Vijil Console REST API (Diamond) to measure an AI agent's trustworthiness
across reliability, security, and safety before deployment.

## Prerequisites
- A Vijil Console gateway URL (`VIJIL_URL`), e.g. the hosted `https://console-api.vijil.ai`.
- Account email + password.
- A model-hub API key for the model you want to evaluate.

## Steps

1. **Authenticate** — `POST /v1/auth/jwt/login` (`jwt_login_v1_auth_jwt_login_post`)
   with `{email, password}`. Save the returned JWT and send it as
   `Authorization: Bearer <token>` on every request.

2. **Select your team** — `GET /v1/users/me/teams`
   (`get_my_teams_v1_users_me_teams_get`); most calls are team-scoped and the
   team is derived from your token.

3. **Register the agent** — `POST /v1/agent-configurations/`
   (`create_agent_configuration_v1_agent_configurations__post`) with
   `{agent_name, model_name, agent_url, api_key}`. Returns `201` with the new
   agent `id`.

4. **List harnesses** — `GET /v1/harnesses/`
   (`list_harnesses_v1_harnesses__get`). Standard harnesses include `safety`,
   `security`, `reliability`, `privacy`, `toxicity`, `ethics`.

5. **Start the evaluation** — `POST /v1/evaluations/`
   (`create_evaluation_v1_evaluations__post`) with
   `{agent_id, team_id, harness_names, sample_size}`. Returns `202 Accepted`
   with an `evaluation_id`. Use a small `sample_size` (e.g. 50) for a fast score.

6. **Poll to completion** — `GET /v1/evaluations/{evaluation_id}`
   (`get_evaluation_v1_evaluations__evaluation_id__get`). Status progresses
   `starting → pending → running → completed → saving → saved`.

7. **Get results** — `GET /v1/evaluation-results/{evaluation_id}/results`
   (`get_evaluation_results_v1_evaluation_results__evaluation_id__results_get`)
   for the Trust Score, per-harness breakdown, and probe results.

8. **Generate a report** (optional) — `POST /v1/evaluations/{evaluation_id}/report`
   (`generate_report_on_demand_v1_evaluations__evaluation_id__report_post`);
   download HTML/PDF from the Console.

## Conventions & error handling
- Auth: JWT Bearer (`authentication/vijil-authentication.yml`). A `401` means
  the token expired — re-login or `POST /v1/auth/jwt/refresh`.
- Validation failures return `422` with a FastAPI `{"detail": [...]}` envelope
  (`errors/vijil-problem-types.yml`).
- Evaluations are asynchronous — never block on the POST; poll the status
  endpoint. No idempotency-key is supported, so avoid blind retries of create.
