[![Maven Central](https://img.shields.io/maven-central/v/org.finos/finos.svg?maxAge=2592000)](http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22finos%22)
[![Renovate](https://img.shields.io/badge/renovate-enabled-brightgreen.svg)](https://renovatebot.com)
![Build](https://github.com/finos/finos-parent-pom/workflows/finos-parent-pom-build/badge.svg)
[![FINOS - Incubating](https://cdn.jsdelivr.net/gh/finos/contrib-toolbox@master/images/badge-incubating.svg)](https://finosfoundation.atlassian.net/wiki/display/FINOS/Incubating)

# FINOS Parent POM
This Maven POM aims to provide **_common functionalities to Maven projects_** hosted by FINOS, the Fintech Open Source Foundation.

* [Usage](#usage)
* [Features](#features)
  * [Plugin Management](#plugin-management)
  * [Enforcing rules](#enforcing-rules)
  * [Attach sources](#attach-sources)
  * [sonarqube\.com Integration](#sonarqubecom-integration)
    * [Enable SonarQube integration](#enable-sonarqube-integration)
  * [Travis CI Integration](#travis-ci-integration)
  * [scan\.coverity\.com Integration](#scancoveritycom-integration)
    * [Enable Coverity Scan integration](#enable-coverity-scan-integration)
  * [Whitesource integration](#whitesource-integration)
    * [Enable Whitesource](#enable-whitesource)
* [Workstation setup](#workstation-setup)
  * [Travis](#travis)
  * [Maven deploy/release](#maven-deployrelease)
  * [Github plugins configuration](#github-plugins-configuration)
* [Artifact Deployment](#artifact-deployment)
  * [Snapshot Deployment](#snapshot-deployment)
  * [Release Deployment](#release-deployment)
* [Docker integration](#docker-integration)

## Usage
Simply define the following `<parent>` in your `pom.xml`:
```
<parent>
  <groupId>org.finos</groupId>
  <artifactId>finos</artifactId>
  <version>4</version>
</parent>
```

If you want to use a SNAPSHOT version (ie `1-SNAPSHOT`), make sure to add the following repository in your `pom.xml`.

```
<repositories>
  <repository>
    <id>sonatype-oss-public</id>
    <url>https://s01.oss.sonatype.org/content/groups/public/</url>
    <snapshots>
        <enabled>true</enabled>
    </snapshots>
  </repository>
</repositories>
```

## Features
- **Plugin Management** for the most common Maven functionalities
- **Simple artifact deployment and release** on Maven Central and Github (`releases` project page)
- **Site (documentation) generation** on Github Wiki (WIP)
- **Enforcing rules** to validate the Foundation code acceptance criteria
- **Attaching sources** to generate and upload artifacts containing current project's source code
- **versioneye.com integration** for security and licensing validation
- **sonarqube.com integration** for code analytics
- **Travis CI** integration for Continuous Integration
- **scan.coverity.com** integration for security validation

### Plugin Management
- `build/pluginManagement/plugins` lists all plugins and sets their latest versions; versions will need to be kept up to date
- `maven-compiler-plugin` sets `source` and `target` compiler JDK version to `1.8`; you can override it by setting the following properties
```
<maven.compiler.source>1.7</maven.compiler.source>
<maven.compiler.target>1.7</maven.compiler.target>
```
- `maven-javadoc-plugin` generates Javadoc JAR (which gets deployed and released along with other artifacts) during each JAR project build
- `maven-remote-resources-plugin` includes the contents of [finos-jar-resource-bundle](https://github.com/finos-fdx/finos-jar-resource-bundle) (mostly licensing-related files) in each artifact that gets built.
- `maven-source-plugin` generates a JAR with the source code (which gets deployed and released along with other artifacts) during each JAR project build
- `nexus-staging-maven-plugin` enables the deployment and release process against [Sonatype OSS Repository](https://s01.oss.sonatype.org) (which is synced with Maven Central)

### Enforcing rules (currently disabled)
The current rules currently defined are:

1. Use Maven 3.0 or greater
2. Define a groupId that starts with `org.finos` or `org.finos.`

The Foundation rules are implemented as Maven Enforcer custom rules and the [source code is available on GitHub](https://github.com/finos/finos-enforcer-rules).

### Attach sources
By enabling the `-Pattach-sources` profile, the `maven-source-plugin` gets invoked and a `JAR` artifact containing the project's source code is created.

When enabled, the `maven-install-plugin`, `maven-deploy-plugin` and `maven-release-plugin` will take the source code JAR file in consideration and including in the respective plugin lifecycles.

#### Additional configuration
You can tweak the following properties:
- `versioneye.trackPlugins` : whether to track Maven plugin, defaults to `false`
- `versioneye.skipScopes` : comma-separated list of Maven dependency scopes that must be ignored by versioneye; defaults to `test,provided`
- `versioneye.propertiesPath` : located `versioneye.properties` file and defaults to `./src/main/resources/versioneye.properties`

### sonarqube.com Integration
The [SonarQube®](https://sonarqube.com/) platform is an open source quality management platform, dedicated to continuously analyzing and measuring the technical quality of source code, from project portfolio down to the method level, and tracking the introduction of new Bugs, Vulnerabilities, and Code Smells in the Leak Period.

This Parent Pom uses and configures the [Sonar Maven Plugin](http://sonarsource.github.io/sonar-maven/) to submit data to SonarQube.

#### Enable SonarQube integration
**TODO! update docs to SonarCloud**
1. Request an invitation (send email to `nemo AT sonarsource.com`) to Sonarqube.com for the project, mentioning that the project belongs to FINOS
2. Define a `SONAR_TOKEN=my_sonarqube_token`; you can find the token in your [Sonar security settings](https://sonarqube.com/account/security); as above, make sure to encrypt the variable if you're using Travis CI: `travis encrypt SONAR_TOKEN=my_sonar_token`
3. Add `sonar` profile to your Maven command: `mvn clean package -Psonar`; the Sonar Plugin will run right after the `package` Maven phase is invoked

### Travis CI Integration
[Travis CI](https://travis-ci.org) is a hosted, distributed continuous integration service used to build and test software projects hosted at GitHub.

The Foundation Parent Pom doesn't have (yet) any dependency or integration with Travis CI; to enable CI on a Foundation project:

1. Drop a `.travis.yml` file in the root folder of your github repo (check this [.travis.yml](.travis.yml) as sample)
2. Register (using your Github account) on Travis CI and [access your profile](https://travis-ci.org/profile) to enable (switch on) the github repository of your choice

### scan.coverity.com Integration
[Coverity Scan](https://scan.coverity.com/) Static Analysis is used to find and fix defects in your Java, C/C++, C# or JavaScript open source project for free.

The Foundation Parent Pom doesn't have (yet) any dependency or integration with Coverity Scan; it's very simple to enable it [using .travis.yml](https://scan.coverity.com/travis_ci)

#### Enable Coverity Scan integration
1. Sign in [Coverity Scan](https://scan.coverity.com/dashboard) using Github
2. [Add a project](https://scan.coverity.com/projects)
3. Define a `COVERITY_SCAN_TOKEN=coverity_project_token`; you can find the token in the Coverity `Project Settings` page; as above, make sure to encrypt the variable if you're using Travis CI: `travis encrypt COVERITY_SCAN_TOKEN=coverity_project_token`

### Whitesource integration
[WhiteSource](https://www.whitesourcesoftware.com/) automatically identiﬁes all the open source components and dependencies in your build by constant and automatic cross-referencing of your open source components against WhiteSource’s deﬁnitive database of open source repositories.

Whitesource provides a dedicated instance to validate and enforce quality, security and legal compliance for all FINOS hosted projects.

#### Enable Whitesource

In order to access the Whitesource dashboard and configure your build to submit metrics remotely, you first need to be invited - via email - by the Foundation Staff; open an INFRA issue with title "Request access to WhiteSource", where you specify the email address you'd like to use to login.

When logged in, you'll have access to the product and project dashboards, that can be used to check and tweak alerts.

In Maven, you can configure the following Whitesource parameters:
- `-Dwhitesource.org.token` - specifies the Whitesource API Key to use, which represents the Whitesource Foundation account; Foundation Staff can configure this field on Travis CI or any other CI environment, but cannot redistribute the key in clear test. If present, the value will be taken from `WHITESOURCE_TOKEN` environment variable
- `-Dwhitesource.product` - specifies the Whitesource product that contains the currently built project (or repository, in Github terms); default value is `${project.artifactId}`
- `-Dwhitesource.checkPolicies` - whether to check the current build against WhiteSource policies; default value is `true`
- `-Dwhitesource.failOnError` - whether to trigger a `BUILD ERROR` if any policy violation is found; default value is `true`

## Workstation setup

### Travis

To launch the `travis` command locally, you must install the gem first: `gem install travis`

### Maven deploy/release

In order to publish (FINOS) artifacts, it is necessary to [sign in Sonatype OSS](https://issues.sonatype.org/secure/Signup!default.jspa) and [configure your workstation](http://central.sonatype.org/pages/apache-maven.html#distribution-management-and-authentication) (only the settings.xml part); we strongly advise to [encrypt your Maven passwords](https://maven.apache.org/guides/mini/guide-encryption.html).

You also need to install [GnuPG](https://www.gnupg.org); on OSX it is available on Homebrew with `brew install gnupg gnupg2`, although some issues were encountered using the GPG Maven Plugin. You can run `gpg2 -q --sign` to check your installation and validate your passphrase.

Before proceeding, please open a `TASK` issue on our INFRA Jira project, attaching the project name (github url) and your username on https://s01.oss.sonatype.org; we will ask Sonatype - on your behalf - to grant you access to publish artifacts using `org.finos` groupId.

### Github plugins configuration
To enable documentation publishing into github pages, you must enable the profile `-Ppublish-site`; to setup github credentials on your local workstation, follow the [Maven github plugins configuration](https://github.com/github/maven-plugins); bare in mind that `<github.global.server>github</github.global.server>` is already defined in this Parent Pom, but you can override it in your own pom, if you like

## Artifact Deployment

### Snapshot Deployment

Simply run
```
mvn clean deploy
```
This command will deploy the artifacts on [Sonatype OSS Snapshot Repository](https://s01.oss.sonatype.org/content/repositories/snapshots/org/finos/)

If you want to deploy site documentation, you can run
```
mvn site -Ppublish-site
```

### Releasing finos-parent-pom

## Prerequisites

Install GPG and generate a key pair following https://central.sonatype.org/publish/requirements/gpg/; store the passphrase, as you'll need it for the release, then export the private key using `gpg --armor --export-secret-key "User Name" | pbcopy`.

Note that the email address used in the key needs to have permission to publish under groupID org.finos. Email help@finos.org for support.

```
export GPG_TTY=$(tty)
mvn clean release:prepare release:perform -P release
```

This command will deploy the artifacts on Sonatype OSS Staging Repository and - by default - promote the artifacts to [Maven Central](http://search.maven.org); synchronisation happens once every day; the goals invoked during the `release:perform` phase are defined by `<release.goals>` property and is set by default to `deploy`.

During the build, the GPG plugin will request for your key passphrase; please refer to [`gpg2`](https://linux.die.net/man/1/gpg2) to check your keys and test your passphrase using `gpg2 -q --sign`.

Please note that all the Maven logic related with a release is wrapped into a `release` profile, which is used by the `maven-release-plugin` when using `release:perform` goal, using the [`releaseProfiles` property](http://maven.apache.org/maven-release/maven-release-plugin/perform-mojo.html#releaseProfiles).

## Contributing

1. Fork it (<https://github.com/finos-fdx/finos-parent-pom/fork>)
2. Create your feature branch (`git checkout -b feature/fooBar`)
3. Read our [contribution guidelines](.github/CONTRIBUTING.md) and [Community Code of Conduct](https://www.finos.org/code-of-conduct)
4. Commit your changes (`git commit -am 'Add some fooBar'`)
5. Push to the branch (`git push origin feature/fooBar`)
6. Create a new Pull Request

_NOTE:_ Commits and pull requests to FINOS repositories will only be accepted from those contributors with an active, executed Individual Contributor License Agreement (ICLA) with FINOS OR who are covered under an existing and active Corporate Contribution License Agreement (CCLA) executed with FINOS. Commits from individuals not covered under an ICLA or CCLA will be flagged and blocked by the FINOS Clabot tool. Please note that some CCLAs require individuals/employees to be explicitly named on the CCLA.

*Need an ICLA? Unsure if you are covered under an existing CCLA? Email [help@finos.org](mailto:help@finos.org)*

#### Known issues
If the release command fails with `gpg: signing failed: Inappropriate ioctl for device`, run `export GPG_TTY=$(tty)` and try again; more info [on stackexchange](http://unix.stackexchange.com/a/257065).

export GPG_TTY=$(tty)

If this doesn't solve it, you can `brew install pinentry-mac` and add `pinentry-program /usr/local/bin/pinentry-mac` to your `~/.gnupg/gpg-agent.conf`.

**Important!** - Do not use maven-release-plugin 3.0.0-M7, as it introduces an issue with GPG signing, see https://github.com/apache/maven-release/pull/125#issuecomment-1302613138

## Docker integration
Run `mvn install -Pdocker` to build a Docker image; you'll need to add a `Dockerfile` in the Maven module root folder, below an example:
```
FROM openjdk:13
MAINTAINER Maurizio Pillitu <maoo@finos.org>

ENTRYPOINT ["/usr/bin/java", "-jar", "/usr/share/myservice/myservice.jar"]

# Add Maven dependencies (not shaded into the artifact; Docker-cached)
ADD target/lib           /usr/share/myservice/lib

# Add the service itself
ARG JAR_FILE
ADD target/${JAR_FILE} /usr/share/myservice/myservice.jar
```

To push the image to `hub.docker.com/u/finos`, run `mvn deploy -Pdocker`; you must have `DOCKER_USERNAME` and `DOCKER_PASSWORD` environment variables set.

In order to disable the plugin on the parent POM, [check this GitHub Issue](https://github.com/spotify/dockerfile-maven/issues/87#issuecomment-338288165).

## License

The code in this repository is distributed under the [Apache License, Version 2.0](http://www.apache.org/licenses/LICENSE-2.0).

Copyright 2018-2019 FINOS - The Fintech Open Source Foundation
