# Deploy boundary

The deploy workflows (`deploy-files.yml` and `deploy-update.yml`) split every service in two:

- **build** runs repository code (`bun install`,
  `wrangler deploy --dry-run`) with no environment, no secrets, and no OIDC
  token. It uploads the deploy-ready output with a `SHA256SUMS` and reports that
  manifest's digest as a job output.
- **deploy** runs in the `dev` or `production` environment, assumes the stage's
  AWS role, mints a short-lived Cloudflare token from the token issuer
  (`cloudflare-token.sh`), and checks out only `.github/release/`. It verifies
  the artifact against the digest the build job reported, installs the pinned
  `wrangler` CLI (`npm ci --ignore-scripts`), and runs only a
  `deploy-*.sh` script here.

Repository code outside `.github/` does not need Dax's review, so the deploy job
must never execute it. The scripts treat the artifact as data: Worker code and
assets may change freely, but names, routes, custom domains, crons, Workflow and
export names, and every binding (including service bindings to other Workers)
come from `targets/`. The artifact is only `index.js`; the deploy script passes the
pinned `wrangler.json` to Wrangler.

## Why the token can be account-wide

The upstream dev and production workflows use a token that can write any Worker on the
account. That is only safe because the code holding it is this directory: one
reviewed script per service, on a verified artifact, with every destination
pinned in `targets/`. Do not move a deploy back into a job that runs repository
code, and do not widen a script's accepted targets without review.

Production keeps this token only for a Dax-approved fast-forward of `dev`; a
`dev` merge cannot reach the production environment.

## Production promotion notes

Moving `dev` to `production` deploys the same pinned targets with the same
scripts, but production holds an account-wide token and a bad deploy is live.

Before promoting:

1. Confirm every dev deploy job is green on the commit being promoted
   (`deploy-files`; `deploy-update` runs on production
   only).
2. Diff `.github/release/targets/` against `origin/production`. Any change here
   is a change to what production may deploy.
3. Rollback is a fast-forward back to the previous `production` commit, which
   redeploys the previous Worker code from the same pinned targets. If a target
   itself changed and must be reverted, revert it with the deploy commit.
4. The production deploy job runs only `.github/release/` at `github.sha`; it
   does not run repo code. The build job still runs repo code, but with no
   secrets and no OIDC token.
