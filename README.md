[!Maven Central](https://img.shields.io/maven-central/v/org.symphonyoss/symphonyoss.svg?maxAge=2592000)](http://search.maven.org/#artifactdetails%7Corg.symphonyoss%7Csymphonyoss%7C2%7Cpom)
[![Dependencies](https://www.versioneye.com/user/projects/5770ea4001f8e1000998d503/badge.svg?style=flat-square)](https://www.versioneye.com/user/projects/5770ea4001f8e1000998d503)
[![Build Status](https://travis-ci.org/symphonyoss/ssf-parent-pom.svg)](https://travis-ci.org/symphonyoss/ssf-parent-pom)

# Symphony Software Foundation Parent POM
This Maven POM aims to provide **_common functionalities to Maven projects_** hosted by the Symphony Software Foundation.

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
- **Plugin Management** for the most common Maven functionalities
- **Simple artifact deployment and release** on Maven Central and Github (`releases` project page)
- **Site (documentation) generation** on Github Wiki (WIP)
- **Enforcing rules** to validate the Foundation code acceptance criteria
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

### Artifact deployment and release
Before trying, make sure you read the [setup instructions](#Workstation setup)

#### Deploy
To deploy a nightly build run `mvn clean deploy`
To reject last nightly built deployment run `mvn nexus-staging:drop`

#### Release
To promote the latest deployment to a release run `mvn nexus-staging:release`
More info on [Sonatype.org](http://central.sonatype.org/pages/apache-maven.html)

### Enforcing rules
The current rules currently defined are:

1. Use Maven 3.0 or greater
2. Define a groupId that starts with `org.symphonyoss` or `org.symphonyoss.`

The Foundation rules are implemented as Maven Enforcer custom rules and the [source code is available on GitHub](https://github.com/symphonyoss/ssf-enforcer-rules); more rules will be added in the future.

### versioneye.com Integration
[versioneye.com](http://versioneye.com) notifies you about out-dated dependencies, security vulnerabilities and license violations in your Git repositories.

This Parent Pom uses and configures the [Versioneye Maven Plugin](https://github.com/versioneye/versioneye_maven_plugin) to register and submit data to versioneye.

#### Enable Versioneye integration
1. Define a `VERSIONEYE_API_KEY=my_versioneye_api_key`; you can find the API Key in your [versioneye settings](https://www.versioneye.com/settings/api); if you use Travis CI, make sure you encrypt it using `travis encrypt VERSIONEYE_API_KEY=my_versioneye_api_key` and then pushing the `secure` token in your `.travis.yml` (check this [.travis.yml](.travis.yml) as example)
2. Add `versioneye` profile to your Maven command: `mvn clean verify -Pversioneye`; please note that the Maven will run versioneye during the `verify` phase (that is after package), therefore `mvn clean package -Pversioneye` won't work!

### sonarqube.com Integration
The [SonarQube®](https://sonarqube.com/) platform is an open source quality management platform, dedicated to continuously analyzing and measuring the technical quality of source code, from project portfolio down to the method level, and tracking the introduction of new Bugs, Vulnerabilities, and Code Smells in the Leak Period.

This Parent Pom uses and configures the [Sonar Maven Plugin](http://sonarsource.github.io/sonar-maven/) to submit data to SonarQube.

#### Enable SonarQube integration
1. Request an invitation (send email to `nemo AT sonarsource.com`) to Sonarqube.com for the project, mentioning that the project belongs to the Symphony Software Foundation
2. Define a `SONAR_LOGIN=my_sonarqube_token`; you can find the token in your [Sonar security settings](https://sonarqube.com/account/security); as above, make sure to encrypt the variable if you're using Travis CI: `travis encrypt SONAR_LOGIN=my_sonar_token`
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

## Workstation setup

### Travis

To launch the `travis` command locally, you must install the gem first: `gem install travis`

### Maven deploy/release

In order to publish (Symphony Foundation) artifacts, it is necessary to [sign in Sonatype OSS](https://issues.sonatype.org/secure/Signup!default.jspa) and [configure your workstation](http://central.sonatype.org/pages/apache-maven.html#distribution-management-and-authentication) (only the settings.xml part); we strongly advise to [encrypt your Maven passwords](https://maven.apache.org/guides/mini/guide-encryption.html).

You also need to install [GnuPG](https://www.gnupg.org); on OSX it is available on Homebrew with `brew install gnupg gnupg2`, although some issues were encountered using the GPG Maven Plugin. You can run `gpg2 -q --sign` to check your installation and validate your passphrase.

Before proceeding, please open a `TASK` issue on our INFRA Jira project, attaching the project name (github url) and your username on oss.sonatype.org; we will ask Sonatype - on your behalf - to grant you access to publish artifacts using `org.symphonyoss` groupId.

### Github plugins configuration
To enable github publishing features (releases and documentation site), you must enable the profile `-Pgithub-release`; to setup github credentials on your local workstation, follow the [Maven github plugins configuration](https://github.com/github/maven-plugins)
_This is work in progress; it needs testing_

## Artifact Deployment

### Snapshot Deployment

Simply run
```
mvn clean deploy
```
This command will deploy the artifacts on [Sonatype OSS Snapshot Repository](https://oss.sonatype.org/content/repositories/snapshots/org/symphonyoss/)

### Release Deployment
```
mvn clean release:prepare release:perform -Dgpg.passphrase=your_passphrase
```
This command will deploy the artifacts on Sonatype OSS Staging Repository and - by default - promote the artifacts to [Maven Central](http://search.maven.org); synchronisation happens once every day.

`your_passphrase` is the passphrase of your GPG key; please refer to `gpg2` to check your keys;  simply type `gpg2 -q --sign` to validate your passphrase
