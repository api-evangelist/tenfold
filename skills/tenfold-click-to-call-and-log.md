---
name: Click-to-call and log to CRM
description: Originate an outbound call for a user, watch it through completion, then attach notes and link the matching CRM record.
api: openapi/tenfold-openapi.yml
operations: [originateCallV1, getCall, setMatchingRecord, updateCall]
---

# Click-to-call and log to CRM

Use the Tenfold API (base URL `https://api.tenfold.com`) to place an outbound
call and log the result against a CRM record.

## Auth
Obtain a JWT via `POST /v2/users/login`, or an OAuth 2.0 token via
`PUT /v2/oauth/token`. Send it as `Authorization: Bearer JWT <token>`
(or `Authorization: Bearer <token>` for OAuth). See
`authentication/tenfold-authentication.yml`.

## Steps
1. **Originate the call** — `originateCallV1` (`POST /api/v1/calls/originate`)
   with the target number and calling user. Capture the returned `callId`.
2. **Track progress** — subscribe to the call-events webhook (statuses
   `queued` → `ringing` → `in-progress` → `finished`, see
   `asyncapi/tenfold-webhooks.yml`), or poll `getCall`
   (`GET /v2/calls/{callId}`) until it reports finished.
3. **Link the CRM record** — `setMatchingRecord`
   (`PUT /v2/calls/{callId}/set-matching-record`) to associate the correct CRM
   contact/lead/account with the call.
4. **Log notes** — `updateCall` (`PUT /v2/calls/{callId}`) to write call notes
   and finalize the CRM linkage.

## Rules
- v1 endpoints (`/api/v1`) are deprecated; prefer v2 where an equivalent exists.
- No idempotency key is supported — do not blindly retry `originateCallV1`; check
  `getCall` before re-issuing.
- Errors are plain HTTP codes: 401 (bad token), 404 (missing call/record). See
  `errors/tenfold-problem-types.yml`.
