# Contributing

Thanks for helping improve the Wraith Protocol docs.

## Snippet checks

All TypeScript and JavaScript code fences in `.mdx` files are checked by:

```bash
npm run check:snippets
```

The checker extracts each `ts`, `tsx`, `typescript`, `js`, and `javascript` fence,
writes it to a temporary file, and runs `tsc --noEmit` against that snippet. The
initial gate is intentionally syntax-focused because many current docs snippets
are fragments meant to illustrate API shapes rather than complete programs. It
still catches malformed TypeScript and keeps the docs ready for stricter runtime
validation over time.

Use `no-check` only for intentionally illustrative pseudocode:

````mdx
```typescript no-check
// Pseudocode that is not copy-paste runnable.
```
````

Prefer making snippets compile over opting them out.

## Navigation coverage

Every `.mdx` page in the shipped taxonomy (root pages plus `architecture/`,
`api-reference/`, `contracts/`, `guides/`, `reference/`, and `sdk/`) must be
registered in the `docs.json` navigation tree, and every navigation entry must
resolve to a real file. This is enforced by:

```bash
npm run check:nav-coverage
```

The checker (`scripts/check-nav-coverage.mjs`) scans for `.mdx` files that are
missing from `docs.json` and for nav entries that point at files that no longer
exist. Run it after adding, renaming, or removing a page:

```bash
node scripts/check-nav-coverage.mjs
```

Note: `docs.json` is strict JSON — do not add `//` comments to it, the Mintlify
CLI rejects them. Keep this file comment-free.

## CI

Every pull request runs the snippet checker and the nav coverage check through
GitHub Actions. A separate non-blocking Stellar testnet job is reserved for
end-to-end snippet validation that depends on network availability.

## Mintlify preview redirect check

After Mintlify publishes the pull request preview, run the smoke check from
this PR's checkout, supplying the actual preview base URL (for example,
`https://example.mintlify.app`). On macOS/Linux:

```bash
MINTLIFY_PREVIEW_URL=https://example.mintlify.app \
pnpm run test:preview-redirect
```

In PowerShell:

```powershell
$env:MINTLIFY_PREVIEW_URL = "https://example.mintlify.app"
pnpm run test:preview-redirect
```

The check makes a real request to `/README`, does not follow redirects, and
requires a 3xx response with a `Location` resolving to `/introduction`. The
repository's GitHub Actions workflows do not expose the Mintlify preview URL.
GitHub also requires a `workflow_dispatch` workflow to exist on the default
branch before it can be manually dispatched, so this PR uses the documented
command against the preview URL instead of adding a workflow that cannot yet
be run for this PR.
