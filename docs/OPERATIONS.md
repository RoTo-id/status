# Public portfolio monitoring

This repository reuses Upptime for public HTTP checks, incident issues, historical
graphs and a status page. It does not have access to any product database, admin
API or internal Maxine service. Fyndmotorn's check requires HTTP 200 and an explicit
connected-database response; a static landing page alone is not proof of health.
It does not prove inventory freshness, correctness or conversion.

## Workflow ownership

The five remaining workflows are maintained in this repository. Automatic template
and dependency writers were removed deliberately: the upstream generator would
overwrite scoped permissions and token handling. Endpoint configuration remains in
`.upptimerc.yml`; Upptime reads it on each run. History and graphs are preserved.
Vendor upgrades are reviewed in a pull request. The monitor is pinned to the exact
commit of the already-used v1.42.5 release, not upgraded incidentally in this repair.

All jobs use GitHub's temporary repository token, never a stored personal/admin
token. Only the uptime and daily response-time jobs receive `issues: write`
(both execute Upptime's status transition logic); monitoring and presentation
jobs receive `contents: write` to save their outputs. No secrets are forwarded to
the endpoint checker. No repository-wide default permission change is required.

## Activation and recovery

Discovery on 2026-09-28 found schedules disabled by GitHub after inactivity. The
last uptime runs failed to push with HTTP 403 because token contents permissions
were read-only. This patch is preparation, not proof of restored monitoring.
The Pages API also returned 404: an active Pages configuration was not verified.
If no Pages site exists, the owner must enable branch-based Pages from `gh-pages`
after approving the first site build. History and GitHub incident checks can work
independently of that presentation step.

After owner-approved merge, enable these existing workflows and run Uptime CI once:

1. Uptime CI
2. Response Time CI
3. Summary CI
4. Graphs CI
5. Static Site CI

Verify a successful run, a new history timestamp for every endpoint, an accurate
incident for any failed endpoint, and then the status page deployment. A failed
shop URL must stay failed until the product or its canonical URL is verified.
Do not mark an expected customer page's 404 response healthy.

GitHub can disable schedules again after inactivity. A stale `lastUpdated` date
means monitoring is unproven, even if the old status badge is green. GitHub issue
notifications depend on the operator's existing repository watch preferences;
this repair adds no paid notification service or new keys.
Upptime writes history when status changes and at the daily response-time run;
use workflow run timestamps to confirm the five-minute checks are still running.

For a dependency upgrade, inspect the upstream source and action commit, preserve
the permission and credential boundaries, validate YAML locally, then review and
run the candidate after approval. Never run upstream `update-template` directly
against the canonical branch; it overwrites workflows and may remove old outputs.
