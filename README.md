# uptime-cron

A scheduled check that a service still answers, run somewhere other than the machine it is
watching, because a host cannot report its own death.

Every value it needs is a repository secret: the URL to probe, and where to send a message
when the probe fails twice in a row. Nothing identifying is in this repository, and the
workflow logs — which are public, as they are for every public repository — are written to
stay that way.

* `watch.yml` — one run watches for about an hour, probing every two minutes, and asks for
  the next run when its window closes, so the watching is continuous. Alerts only after two
  consecutive misses. The `*/10` schedule is the backstop that restarts the chain if a
  hand-over ever fails; a cron run that lands mid-window is superseded and shows as
  "cancelled", which is normal. To stop everything, disable the workflow in the Actions tab.
* `heartbeat.yml` — one line a week, so that silence from this repository is distinguishable
  from silence because nothing is wrong.
* `secret-scan.yml` — gitleaks reads the whole history of every branch on each pull request,
  each push to `main` and on demand, and fails if a key-shaped string was ever committed. It
  uses gitleaks' default rules, which skip some paths entirely: lock files
  (`package-lock.json`, `yarn.lock` and the like), `node_modules/`, images including SVG,
  fonts and office documents. This repository holds only YAML and this README, so none of
  those exist here. The scan holds no secrets and can only read the repository. Findings are
  printed redacted.

No third-party actions are used. `watch.yml` and `heartbeat.yml` use no actions at all;
`secret-scan.yml` uses GitHub's own `actions/checkout`, pinned to a full commit SHA.
