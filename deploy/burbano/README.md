# OpenMAIC on the Burbano k3s cluster

Runbook lives in `cluster-ops`; this directory is the manifest set. All nodes are arm64.

- Images: `gh workflow run burbano-build-image.yml -f ref=<tag>` builds `ghcr.io/burbano-inc/openmaic:<tag>` and
  `openmaic-render:<tag>`. The tag must exist in this fork (`git push origin <upstream tag>`).
- Secret `openmaic-env` (namespace `openmaic`, created by hand, never committed): `DEEPSEEK_API_KEY`, `DATABASE_URL`,
  `ACCESS_CODE`, `OPENMAIC_SECRET_KEY`. Pull secret `ghcr-pull` is copied from `personal-platform`.
- Database: `openmaic` db + role on the shared Postgres (`database` ns), created with `kubectl exec ... psql`.
- Apply: `kubectl apply -k deploy/burbano`. Upgrade: build a new tag, then `kubectl -n openmaic set image deploy/openmaic openmaic=...`.
- The render service is scaled to 0 by default; CronJobs scale it to 1 at 22:00 and 0 at 07:00 US Eastern.
- Do not run a 1.5 rc against this database: some upgrades are one-way. A preview gets its own database.
