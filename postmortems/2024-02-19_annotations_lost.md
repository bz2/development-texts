# 2023-02-19 Annotations not received/recorded by platform

## What

A browser-specific edge-case in handling large POST requests over the fetch api caused all annotation updates to fail after many comments had been left on a map, which meant data loss for our most engaged user on a project.

2023-02 (all times UTC)

* -19 (Monday) 15:24 S begins recording comments using annotation feature - makes 63 comments but only 45 are visible after reloading the page.
* -19 18:07 S emails HM to report they had lost 18 comments and had re-entered the comments - and suspected suspicion they were lost on page refresh, not due to losing internet connection.
* -20 (Tuesday) ~09:30 HM raises first report during check-in.
* -20 AL checks map term bags for comments left by user and shared timestamp for first recorded comment.
* -21 (Wednesday) Comments lost a second time; S confirms details including which map, and browser used - Chrome.
* -22 (Thursday) 15:32 HM reports second incident. HM and AL review the number of recorded comments and whether there were any duplications. HM requests more details from user. No evidence in platform log that missing 18 comments had been attempted to be sent.
* -22 18:25 AL and MP discuss how annotation saving works, and theories of how comments could be lost.
* -23 (Friday) ~14:30 AJ suggests automated browser testing for annotations is partially repurposed to attempt to record a high number of comments on the platform to recreate error. AL & MP suggest reviewing access logs. 
* -23 16:12 AL confirms access logs for annotations API checked and reports on POST requests, GET requests, responses and server errors.
* -23 16:57 S loses comments a third time. HM updates second report and provides records of discussion with user. 
* -24 (Saturday) 14:19 RT manually reproduces error in Google Chrome with varying number of limts on comments (35 to 45) depending on the map and comment length. AL is able to establish that limit is 64kb. Error is not reproducible in Firefox. Error is reproducible in Safari.
* -26 (Monday) 10:29 MP deploys fix to staging and continues working on more complete solution.
* -27 (Tuesday) 13:09 Fix v154 is deployed to production removing `keep-alive` only.
* -29 (Thursday) 15:44 Fix v155 is deployed to production compressing annotations, and using local storage cache.


## Why

After making lots of comments, all newer ones did not save
because the total size of the comments encoded as RDF was greater than 64KiB
because Chrome (but not Firefox) follow the letter of the WhatWG fetch spec
that states a network error must be returned if `keepalive` is true and "the sum of `contentLength` and `inflightKeepaliveBytes`"[[1]] exceeds this limit.

While the annotation feature was designed with the intention of being a mechanism for capturing feedback and running user exercises, it's had minimal real use on projects. This means the expectations of the development team and consultants are that the user interface is a little janky, and there are some edge-case problems with how annotations get recorded over the api. It also means the overall process, both in procedure and technically have not been refined or optimised.

red herrings

client-side monitoring

Visual Meaning does not have a culture of providing technical support. The assumed environment is one where a client request or email should be treated as priority one, but does not match what we experience from the software products we interact with, and has not been part of onboarding or training. Combined with slow-responding client organisations, this means everyone is too used to glacial cycle times on work and issue resolution, so the appropriate level of attention and urgency was not applied in this case.


## Wonderings

call clients when they have issues and watch them
or at least give a good bug template so technical details are passed along

local storage cache allows for recovery but is not seamless

save-on-exit still can be flakey, short window before tab gets finally frozen

add client side error monitoring, resolve all promises to a last-ditch catcher.



[1]: https://fetch.spec.whatwg.org/#http-network-or-cache-fetch
