**New Relic Instructions**

**App**

1. Pick an app, create a directory, clone product and app and build app. 
2. Take a note of current app version, product name/version and stack tag.
2. Update Java base image to `docker-registry.prod.williamhill.plc/trading/whc-trading-java-newrelic:java8`
3. Remove Wily lines from Dockerfile.
   - `ADD /docker/ApplicationDirectives.pbd /app/`
   - `ADD /docker/IntroscopeAgent.profile /app/`

4. Remove memory overrides at app level (possibly)
5. Remove Wily files `ApplicationDirectives.pbd` and `IntroscopeAgent.profile`, delete containing folder too.
6. Check for old Sonar URL in pipeline files.
6. Merge with project and release to nexus. Increment Major app version by look of Dans.(Not done this on first two, onlyincremented product, Should I cut a release just for a tag - feels wrong like maybe wrong?)

**Product**

1. Remove `extra_hosts` from `docker-compose.yaml`
2. Add `WHC_KEYVALUE: whc/tap_env?recurse` to `docker-compose.yaml`
3. Possibly remove any microservice overrides for memory.(See above in microservice section)
4. Add Folders and code for tf commit and push to product.
9. Tag product and update tracker.
9. Release product to PP3.
8. Apply alerting using terraform.
8. Verify Splunk and New relic.
9. Any tests.



