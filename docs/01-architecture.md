# Architecture & node walk-through

## Node inventory (6 functional + 2 sticky notes)

| # | Node | Type | Role |
|---|------|------|------|
| 1 | Job search form | formTrigger | Hosts the search form at a public path |
| 2 | Build search request | code | Input validation + request body shaping |
| 3 | Search Jobright jobs | httpRequest | POST to the visitor-list/jobs endpoint |
| 4 | Validate and format jobs | code | Contract check + top-5 normalization |
| 5 | Render job results | code | Safe HTML card markup |
| 6 | Show relevant jobs | form (completion) | Closes the request/response loop |
| - | Explain search failure / Show search failure | code + form | Error net branch |

## Request shaping (node 2, verbatim behaviour)

- `jobTitle`: required, 1-200 chars, otherwise **throws** (good - fail fast at the boundary).
- `Location`: optional; when present it becomes `{city, radiusRange: 25}` plus a `locations[]`
  array (API wants both forms).
- `Remote Preference`: whitelisted against `['Any','Remote only','Onsite only','Hybrid only']`;
  mapped to `workModel: 2|1|3`, omitted entirely for `Any`.
- `country` is **hardcoded to 'US'** - this template is US-only by design.

## Error net (the pattern worth stealing)

The HTTP node carries `onError: continueErrorOutput`. Its **error output** is wired to
"Explain search failure" (renders a cause explanation) -> "Show search failure" (second completion
page). Result: transport failures, 4xx/5xx, and validation throws at node 4 all degrade to a
readable page instead of a dead execution. Keep this pattern in every customer-facing automation.

## Rendering safety (node 5)

- `escapeHtml()` on every interpolated string (title/company/location/salary).
- `safeUrl()` only lets `http(s)` URLs into `href` - blocks `javascript:` injection from a
  malicious listing payload.
- On the completion node, *respond with text* containing a full HTML document; n8n serves it
  as `text/html` as long as the markup starts with `<!DOCTYPE html>`.