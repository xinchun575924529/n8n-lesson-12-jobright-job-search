# Running it for free (and from China)

**Good news first: this lesson needs no replacement at all.** The endpoint is anonymous and was
reachable from China without proxy on verification day (0.9-2.2s round trip).

What we still recommend knowing:

## If the endpoint gets blocked / flaky in your region

- Add a proxy at the node level (`options.proxy`) - per-request, no system-wide proxy needed.
- Add browser-like headers (see docs/02-pitfalls.md, pit #2).

## Alternative job data sources (free tiers, for exercises 2-3)

| Provider | Free tier | Key needed |
|---|---|---|
| Jobright visitor endpoint (this course) | anonymous | none |
| Adzuna API | ~25 calls/min free tier | yes (free signup) |
| JSearch (RapidAPI) | ~200 calls/month free | yes (free signup) |
| The Muse API | 500 calls/month | yes (free signup) |

The request-shaping and error-net patterns transfer 1:1 - that is the real skill of this lesson.

## Rendering/language localization

To serve a non-English audience, the strings live in two Code nodes (Build search request's error
messages, Render job results' HTML). Translating them is a 15-minute exercise and produces a
fully localized micro-app.