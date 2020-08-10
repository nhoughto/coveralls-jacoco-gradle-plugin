# Coveralls Jacoco Gradle Plugin
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT) [![Coverage Status](https://coveralls.io/repos/github/nbaztec/coveralls-jacoco-gradle-plugin/badge.svg?branch=master)](https://coveralls.io/github/nbaztec/coveralls-jacoco-gradle-plugin?branch=master)

A jacoco test coverage reporter gradle plugin for [coveralls.io](https://coveralls.io).

The plugin supports non-root packages in line with the recommended [Kotlin directory structure](https://kotlinlang.org/docs/reference/coding-conventions.html#directory-structure) 
which was missing in many other plugins for the Kotlin ecosystem. 

## Usage

[Gradle Plugin page](https://plugins.gradle.org/plugin/com.github.nbaztec.coveralls-jacoco)

Apply the plugin with the ID: `com.github.nbaztec.coveralls-jacoco`
```
// build.gradle.kts
plugins {
    jacoco
    id("com.github.nbaztec.coveralls-jacoco")
}
```

This will add a gradle task `coverallsJacoco` that can be used to publish coverage report via 
```bash
$ ./gradlew test jacocoTestReport coverallsJacoco
```

Set the value of `COVERALLS_REPO_TOKEN` from the project page on coveralls.io

## Options
```kotlin
// build.gradle.kts

coverallsJacoco {
    reportPath = "" // default: "build/reports/jacoco/test/jacocoTestReport.xml"
    rootPackage = "com.github.nbaztec.foo" // optional, leave out if project has a normal java styled directory structure  
    reportSourceSets = [ sourceSets.foo, sourceSets.bar ] // optional, default: main
    apiEndpoint = "" // optional, default: https://coveralls.io/api/v1/jobs 
}
```

## Excluding Files
Please refer to the official JaCoCo documentation to exclude files from the report. An example configuration is as follows:
```
jacocoTestReport {
    afterEvaluate {
        classDirectories = files(classDirectories.files.collect {
            fileTree(dir: it, exclude: "com/foo/**")
        })
    }
}
```

## CI Usage
The plugin can be used with the following CI providers:

* Travis-CI & Travis-Pro
* CircleCI
* Github Actions
* Jenkins
* Codeship


### Travis
Set the `COVERALLS_REPO_TOKEN` via [Environment Variables](https://docs.travis-ci.com/user/environment-variables/) or [Encryption Keys](https://docs.travis-ci.com/user/encryption-keys/) and set up a job as follows:

```yaml
language: java
script: ./gradlew test jacocoTestReport coverallsJacoco
```

### CircleCI
Set the `COVERALLS_REPO_TOKEN` via CircleCI's [Environment Variables](https://circleci.com/docs/2.0/env-vars/) and set up a job as follows:
```yaml
jobs:
  check:
    docker:
      - image: amazoncorretto:11
    steps:
      - run: ./gradlew test jacocoTestReport coverallsJacoco
```

### Github Actions
Set the `COVERALLS_REPO_TOKEN` via Github's [Environment Variables](https://docs.github.com/en/actions/configuring-and-managing-workflows/using-environment-variables) or [Secrets](https://docs.github.com/en/actions/configuring-and-managing-workflows/creating-and-storing-encrypted-secrets) and set up a job as follows:
```yaml
jobs:
  tests:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
    - name: test and publish coverage
      env:
        COVERALLS_REPO_TOKEN: ${{ secrets.COVERALLS_REPO_TOKEN }}
      run: ./gradlew test jacocoTestReport coverallsJacoco
```