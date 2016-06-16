# Symphony Software Foundation Parent POM

This Maven POM aims to provide common functionalities to Maven projects hosted by the Symphony Software Foundation.

## Features
- Freeze most common Maven plugin versions using pluginManagement
- Blacklist incompatible licenses of transitive Maven dependencies
- Publish released artifacts on Github download page
- Publish Maven site on Github Wiki

## Maven Central Deployment configuration

Please follow [instructions on Sonatype.org](http://central.sonatype.org/pages/apache-maven.html)

## Github plugins configuration

To enable github publishing features (downloads and site), you must enable the profile `-Pgithub-release`; to setup github credentials on your local workstation, follow the [Maven github plugins configuration](https://github.com/github/maven-plugins)

## Performing a deployment/release

To deploy a nightly build run `mvn clean deploy`

To promote the latest deployment to a release run `mvn nexus-staging:release`

To reject last nightly built deployment run `mvn nexus-staging:drop`

More info on [Sonatype.org](http://central.sonatype.org/pages/apache-maven.html)
