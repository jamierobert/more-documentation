## Releasing Micro-Services

####Merge + Deploy

1. Go to the project page and enter pipeline. Press play on the tag job of pipeline. 

2. Create feature branch corresponding to the ticket / find feature branch - Check changes and make sure all is good.

4. Merge develop into master - again make sure commit is self explanantory without having to look at ticket.

5. Update Version tracker - this can be found by searching for ppx.

6. Update version tracker to next version - these seem to split by environment but contain all WH apps or a least all of the trading apps.
7. Then we need to prepeare the release docs.

8. ssh to the environment jumphost that is required.
8. Check for the existence of the old service - double check that `-p` is set to the correct env
9. If app exists - we are good.
10. run `cx container sync -c pp2 -t ttf01`
11. This should be enough for all apps except InPlayService - This requires stop/start to ensure that the apps come back and old instances are killed.
12. run `cx location` - We should see the new containers with the new version number.

13. Add message to trading-tap-release specifying Environment, service, Version number and ticket Number.

14. Check environment for currently running service then release using sync.

15. Then sanity check !! - look for tags in Splunk the tag attribute changes when you release so we can guarantee thats unique - could also set up monitoring first and just watch - remember to check for Errors and Warns.
16. Now send email to e2e release.