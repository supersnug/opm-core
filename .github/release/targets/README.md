# Pinned deploy targets

These files are the allow-list for the deploy jobs that hold a Cloudflare
token. `deploy-*.sh` uses the pinned config, not configuration from an artifact.

The upstream deploy token can write every Worker on the account,
so changing what a stage can deploy means changing these
files, which CODEOWNERS (`/.github/ @thdxr`) reviews.

- `files/`, `update/`: the complete `wrangler.json` the deploy script passes to
  the pinned wrangler CLI. The artifact is only the bundled `index.js`; these
  services have no config in the artifact to trust.

A Worker's own code and assets are expected to change freely. Names, routes,
custom domains, crons, Workflow/export names, and every binding (including
service bindings to other Workers) are pinned here.
