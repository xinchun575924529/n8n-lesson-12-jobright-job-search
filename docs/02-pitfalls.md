# Pitfalls (all production-verified for this lesson)

## Pit #1 - Shipped HTTP node config is broken on recent n8n versions [CRITICAL]

The template configures the HTTP node as **Body = raw** (`contentType: raw`,
`body = {{ JSON.stringify($json.body) }}`) with
`options.response.response.responseFormat = json`.

On the n8n version we verified (2.33.3, 2026-09-28) this combination makes the node emit an
**unparsed raw response stream** (an object with `_readableState`, gzipped byte buffer, etc.)
instead of parsed JSON. The Validate node then throws
`Jobright search failed: unexpected response` - **every single search fails**, forever.
Both our "official" and "patched-headers" variants died this way before the fix.

**Fix** (applied in `workflow-custom.json`): set Body mode to **JSON**
(`specifyBody=json`, `jsonBody = {{ JSON.stringify($json.body) }}`). Same payload, faithful
behaviour, parsed response. After this single change the workflow returns 5 real listings.

Teaching point: when a downstream node receives a giant object with `_readableState`, your HTTP
node returned a stream, not JSON - check the response-format option before blaming the API.

## Pit #2 - The endpoint filters non-browser clients (UA / Origin checks)

Jobright's visitor endpoint returns `400 {"errorCode":20000,"errorMsg":"Invalid request body"}`
(a misleading message; the body was fine) when called from plain script clients (verified with
Python urllib + custom UA). Adding a browser `User-Agent` **or** Origin/Referer headers returns 200.

n8n's own default UA happened to pass on verification day, but treat this as unstable:

- If you suddenly get HTTP 400 with "Invalid request body", suspect client filtering first.
- Countermeasure: add `User-Agent: Mozilla/5.0 ...` (or Origin/Referer) as request headers.
  One header line documented in `workflow-custom.json` node notes.

## Pit #3 - The endpoint is anonymous and unversioned (availability risk)

No SLA, no auth, no changelog. It can change signatures, add a token, rate-limit, or disappear.
That is exactly why the template's error net exists - and why docs/05-hardening adds an external
uptime probe plus fallback search providers (Adzuna free tier, JSearch on RapidAPI free quota).

## Minor notes

- The template's sticky note mentions verification of response contract; our live probe sample
  (state/19836-jobright-sample.json) matches the field names the Validate node expects.
- Form dropdown values are **strings** (`'Any'`, ...) - when you mock inputs in Set nodes for
  testing, keep them as strings or `Build search request` rejects them
  ("Select a valid remote preference").