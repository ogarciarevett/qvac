# Contributing

We welcome contributions! Feel free to open a pull request, report bugs, or share ideas through issues and discussions.

## API Reference Docs

The SDK's public API summary is generated from TypeScript source by a pipeline under `docs/website/scripts/`. It is written into the SDK's current documentation line, at `docs/website/content/docs/sdk/<current line>/reference/api.mdx`, resolved from the version manifest in `docs/website/src/lib/versions.ts`. To regenerate it locally:

```bash
cd docs/website
npm install
npm run docs:generate-api -- 0.9.1 --latest  # writes the current line's reference/api.mdx
```

Full workflow, CLI flags, determinism guarantees, and troubleshooting are documented in [docs/website/docs-workflow.md](docs/website/docs-workflow.md). `docs:generate-api` requires `bun` on PATH (listed as a devDependency of `docs/website`).

## PR Labels

CI behaviour is driven by PR labels and the `fork-ci` environment. External fork PRs need merge/release-team approval of the `fork-ci` environment on each workflow run before secret-bearing jobs execute — see [`docs/ci/LABELS.md`](docs/ci/LABELS.md) for the full reference (labels, fork trust, per-commit re-approval), and [`docs/ci/TEAMS.md`](docs/ci/TEAMS.md) for the teams that can approve.

## Dependency bumps

Changing a dependency specifier in any `package.json` also changes
`pnpm-lock.yaml`. Regenerate the lockfile in the same commit:

```bash
pnpm install --lockfile-only
```

This resolves and writes the lockfile without linking `node_modules`. Use the
pnpm version pinned in the root `package.json` `packageManager` field; another
major writes a lockfile the pinned version rejects. If your global pnpm differs,
run it through `npx pnpm@<pinned-version>`.

CI installs with `--frozen-lockfile`, so a specifier the lockfile does not carry
aborts the install rather than resolving around it:

```
specifiers in the lockfile don't match specifiers in package.json
```

That install runs before the project matrix is built, so the failure is not
scoped to the package that was bumped. Every PR in the repo stays blocked until
the lockfile is refreshed on the default branch. Verify with a run that must
exit zero before you push:

```bash
pnpm install --frozen-lockfile --lockfile-only
```

Two PRs bumping different packages both rewrite the lockfile's `importers`
block, so the second to merge conflicts. Do not hand-resolve it. Take either
side, re-run `pnpm install --lockfile-only`, and commit the regenerated file.

## Changelog

Version bumps require CHANGELOG.md updates with version, date, changes by category (✨ Features, 🐛 Fixes, 🔧 Changed, etc.), and PR links.

## Development

- For the standard development workflow used in this monorepo, see [`/docs/gitflow.md`](./docs/gitflow.md).
- For development specifics of each QVAC component, refer to the documentation in the respective subdirectory under `/packages`.
- For the QVAC architecture as a whole, see `/docs/architecture`.
- For the QVAC monorepo structure, see `docs/repository-layout.md`.

## Contribution workflow and AI assistance

This section applies only to [ogarciarevett/qvac](https://github.com/ogarciarevett/qvac), an owner-controlled fork of [tetherto/qvac](https://github.com/tetherto/qvac). It does not change upstream policy or authorize an upstream PR. For this fork, it supersedes conflicting inherited wording only about the PR workflow, AI disclosure, and AI attribution trailers; substantive quality, review, CLA/DCO, license, security, and testing requirements remain in force.

All contributors and agents must work on a topic branch and submit changes by pull request to `main`. Only the human repository owner, after confirming exact GitHub account control, may choose direct changes to `main` or the default branch; existing protections still apply. Agents must never directly update those branches, even when authenticated with the owner's account. GitHub account authentication does not establish that a human is operating it.

AI assistance is welcome. In the PR description, disclose each tool, the model identifier when exposed, and the scope of its help. If the model is not exposed, say `unknown (not exposed)`; do not guess. This scoped disclosure is required even if an inherited instruction otherwise discourages tool attribution.

Example disclosure (replace the example with accurate details):

```text
AI assistance: tool = coding assistant; model = unknown (not exposed).
Scope: helped draft the change and tests.
Human responsibility: I reviewed and understand every submitted change.
Validation: list actual checks and results, or explain checks not run.
```

A named human must review and understand the complete change, verify its tests and claims, check security and privacy, and confirm the right to contribute all code and dependencies under the applicable licenses. AI assistance does not waive existing review, CLA/DCO, signing, coverage, or CI requirements. Follow the existing commit conventions, including Conventional Commits where required. No AI `Co-authored-by` trailer or one-line commit attribution is required; any required human credit or DCO sign-off still applies.

Do not include secrets, personal data, private prompts, conversation transcripts, or hidden reasoning in the disclosure. None is required. Provide only the tool/model, scope, human accountability, and actual validation evidence.
