```mvn archetype:generate -DarchetypeCatalog=local -DgroupId=com.williamhill.trading.distribution.services -DartifactId=competition-v1 -Dversion=0.0.1-SNAPSHOT -Dpackage=com.williamhill.trading.distribution.services.competition -DserviceType=Competition -DbasePath=/competition/v1/competitions -DdbInstanceUrl=competition-service-db.trading.aws-eu-west-1.dev.williamhill.plc -DdbName=CompetitionDb -DrdsName=competition-service-db -DschemaName=competition_v1 -DssmPathToDbUsername=/concourse/main/rds/competition/admin_username -DssmPathToDbPassword=/concourse/main/rds/competition/admin_password -DentityName=competition```







```mvn archetype:generate -DarchetypeGroupId=com.williamhill.trading.distribution.archetypes -DarchetypeArtifactId=low-velocity-performance-test-archetype -DarchetypeVersion=1.0.2 -DgroupId=com.williamhill.trading.distribution.services -DarchetypeCatalog=local -DgroupId=com.williamhill.trading.distribution.services -DartifactId=competition-v1 -Dversion=0.0.1-SNAPSHOT -Dpackage=com.williamhill.trading.distribution.services.competition -DserviceType=Competition -DbasePath=/competition/v1/competitions -DdbInstanceUrl=competition-service-db.trading.aws-eu-west-1.dev.williamhill.plc -DdbName=CompetitionDb -DrdsName=competition-service-db -DschemaName=competition_v1 -DssmPathToDbUsername=/concourse/main/rds/competition/admin_username -DssmPathToDbPassword=/concourse/main/rds/competition/admin_password -DentityName=competition```

### This is where it's looking for the RDS db.

git@git.nonprod.williamhill.plc:trading_development/global-trading-platform/infrastructure/competition-postgresql-rds.git