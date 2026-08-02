---
name: Protect an agent at runtime with Vijil Dome
description: Create and apply a Dome Guardrail configuration to an agent, run a detection, and inspect telemetry traces.
api: openapi/vijil-openapi-original.json
operations:
  - jwt_login_v1_auth_jwt_login_post
  - create_dome_config_v1_dome_configs_post
  - apply_dome_config_v1_dome_configs__config_id__apply_post
  - get_agent_dome_config_v1_agent_configurations__agent_id__dome_configs_get
  - create_detection_v1_detections_post
  - get_detection_v1_detections__detection_id__get
  - search_traces_v1_telemetry_traces_get
---

# Protect an agent at runtime with Vijil Dome

Configure Dome Guardrails (Guards + Detectors) to defend an agent's inputs and
outputs in production, then verify protection with a detection and telemetry.

## Steps

1. **Authenticate** — `POST /v1/auth/jwt/login`
   (`jwt_login_v1_auth_jwt_login_post`); send the JWT as a Bearer token.

2. **Create a Dome config** — `POST /v1/dome-configs`
   (`create_dome_config_v1_dome_configs_post`) with the agent and guardrail
   settings. Returns a `config_id`.

3. **Apply it to the agent** — `POST /v1/dome-configs/{config_id}/apply`
   (`apply_dome_config_v1_dome_configs__config_id__apply_post`) to promote the
   pending config and bind it to the agent.

4. **Confirm the binding** — `GET /v1/agent-configurations/{agent_id}/dome-configs`
   (`get_agent_dome_config_v1_agent_configurations__agent_id__dome_configs_get`).

5. **Run a detection** — `POST /v1/detections`
   (`create_detection_v1_detections_post`) with sample input/output text; fetch
   the verdict with `GET /v1/detections/{detection_id}`
   (`get_detection_v1_detections__detection_id__get`).

6. **Inspect telemetry** — `GET /v1/telemetry/traces`
   (`search_traces_v1_telemetry_traces_get`) to review guarded traffic, latency,
   and blocked events.

## Conventions & error handling
- Auth: JWT Bearer (`authentication/vijil-authentication.yml`).
- Errors follow the FastAPI `422` validation envelope
  (`errors/vijil-problem-types.yml`).
- Config changes are two-phase: create (pending) then apply — inspect before
  applying to production.
