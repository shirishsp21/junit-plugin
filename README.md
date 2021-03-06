JUnit 5 plugin for Kiwi TCMS
----------------------------

[![Build Status](https://travis-ci.org/kiwitcms/junit-plugin.svg?branch=master)](https://travis-ci.org/kiwitcms/junit-plugin)
[![Coverage Status](https://coveralls.io/repos/github/kiwitcms/junit-plugin/badge.svg)](https://coveralls.io/github/kiwitcms/junit-plugin)
[![TP for kiwitcms/junit-plugin (master)](https://img.shields.io/badge/kiwi%20tcms-results-9ab451.svg)](https://tcms.kiwitcms.org/plan/25/)
[![Maven Central](https://img.shields.io/maven-central/v/org.kiwitcms.java/kiwitcms-junit-plugin.svg?label=Maven%20Central)](https://search.maven.org/search?q=g:%22org.kiwitcms.java%22%20AND%20a:%22kiwitcms-junit-plugin%22)

## Installation & usage

Add this into your `pom.xml`:

    <dependency>
        <groupId>org.kiwitcms.java</groupId>
        <artifactId>kiwitcms-junit-plugin</artifactId>
        <version>x.y.z</version>
    </dependency>


This plugin extends `org.junit.jupiter.api.extension.Extension` and has
`junit.jupiter.extensions.autodetection.enabled=true` configured by default in
`pom.xml`. This means Jupiter will pick it up automatically.

You may alternatively decorate your test suite with the `KiwiTcmsExtension` class
but that should be redundant:

    import org.junit.jupiter.api.Test;
    import static org.hamcrest.MatcherAssert.assertThat;
    
    import org.junit.jupiter.api.extension.ExtendWith;
    import org.kiwitcms.java.junit.KiwiTcmsExtension;
    
    @ExtendWith(KiwiTcmsExtension.class)
    public class KiwiJsonRpcClientTest {
        @Test
        public void yourTest(){
            assertThat(...);
        }
    }


## Configuration and environment

Minimal config file `~/.tcms.conf`:

    [tcms]
    url = https://tcms.server/xml-rpc/
    username = your-username
    password = your-password


For more info see [tcms-api docs](https://tcms-api.readthedocs.io).

This plugin is only concerned with parsing the JUnit test results and reporting
them to the backend. Behavior is controlled via environment variables.

For example this is how our own environment looks like::

    #!/bin/bash
    
    if [ "$TRAVIS_EVENT_TYPE" == "push" ]; then
        # same as $TRAVIS_TAG when building tags
        export TCMS_PRODUCT_VERSION=$TRAVIS_BRANCH
    fi
    
    if [ "$TRAVIS_EVENT_TYPE" == "pull_request" ]; then
        export TCMS_PRODUCT_VERSION="PR-$TRAVIS_PULL_REQUEST"
    fi
    
    export TCMS_BUILD="$TRAVIS_BUILD_NUMBER-$(echo $TRAVIS_COMMIT | cut -c1-7)"

For a more detailed example see:
http://kiwitcms.org/blog/atodorov/2019/02/22/how-to-use-kiwi-tcms-plugins-pt-1/


## Changelog

Files signed with C0C5FF36, Kiwi TCMS &lt;info@kiwitcms.org&gt;

### 6.7.2 (01 May 2019)

- Don't create duplicate TestCases. Fixes
  [Issue #50](https://github.com/kiwitcms/junit-plugin/issues/50)
- Still Beta quality

### 6.7.1 (15 April 2019)

- Don't create 2 test runs (Aneta Petkova). Fixes
  [Issue #41](https://github.com/kiwitcms/junit-plugin/issues/41)
- Still Beta quality

### 6.7.0 (10 April 2019)

- Compatible with Kiwi TCMS v6.7 or newer. Fixes
  [Issue #42](https://github.com/kiwitcms/junit-plugin/issues/42)
- Version string updated to reflect minimum server version
- Still Beta quality

### 1.0.3 (21 March 2019)

- Initial release. Beta quality!


## Hacking

You need your `~/.m2/settings.xml` to look something like this

    <settings>
      <servers>
        <server>
          <id>ossrh</id>
          <username>kiwitcms-bot</username>
          <password>***</password>
        </server>
      </servers>
    
      <profiles>
        <profile>
          <id>ossrh</id>
          <activation>
            <activeByDefault>true</activeByDefault>
          </activation>
          <properties>
            <gpg.executable>gpg2</gpg.executable>
            <gpg.keyname>C0C5FF36</gpg.keyname>
          </properties>
        </profile>
      </profiles>
    </settings>

To push a new release/SNAPSHOT update the version string in `pom.xml` and do

    mvn clean deploy -P release

You will need Maven >= 3.3.9 which on RHEL/CentOS system can be obtained via
SoftwareCollections:

    scl enable rh-maven35 /bin/bash
