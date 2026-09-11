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

No third-party actions are used.
