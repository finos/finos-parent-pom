[![Maven Central](https://img.shields.io/maven-central/v/org.symphonyoss/symphonyoss.svg?maxAge=2592000)](http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22symphonyoss%22)
[![Dependencies](https://www.versioneye.com/user/projects/5770ea4001f8e1000998d503/badge.svg?style=flat-square)](https://www.versioneye.com/user/projects/5770ea4001f8e1000998d503)
[![Build Status](https://travis-ci.org/symphonyoss/ssf-parent-pom.svg)](https://travis-ci.org/symphonyoss/ssf-parent-pom)

# Symphony Software Foundation Parent POM
This Maven POM aims to provide **_common functionalities to Maven projects_** hosted by the Symphony Software Foundation.

* [Usage](#usage)
* [Features](#features)
  * [Plugin Management](#plugin-management)
  * [Enforcing rules](#enforcing-rules)
  * [Attach sources](#attach-sources)
  * [versioneye\.com Integration](#versioneyecom-integration)
    * [Enable Versioneye integration](#enable-versioneye-integration)
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

## Usage
Simply define the following `<parent>` in your `pom.xml`:
```
<parent>
  <groupId>org.symphonyoss</groupId>
  <artifactId>symphonyoss</artifactId>
  <version>8</version>
</parent>
```

If you want to use a SNAPSHOT version (ie `8-SNAPSHOT`), make sure to add the following repository in your `pom.xml`.

```
<repositories>
  <repository>
    <id>sonatype-oss-public</id>
    <url>https://oss.sonatype.org/content/groups/public/</url>
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
- `maven-remote-resources-plugin` includes the contents of [ssf-jar-resource-bundle](https://github.com/symphonyoss/ssf-jar-resource-bundle) (mostly licensing-related files) in each artifact that gets built
- `maven-source-plugin` generates a JAR with the source code (which gets deployed and released along with other artifacts) during each JAR project build
- `nexus-staging-maven-plugin` enables the deployment and release process against [Sonatype OSS Repository](https://oss.sonatype.org) (which is synced with Maven Central)

### Enforcing rules
The current rules currently defined are:

1. Use Maven 3.0 or greater
2. Define a groupId that starts with `org.symphonyoss` or `org.symphonyoss.`

The Foundation rules are implemented as Maven Enforcer custom rules and the [source code is available on GitHub](https://github.com/symphonyoss/ssf-enforcer-rules); more rules will be added in the future.

### Attach sources
By enabling the `-Pattach-sources` profile, the `maven-source-plugin` gets invoked and a `JAR` artifact containing the project's source code is created.

When enabled, the `maven-install-plugin`, `maven-deploy-plugin` and `maven-release-plugin` will take the source code JAR file in consideration and including in the respective plugin lifecycles.

### versioneye.com Integration
[versioneye.com](http://versioneye.com) notifies you about out-dated dependencies, security vulnerabilities and license violations in your Git repositories.

This Parent Pom uses and configures the [Versioneye Maven Plugin](https://github.com/versioneye/versioneye_maven_plugin) to register and submit data to versioneye.

#### Enable Versioneye integration
1. Define a `VERSIONEYE_API_KEY=my_versioneye_api_key`; you can find the API Key in your [versioneye settings](https://www.versioneye.com/settings/api); if you use Travis CI, make sure you encrypt it using `travis encrypt VERSIONEYE_API_KEY=my_versioneye_api_key` and then pushing the `secure` token in your `.travis.yml` (check this [.travis.yml](.travis.yml) as example)
2. Add `versioneye` profile to your Maven command: `mvn clean verify -Pversioneye`; please note that the Maven will run versioneye during the `verify` phase (that is after package), therefore `mvn clean package -Pversioneye` won't work!

#### Additional configuration
You can tweak the following properties:
- `versioneye.trackPlugins` : whether to track Maven plugin, defaults to `false`
- `versioneye.skipScopes` : comma-separated list of Maven dependency scopes that must be ignored by versioneye; defaults to `test,provided`
- `versioneye.propertiesPath` : located `versioneye.properties` file and defaults to `./src/main/resources/versioneye.properties`

### sonarqube.com Integration
The [SonarQube®](https://sonarqube.com/) platform is an open source quality management platform, dedicated to continuously analyzing and measuring the technical quality of source code, from project portfolio down to the method level, and tracking the introduction of new Bugs, Vulnerabilities, and Code Smells in the Leak Period.

This Parent Pom uses and configures the [Sonar Maven Plugin](http://sonarsource.github.io/sonar-maven/) to submit data to SonarQube.

#### Enable SonarQube integration
1. Request an invitation (send email to `nemo AT sonarsource.com`) to Sonarqube.com for the project, mentioning that the project belongs to the Symphony Software Foundation
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

Whitesource provides a dedicated instance to validate and enforce quality, security and legal compliance for all Symphony Software Foundation hosted projects.

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

In order to publish (Symphony Foundation) artifacts, it is necessary to [sign in Sonatype OSS](https://issues.sonatype.org/secure/Signup!default.jspa) and [configure your workstation](http://central.sonatype.org/pages/apache-maven.html#distribution-management-and-authentication) (only the settings.xml part); we strongly advise to [encrypt your Maven passwords](https://maven.apache.org/guides/mini/guide-encryption.html).

You also need to install [GnuPG](https://www.gnupg.org); on OSX it is available on Homebrew with `brew install gnupg gnupg2`, although some issues were encountered using the GPG Maven Plugin. You can run `gpg2 -q --sign` to check your installation and validate your passphrase.

Before proceeding, please open a `TASK` issue on our INFRA Jira project, attaching the project name (github url) and your username on oss.sonatype.org; we will ask Sonatype - on your behalf - to grant you access to publish artifacts using `org.symphonyoss` groupId.

### Github plugins configuration
To enable documentation publishing into github pages, you must enable the profile `-Ppublish-site`; to setup github credentials on your local workstation, follow the [Maven github plugins configuration](https://github.com/github/maven-plugins); bare in mind that `<github.global.server>github</github.global.server>` is already defined in this Parent Pom, but you can override it in your own pom, if you like

## Artifact Deployment

### Snapshot Deployment

Simply run
```
mvn clean deploy
```
This command will deploy the artifacts on [Sonatype OSS Snapshot Repository](https://oss.sonatype.org/content/repositories/snapshots/org/symphonyoss/)

If you want to deploy site documentation, you can run
```
mvn site -Ppublish-site
```

### Release Deployment
```
export GPG_TTY=$(tty)
mvn clean release:prepare release:perform -Prelease
```

This command will deploy the artifacts on Sonatype OSS Staging Repository and - by default - promote the artifacts to [Maven Central](http://search.maven.org); synchronisation happens once every day; the goals invoked during the `release:perform` phase are defined by `<release.goals>` property and is set by default to `deploy`.

During the build, the GPG plugin will request for your key passphrase; please refer to [`gpg2`](https://linux.die.net/man/1/gpg2) to check your keys and test your passphrase using `gpg2 -q --sign`.

Please note that all the Maven logic related with a release is wrapped into a `release` profile, which is used by the `maven-release-plugin` when using `release:perform` goal, using the [`releaseProfiles` property](http://maven.apache.org/maven-release/maven-release-plugin/perform-mojo.html#releaseProfiles).

#### Known issues
If the release command fails with `gpg: signing failed: Inappropriate ioctl for device`, run `export GPG_TTY=$(tty)` and try again; more info [on stackexchange](http://unix.stackexchange.com/a/257065).

export GPG_TTY=$(tty)