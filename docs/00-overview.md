# Overview - lesson 12 (template 19836)

**One-liner**: an n8n-hosted form that searches US job listings on Jobright and renders the top 5
matches on a branded completion page.

## Why this template is a great teaching specimen

- It is **small** (6 functional nodes) but contains a complete production pattern:
  input validation, request shaping, error net, HTML rendering with injection guards.
- It needs **zero credentials** - students finish it in one sitting.
- It is **silently broken** as shipped on recent n8n versions (pit #1), which makes the
  "find it -> prove it -> fix it" arc a real debugging lesson, not a toy.

## User experience

1. Visitor opens the published form URL (`/form/find-relevant-jobs-with-jobright`).
2. Fills *Job Title* (text), *Location* (text, e.g. `San Francisco, CA`), *Remote Preference*
   (dropdown: Any / Remote only / Onsite only / Hybrid only).
3. Sees a results page with up to 5 listings (title, company, location, salary if present,
   apply link) - or a friendly failure page when the search errors out.

## Data flow

```text
formTrigger "Job search form"
    |
    v
Code "Build search request"   -> validates fields, maps remote preference to workModel
    |
    v
HTTP "Search Jobright jobs"   -> POST https://jobright.ai/swan/recommend/visitor-list/jobs
    |   (onError: continueErrorOutput -> "Explain search failure" -> "Show search failure")
    v
Code "Validate and format jobs" -> require success=true + jobList array, take top 5
    |
    v
Code "Render job results"     -> escapeHtml/safeUrl guarded HTML
    |
    v
Form "Show relevant jobs"     -> completion page
```

## Field map (verified against live API, 2026-09-28)

| Template field | Live API source |
|---|---|
| jobTitle | `jobResult.jobTitle` |
| company | `companyResult.companyName` (fallback `jobResult.userCompanyName`) |
| location | `jobResult.jobLocations[]` joined, fallback `jobResult.jobLocation` |
| workModel | `jobResult.workModel` ("Onsite"/"Remote"/"Hybrid") |
| url | `jobResult.url` / `applyLink` / `jobs/info/<id>` reconstruction |