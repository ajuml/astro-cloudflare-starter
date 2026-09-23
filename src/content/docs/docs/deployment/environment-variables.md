---
title: Environment Variables
description: Environment configuration for local dev and production.
sidebar:
  order: 2
---

This is a static site, so most variables are optional. See `.env.example` for the
full list.

## Local Development

Copy `.env.example` to `.env` for build-time variables:

```ini
# Public production URL (canonical/OG/sitemap)
SITE_URL=http://localhost:4321

# Optional analytics
PUBLIC_GA_MEASUREMENT_ID=
PUBLIC_GTM_ID=
```

Pages Functions secrets (only the R2 cleanup worker) go in `.dev.vars`:

```ini
CLEANUP_SECRET=dev-cleanup-secret-change-me
```

## Production

Configure variables via **Cloudflare Dashboard > Pages > your-project >
Settings > Environment variables**. Add `CLEANUP_SECRET` as an encrypted secret.

## Secrets Validation

The `validate:secrets` script scans the repo for accidentally committed secrets:

```bash
pnpm run validate:secrets
```

It runs during CI and fails the build if a likely secret is detected.

## Decap CMS (GitHub OAuth via Netlify)

The `/admin` CMS (`public/admin/config.yml`) authenticates editors with GitHub,
using a Netlify site as the OAuth proxy — this works even though the site
itself deploys to Cloudflare Pages. One-time setup, done in dashboards (no
files to edit besides `repo:` in `config.yml` if you fork this template):

1. Create a [GitHub OAuth App](https://github.com/settings/developers):
   - Homepage URL: your Netlify site's URL (e.g. `https://<site>.netlify.app`)
   - Authorization callback URL: `https://api.netlify.com/auth/done`
2. In the Netlify dashboard, open the site tied to this project → **Project
   configuration > General > Access control > OAuth** → add the GitHub
   Client ID and Client Secret.
3. Make sure `repo:` and `branch:` in `public/admin/config.yml` point at your
   fork and default branch.
4. Visit `/admin` on the deployed site and sign in with a GitHub account that
   has write access to the repo.

For local editing without GitHub auth, run `netlify dev` (or `npx decap-server`
alongside `pnpm dev`) — `local_backend: true` in `config.yml` writes straight
to the working tree.
