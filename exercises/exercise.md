# Exercises (answers included)

## Exercise 1 - Prove pit #1 yourself

Import `workflow.json` (official). Run the form once. Then import `workflow-custom.json` and run it.
Describe the exact difference in what the "Search Jobright jobs" node outputs.

<details><summary>Answer</summary>
Official: an object containing `_readableState`, `_events`, a gzipped byte buffer - an unparsed
response stream. Custom: the parsed JSON object `{success, errorCode, result: {jobList: [...]}}`.
The root cause is the raw-body + response-format combination on recent n8n versions; the JSON body
mode produces a parsed response.
</details>

## Exercise 2 - Add a country selector

Extend the form with a `Country` dropdown (US / UK / CA) and adapt "Build search request" and the
failure text. The endpoint accepts `country: 'GB'|'CA'|'US'` (probe it!). Which existing validation
pattern do you reuse?

<details><summary>Answer</summary>
Add the dropdown field; in node 2 read `input.Country`, whitelist against `['US','GB','CA']`,
default 'US', set `body.country = country`. Reuse the same whitelist pattern used for
`Remote Preference`. Update the Describe nose text in "Explain search failure" accordingly.
</details>

## Exercise 3 - Phone-friendlier rendering

Modify "Render job results" so each card is a table-less block optimised for narrow screens (full
width, 44px line-height links), and add the result count to the page heading.

<details><summary>Answer</summary>
The HTML is fully hand-built in node 5 - inject a `<meta name="viewport">` tag, replace the flex
row layout with `display:block` and apply `font-size:16px; line-height:44px` to apply links. The
count is available as `jobs.length` / `jobCount` from node 4's output and can be interpolated into
the `<h1>` after `escapeHtml()`.
</details>