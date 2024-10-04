# minimal, reproducible example configuration cache caches all environment variables
Gradle configuration cache caches _all_ environment variables, not just the ones that are used in the build.
The behavior is unexpected.

This example uses two environment variables:

- ENVIRONMENT which is [set to 'local' when running with gradle](app/build.gradle.kts#L46-50)
- SOME_ENV which is used by gradle

## Reproducing the issue
```shell
export SOME_ENV=foo
./gradlew run
# Calculating task graph as no cached configuration is available for tasks: run
#   I'm running in local environment
#   SOME_ENV is 'foo'

export SOME_ENV=bar
./gradlew run
# Reusing configuration cache.
#   I'm running in local environment
#   SOME_ENV is 'foo'                  <--- expected 'bar'

# changing ENVIRONMENT bustes the configuration cache
# could also use --no-configuration-cache to get updated SOME_ENV
export ENVIRONMENT=prod
./gradlew run
# Calculating task graph as configuration cache cannot be reused because environment variable 'ENVIRONMENT' has changed.
#   I'm running in prod environment
#   SOME_ENV is 'bar'                  <--- ok after cache bust
```
