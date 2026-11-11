# Maintenance Guide

Covers the monorepo layout: `packages/domain` (Fresh Onion core), `apps/api` (Hono on Cloudflare Workers), `apps/web` (Vite), on Debian stable.

## 1. Ground rules

- **One source of truth for versions.** Node is pinned in `.nvmrc` and `engines` in the root `package.json`. The lockfile (`package-lock.json`) is committed. Setup scripts fail early if the Node version doesn't match.
- **One Node source.** Choose either Debian's packaged Node (security fixes arrive via `apt`) or upstream (NodeSource or a version manager). Don't mix them, and don't run `sudo npm install -g npm@latest` over a dpkg-managed Node.
- **Never upgrade everything at once.** Bump in this order, with build, typecheck and tests between steps: Node → TypeScript → Vite → Hono → Wrangler → everything else.
- **Domain stays boring.** `packages/domain` should have zero runtime dependencies. Any dependency added there needs a written reason.

## 2. Cadence

| When | Task |
|---|---|
| Automated, continuous | Renovate or Dependabot opens PRs; group minor and patch bumps weekly, majors separately |
| Weekly | Review and merge dependency PRs; run `npm audit` |
| Monthly | `apt update && apt list --upgradable`; review the Wrangler and Workers changelog; bump `compatibility_date` if there's a reason to |
| Quarterly | Check Node LTS status and end-of-life dates; check Debian release status; review deprecations in Vite, Wrangler and Hono |
| On security advisory | Act within days; see section 4 |
| Yearly | Plan the move to the next Node LTS and the next Debian stable |

## 3. Sources by component

### Debian and system
- Security announcements: <https://lists.debian.org/debian-security-announce/>
- Security tracker (per-package status): <https://security-tracker.debian.org/>
- Release news and lifecycle: <https://www.debian.org/News/> and <https://www.debian.org/releases/>

### Node.js and npm
- Release schedule and LTS dates: <https://github.com/nodejs/release>
- Blog (security releases): <https://nodejs.org/en/blog/>
- End-of-life lookup: <https://endoflife.date/nodejs>
- npm CLI releases: <https://github.com/npm/cli/releases>
- Only if using upstream packages: <https://github.com/nodesource/distributions>

### TypeScript
- Release notes: <https://devblogs.microsoft.com/typescript/> and <https://github.com/microsoft/TypeScript/releases>
- Watch for: new `moduleResolution` behavior, stricter defaults, and changes to `composite`, project references or `tsc -b`.

### Vite (apps/web)
- Blog: <https://vite.dev/blog/>
- Releases: <https://github.com/vitejs/vite/releases>
- Migration guide (read before every major bump): <https://vite.dev/guide/migration>
- Watch for: minimum Node version changes, plugin API changes, and changes to dev-server proxy behavior.

### Hono (apps/api)
- Releases and changelog: <https://github.com/honojs/hono/releases>
- Docs: <https://hono.dev/>
- Watch for: changes to the `c.req` and `c.json` APIs, middleware moves, and Workers adapter notes.

### Cloudflare Workers and Wrangler
- Developer changelog (filter: Workers, Wrangler): <https://developers.cloudflare.com/changelog/>
- Workers SDK releases (Wrangler): <https://github.com/cloudflare/workers-sdk/releases>
- Compatibility dates: <https://developers.cloudflare.com/workers/configuration/compatibility-dates/>
- Platform status: <https://www.cloudflarestatus.com/>
- Watch for: Wrangler major versions, changes in how static frontends are hosted (Pages versus Workers static assets), and runtime deprecations tied to `compatibility_date`.

## 4. Security response

1. Sources: `npm audit`, <https://github.com/advisories>, `debian-security-announce`, Dependabot or Renovate alerts.
2. Decide whether you're affected: is the vulnerable code in the Worker bundle, the browser bundle, or only dev tooling?
3. Patch: update the dependency (or `apt upgrade` for system packages), regenerate the lockfile, rebuild, run tests, deploy.
4. If no patched version exists, pin a safe version or use npm `overrides`, and leave a dated comment or issue explaining why.
5. Record what changed and when in `CHANGELOG.md`.

## 5. Upgrade checklist

Run for every non-trivial bump (Node, TypeScript, Vite, Hono, Wrangler):

- [ ] Read the release notes and any migration guide
- [ ] Create a branch; bump one component only
- [ ] `npm install` and confirm the lockfile diff is sensible
- [ ] Build `packages/domain` first, then `apps/api` and `apps/web`
- [ ] Typecheck the whole workspace
- [ ] Run tests (the domain package should be testable without Cloudflare or a browser)
- [ ] `wrangler dev` smoke test: the API responds and the web proxy works
- [ ] Deploy to a preview or staging Worker before production
- [ ] Confirm production health after deploy; keep the last good deploy for rollback

## 6. `compatibility_date` policy

- Treat it as a deliberate setting, not an auto-bump.
- Review it at least quarterly: read the changes between your current date and the new one.
- Bump it in its own PR, and test on a preview deployment first.

## 7. Dependency boundaries

- `domain` imports nothing from `api` or `web`, and has no Cloudflare or DOM types.
- `api` and `web` may import `domain`, never each other.
- Enforce this with a lint rule (for example, an import-restriction rule) so the architecture doesn't erode silently.

## 8. Record keeping

- Keep a short `CHANGELOG.md` with one line per upgrade and deploy.
- Note the current Node, Debian and Wrangler versions in the README, and update the note whenever they change.
