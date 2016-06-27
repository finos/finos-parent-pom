
[![Dependencies](https://www.versioneye.com/user/projects/5770ea4001f8e1000998d503/badge.svg?style=flat-square)](https://www.versioneye.com/user/projects/5770ea4001f8e1000998d503)
[![Build Status](https://travis-ci.org/symphonyoss/ssf-parent-pom.svg)](https://travis-ci.org/symphonyoss/ssf-parent-pom)

# Symphony Software Foundation Parent POM
This Maven POM aims to provide common functionalities to Maven projects hosted by the Symphony Software Foundation.

## Usage
Simply define the following `<parent>` in your `pom.xml`:
```
<parent>
  <groupId>org.symphonyoss</groupId>
  <artifactId>symphonyoss</artifactId>
  <version>1-SNAPSHOT</version>
</parent>
```

## Features
- Freeze most common Maven plugin versions using pluginManagement
- Blacklist incompatible licenses of transitive Maven dependencies
- Publish released artifacts on Github download page
- Publish Maven site on Github Wiki
- Publish snapshot (aka nightly builds) and released artifacts on Maven Central (search.maven.org)

## Maven Central Deployment configuration
In order to publish artifacts on Maven Central, it is necessary to [sign up on Sonatype OSS](http://central.sonatype.org/pages/ossrh-guide.html) and [configure your workstation](http://central.sonatype.org/pages/apache-maven.html).

We strongly advise to [encrypt your Maven passwords](https://maven.apache.org/guides/mini/guide-encryption.html)

## Github plugins configuration
To enable github publishing features (downloads and site), you must enable the profile `-Pgithub-release`; to setup github credentials on your local workstation, follow the [Maven github plugins configuration](https://github.com/github/maven-plugins)

## Maven deployment/release

### Requirements
- Install GnuPG; on OSX it is available on Homebrew with `brew install gnupg gnupg2`
- Configure sonatypeoss.org and github.com authentications as explained above

### Deploy
To deploy a nightly build run `mvn clean deploy`
To reject last nightly built deployment run `mvn nexus-staging:drop`

### Release
To promote the latest deployment to a release run `mvn nexus-staging:release`
More info on [Sonatype.org](http://central.sonatype.org/pages/apache-maven.html)
