---
name: audit-bad-patterns
description: Scan a repository for security, reliability, and maintainability anti-patterns and append evidence-backed findings to a Markdown audit log. Use for requested or scheduled anti-pattern audits, not ordinary feature implementation.
disable-model-invocation: true
---

# Audit Bad Patterns

Audit the current repository and append a concise, actionable run summary to `docs/bad-patterns.md`, unless the user names another repository document. Create the document if absent. This is a reporting workflow; fixing application code and scheduling recurring runs require their own instructions.

## Scan and verify

Read repository instructions and relevant component guides, then identify the languages, frameworks, providers, and deployed components from source and project/dependency files. Read only the relevant catalogs:

- [Python backend patterns](references/python.md): Python services, especially FastAPI, SQLAlchemy, authentication, uploads, and deployment.
- [Shared .NET Windows desktop patterns](references/dotnet-desktop.md): WinUI or WinForms applications and their shared libraries/services. Covers threading, DI, SQLite, scheduling, networking, privileged IPC, cryptography, updates, COM/native interop, configuration, logging, localization, and filesystem operations. Inspect only groups for components actually present.
- [WinUI patterns](references/winui.md): WinUI 3 / Windows App SDK applications; read alongside the shared .NET catalog.
- [WinForms patterns](references/winforms.md): Windows Forms applications; read alongside the shared .NET catalog.

For mixed repositories, read each applicable catalog; for two desktop shells, read both shell catalogs and the shared catalog once. The catalogs are the user's baseline; include other concrete anti-patterns only when supported by comparable evidence. Preserve existing catalog IDs when comparing prior runs. Apply each pattern only where its technology and context exist. Package names and search terms are candidate locators, not defects. Verify installed versions, supported frameworks, configuration defaults, and advisories against primary documentation before reporting version-dependent issues; being older than the latest release alone is not a finding.

Default to the whole repository's first-party source, configuration, migrations, and deployment definitions. Exclude generated code, build output, vendored dependencies, and the audit log itself. Read tests as evidence for behavior and safeguards; do not mistake deliberately vulnerable fixtures or development settings for production behavior. Respect a narrower requested scope.

Use `rg` to find candidates, then read implementations and relevant callers, middleware, transaction boundaries, and configuration. A search match is not a finding. Verify runtime paths and protections before reporting. For connection capacity, inspect replicas, workers, pool size, overflow, and server budget together; do not invent missing deployment values. For missing safeguards, inspect shared enforcement rather than inferring absence from one endpoint.

Report confirmed issues with precise code evidence. Put plausible but unproven concerns under **Needs verification**, stating what evidence is missing. Do not turn unknown production values, unmeasured scale, or design preferences into confirmed defects. No fixed token lifetime, worker count, or storage architecture is universally required.

Use the smallest read-only checks that resolve uncertainty. Do not run migrations, access production services, modify tests/application code, or send messages as part of an audit. If a check cannot run, describe the limitation and continue the supported scan. When authoritative technical verification is needed, consult primary documentation for the installed versions.

## Append the report

Read the existing document before writing. Preserve all prior text and unrelated edits; append one completed run section. Re-read immediately before appending if another writer may have changed it. Do not commit.

Record date/time with timezone, current commit, dirty-tree status, scope, and material limitations. Link to repository-relative files from the report's directory and give exact current line numbers and symbols, for example `[routes.py](../services/api/app/routes.py), lines 42–51, create_match`. The recorded revision anchors references as code changes. Include short redacted snippets only when useful; never copy actual credentials, tokens, codes, cookies, or personal data.

Give each confirmed finding a stable key combining catalog ID, repository-relative file, and symbol (not line number). Compare with prior runs: label findings **new**, **recurring**, or **changed**. Keep recurring entries brief with fresh verified references. Mark a previous finding **resolved** only after checking its specific path and condition; issues outside this run's scope remain unchecked. Avoid duplicate findings for one underlying cause.

Use this compact shape, omitting empty optional sections:

```markdown
## Audit — <timestamp and timezone>

Revision: <commit>; working tree: <clean/dirty>
Scope: <components and exclusions>; limitations: <if any>
Summary: <counts of new/recurring/changed findings and severity totals>

### Confirmed findings

- **<severity> · <new/recurring/changed> · <title>** (`<stable key>`)
  - Evidence: <linked files, exact lines, symbols, and cross-file path>.
  - Impact: <concrete failure and conditions under which it occurs>.
  - Recommendation: <smallest practical correction>.

### Resolved since prior run

- <prior key, verified correction, and current reference>.

### Needs verification

- <candidate, evidence, missing fact, and useful next check>.

Coverage: <catalog groups checked, not applicable, or incomplete; safeguards verified>.
```

Assign severity from demonstrated impact: critical for direct severe compromise, high for substantial security/data-integrity failures, medium for credible reliability/performance risks, low for limited maintainability/operational risks. Uncertainty belongs in verification notes, not inflated severity.

If no confirmed issues are found, append a zero-findings summary with coverage and limitations; do not imply the repository is proven safe. Verify the final diff contains only the intended appended report. Finish with the report path, counts, and most important findings or limitation.
