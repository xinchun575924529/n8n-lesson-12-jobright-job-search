# n8n Lesson 12: Build a Job-Search Web App with n8n Forms + Jobright (US jobs)

> Official template: [Find relevant US job listings by role and location](https://n8n.io/workflows/19836/) (template ID 19836, 6 functional nodes + teaching sticky notes)

Turn an n8n form into a tiny job-search app: the user enters a **job title**, a **location** and a
**remote preference**, the workflow calls Jobright's anonymous visitor-search endpoint, and renders
the top-5 US job listings as a self-updating results page - with a full search-failure branch.

## What you will learn

1. **n8n Hosted Forms as a UI**: form trigger -> processing -> `Ending: completion` page, a real
   request/response web-app pattern with zero frontend code.
2. **Calling an undocumented public API safely**: body shaping, radius/locations arrays, and how to
   defend yourself when the provider filters non-browser clients.
3. **The `onError: continueErrorOutput` error net**: every failure of the search node is routed into
   a dedicated "Explain search failure -> Show search failure" branch instead of a dead workflow.
4. **Two hard, production-verified pitfalls** (docs/02-pitfalls.md), including a config combination
   that silently breaks the shipped template on recent n8n versions.

## Course files

| File | Content |
|---|---|
| `workflow.json` | Official template, byte-faithful |
| `workflow-custom.json` | **Patched** version (pit #1 fixed, no credentials needed anywhere) |
| `docs/00~05` | Overview / architecture / pitfalls / L2 verification / free-run options / hardening |
| `exercises/exercise.md` | 3 hands-on exercises (answers included) |

## L2 verification results (all real-tested for this lesson)

- **A unit level**: 4 Code nodes transplanted verbatim, **32/32 assertions passed**
- **B mock**: skipped by decision (C real-run was free and stronger)
- **C real run**: workflow executed twice via n8n CLI against the **live Jobright endpoint** -
  `jobCount=5` real US listings returned both times (e.g. "Software Engineer I - Everlaw - Oakland, CA")

## What it does in one minute

```text
form submission (Job Title / Location / Remote Preference)
  -> Build search request  (validate + shape body, US-only)
  -> Search Jobright jobs  (POST visitor-list/jobs)
  -> Validate and format jobs  (top 5, failure -> error branch)
  -> Render job results  (escapeHtml + safeUrl, no HTML injection)
  -> Show relevant jobs  (form completion page)
```

## Cost

Zero. No API key, no account, no credential node - the only dependency is Jobright's public
visitor endpoint, whose stability you should monitor (see docs/05-hardening.md).