# 2021-05-03 Heroku ecosystemguide opatlas app request timeouts

## What

The opatlas deployment hosting most of the older catalyst maps responded slowly or not at all to requests leading to broken and partial maps.

2021-05-03 (all times UTC)

* 05:20 First request timeout in heroku events graph
* 14:20 Second block of timeouts in events graph
* 14:45 Y emails Martin that maps are not working
* 15:12 Martin investigates and sees worker restarts in logs
* 15:15 Martin creates db backup
* 15:24 Martin restarts all dynos in the app and verifies fix
* 15:26 Martin responds to Y


## Why

Not completely clear what triggered the degradation, or even entirely why restarting the dynos fixed it.

Some api requests were succeeding, but taking over a second, others were hitting the 30s heroku internal timeout:
```
...  path="/voter/0?_=1620054670206" ... dyno=web.1 connect=297ms service=1286ms status=200 bytes=203 ...
... path="/pp_votes/54/0?_=1620054670212" ... dyno=web.1 connect=1ms service=30000ms status=503 bytes=0 ...
```

When the worker was failing to respond, it was getting replaced:
```
... [CRITICAL] WORKER TIMEOUT (pid:87)
... [INFO] Worker exiting (pid: 87)
... [INFO] Booting worker with pid: 91
```
Which suggests the parent process must have been in some kind of bad state.

No code changes or deploys had happened since 2021-04-21 though that was an upgrade to the newest Heroku-20 stack, as the previous Heroku-16 was being deprecated. The deprecation itself was happening across the preceding weekend, so a platform change on the heroku change seems the most likely trigger.

Also noticed the db is at '9,903 of 10,000' rows for the free tier. Likely unrelated, and not an issue if new maps are not being added there.


## Wonderings

The basic heroku monitoring did pick up the timeouts, so if alerts were configured this could have been addressed without Y needing to email for assistance. Though, it was a bank holiday so that's not a complete given. Also false positives are not uncommon on heroku due to service restarts and similar.

The logging and metrics available are insufficient to make a good guess at the root cause. Both are being addressed for the new codebase, but not this legacy deployment.

Moving older maps to the new platform may make support easier in the long run. The effort required for this will vary however, as older maps have various one-off features which may not yet have corresponding definitions. So far the cost of keeping the old platform up and running has not been too large, and has been useful as a cross reference as the platform evolves.
