---
name: Pull call analytics and transcripts
description: Search a user's calls, retrieve transcripts and keywords, and roll up team or company analytics.
api: openapi/tenfold-openapi.yml
operations: [searchAnalytics, getUserCalls, getCallTranscript, getCallKeywords, getCompanyAnalytics]
---

# Pull call analytics and transcripts

Use the Tenfold API (base URL `https://api.tenfold.com`) to mine call activity
and conversation analytics.

## Auth
Send a JWT or OAuth 2.0 bearer token (see
`authentication/tenfold-authentication.yml`).

## Steps
1. **Find calls** — `searchAnalytics` (`GET /v2/analytics/search`) with query
   terms, or `getUserCalls` (`GET /v2/analytics/users/{userId}/calls`) for a
   specific user. Page with `page` / `pageSize` (max 500).
2. **Get the transcript** — `getCallTranscript`
   (`GET /v2/analytics/calls/{callId}/transcript`).
3. **Extract keywords** — `getCallKeywords`
   (`GET /v2/analytics/calls/{callId}/keywords`) for topic signals.
4. **Roll up** — `getCompanyAnalytics` (`GET /v2/analytics/company`) or the
   team endpoints for aggregate metrics.

## Rules
- Pagination is page-number based (`page` default 0, `pageSize` v2 max 500). See
  `conventions/tenfold-conventions.yml`.
- Handle 404 for calls without transcripts; handle 401 for expired tokens
  (`errors/tenfold-problem-types.yml`).
