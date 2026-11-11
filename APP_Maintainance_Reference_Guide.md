# Maintenance Reference Guide

This document tracks the external update resources, release feeds, and changelogs required to maintain the components of your POSIX-compliant monorepo webapp over time.

---

### 1. Debian Apt & System Primitives (Node.js ecosystem on Linux)
* **Debian Project News & Announcements:** [debian.org/News](https://www.debian.org/News/) for official Debian release and package maintenance updates.
* **Node.js Official Blog & Releases:** [nodejs.org/en/blog](https://nodejs.org/en/blog/) for Long Term Support (LTS) schedules, security releases, and major version shifts.
* **NodeSource Distribution Binary Releases:** [github.com/nodesource/distributions](https://github.com/nodesource/distributions) if tracking upstream Node.js package streams for Debian.

---

### 2. Monorepo Workspaces & Configuration Templates (Vite, TypeScript, npm)
* **TypeScript Release Notes:** [devblogs.microsoft.com/typescript](https://devblogs.microsoft.com/typescript/) and the [TypeScript GitHub Releases](https://github.com/microsoft/TypeScript/releases) for new compiler flags, syntax adjustments, and breaking changes.
* **Vite Blog & Release Feed:** [vite.dev/blog](https://vite.dev/blog/) and [Vite GitHub Releases](https://github.com/vitejs/vite/releases) for bundler plugin API updates and build configuration shifts.
* **npm CLI Changelog:** [github.com/npm/cli/releases](https://github.com/npm/cli/releases) for workspace protocol evolution and package resolution behavior changes.

---

### 3. Cloudflare Workers & Wrangler CLI Ecosystem
* **Cloudflare Developers Changelog:** [developers.cloudflare.com/changelog](https://developers.cloudflare.com/changelog/) (specifically filtering for **Workers**, **Pages**, and **Wrangler**). This is the definitive feed for runtime API updates and compatibility date requirements.
* **Cloudflare Workers SDK Releases:** [github.com/cloudflare/workers-sdk/releases](https://github.com/cloudflare/workers-sdk/releases) for granular tracking of `wrangler` CLI updates, bug fixes, and breaking command line changes.
