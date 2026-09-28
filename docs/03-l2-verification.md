# L2 verification report - lesson 12

## A - Unit level: 32/32 PASSED

All 4 Code nodes transplanted into a test rig (same harness pattern as lessons 01-11) and exercised
against boundary cases: missing/over-long job titles, invalid remote preference values, workModel
mapping, radius/location shaping, error placeholders, `escapeHtml`/`safeUrl` behaviours.

- Rig: `scripts/build-l2-test-19836.py` -> `workflows/wf-l2test-19836.json`
- Result: **32/32 assertions green**

## B - Mock: SKIPPED (management decision)

Skipped because the C real run is free, fast and strictly stronger evidence for this template
(no credential, single API call).

## C - Real run: PASSED (twice)

Two variants executed through the n8n CLI (`n8n execute --id ...`) on 2026-09-28, hitting the live
Jobright endpoint from the office network in China **without any proxy**:

| Run | Config | Result |
|-----|--------|--------|
| L2C19836off0001 | JSON body, no extra headers | executed OK, `jobCount=5` |
| L2C19836fix0001 | JSON body + browser headers | executed OK, `jobCount=5` |

First listing (identical in both runs, live data): *Software Engineer I - Everlaw - Oakland, CA*,
posted "13 minutes ago", apply link `jobright.ai/jobs/info/6a95d42d9fcec5442372f78d...`.

Raw execution dumps and probe logs:

- `state/19836-l2-findings.md` - full findings log
- `state/19836-jobright-probe2.json` - header-filter matrix (UA/origin experiments)
- `state/19836-jobright-sample.json` - raw API response sample (field map source)

### Bonus finding during C

`executeWorkflowTrigger` workflows cannot be started via the n8n CLI
(`No active execution found`) - test workflows must start from a Manual Trigger node.