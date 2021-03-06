EasyMock
========

EasyMock is a Java library that provides an easy way to use Mock Objects in unit testing.

You can find the website and user documentation at http://easymock.org.

Developer information
=====================

Travis status
-------------
[![Build Status](https://travis-ci.org/easymock/easymock.svg?branch=master)](https://travis-ci.org/easymock/easymock)

Environment setup
-----------------

I'm using:
- Eclipse 4.3.2 (Kepler Service Release 2)
- Maven 3.2.3
- m2e with the following connectors:
  - buildhelper (for build-helper-maven-plugin)
- IntelliJ 13.1.4 (instead of Eclipse)

To configure your local workspace:
- Import the Maven parent project to Eclipse or IntelliJ
- Import the Eclipse formatting file `EasyMock-formatter.xml` (usable in Eclipse or IntelliJ)

To build EasyMock with Maven
----------------------------

There are three different levels of build.

### Build without any active profile

It is a basic compilation of the application.

`mvn install`

### Full build

This build will check code coverage using Jacoco, run findbugs and
validate that the license headers are correctly set.

`mvn install -PfullBuild`

### Deploy build

This is the build to launch to deploy to the surefire repository. It assembles the application and add
the gpg checksum. You will usually launch it on top of the full build.

The command line will ask you to give the passphrase for the gpg private key.

`mvn install -PdeployBuild`

To compile EasyMock in Eclipse
-----------------------------
- Install m2e
- Import the EasyMock Maven parent project to your Eclipse workspace

To compile EasyMock in IntelliJ
-----------------------------
- Import the EasyMock Maven parent project as an New IntelliJ project

To update the versions
----------------------
- `mvn versions:set -DnewVersion=X.Y -Pandroid,bench`
- `mvn versions:commit -Pandroid,bench` if everything is ok, `mvn versions:revert -Pandroid,bench` otherwise

Configure to deploy to the Sonatype maven repository
----------------------------------------------------
- You will first need to add something like this to your settings.xml
```xml
<servers>
  <server>
    <id>sonatype-nexus-snapshots</id>
    <username>sonatypeuser</username>
    <password>sonatypepassword</password>
  </server>
  <server>
    <id>sonatype-nexus-staging</id>
    <username>sonatypeuser</username>
    <password>sonatypepassword</password>
  </server>
</servers>
```
- Then follow the instructions from the site below to create your key to sign the deployed items

http://www.sonatype.com/people/2010/01/how-to-generate-pgp-signatures-with-maven/

To build the maven site (with findbugs, checkstyle, jdepends and JavaNCSS reports)
--------------------------------------------------------------------------------------
- You will to give enough memory to maven with 'set MAVEN_OPTS=-Xmx512m' (or setting it as environment variable)
- Then type `mvn site`

To check dependencies and plugins versions
--------------------------------------------------------------------------------------
`mvn versions:display-dependency-updates versions:display-plugin-updates -Pall`

To download the sources associated to our dependencies
--------------------------------------------------------------------------------------
`mvn dependency:resolve -Dclassifier=sources`

To update the license
--------------------------------------------------------------------------------------
`mvn validate license:format -Pall`

Android
--------------------------------------------------------------------------------------
- Install the Android SDK
- Configure a device (real or simulated)
- Add an `ANDROID_HOME` to target the Android SDK
- Add `$ANDROID_HOME/platform-tools` to your path 
- Activate the debug mode if it's a real device
- `mvn install -Pandroid`

To bundle EasyMock and deploy
--------------------------------------------------------------------------------------
- Update "easymock/ReleaseNotes.txt"
  - Add a little speech on the features
  - Retrieve the release notes in the textual format (http://jira.codehaus.org/browse/EASYMOCK#selectedTab=com.atlassian.jira.plugin.system.project%3Aroadmap-panel)
- In local:
```bash
mvn versions:set -DnewVersion=x.y -Pall
mvn versions:commit -Pall
git commit -am "Move to version x.y"
mvn -T 8.0C clean deploy -PfullBuild,deployBuild -Dgpg.passphrase=xxxx
```
- Close the deployment at Sonatype Nexus UI (https://oss.sonatype.org/index.html#stagingRepositories)
  More details on the deployment rules here: https://docs.sonatype.org/display/Repository/Sonatype+OSS+Maven+Repository+Usage+Guide
- Add the staging repository url found in Nexus to easymock-test-deploy
```xml
<repositories>
  <repository>
    <id>sonatype-nexus-staging</id>
    <name>Nexus Release Repository</name>
    <url>https://oss.sonatype.org/content/repositories/orgeasymock-213/</url>
    <releases>
      <enabled>true</enabled>
    </releases>
    <snapshots>
      <enabled>false</enabled>
    </snapshots>
  </repository>
</repositories>
```
- Release the repository. It will be synced with Maven Central Repository
- Go to the File Manager on the EasyMock SF project (https://sourceforge.net/projects/easymock/files/?source=navbar)
  - In EasyMock, create a folder named "x.y"
  - Upload "easymock/target/easymock-x.y-bundle.zip" and "easymock/ReleaseNotes.txt" in the newly created directory
  - Show the detailed information and rename it to remove the "-bundle" at the end
  - Show the detailed information and rename it to readme.txt
  - Repeat these step for EasyMock Class Extension
- Release the Jira version (http://jira.codehaus.org/plugins/servlet/project-config/EASYMOCK/versions)
- Announce to easymock@yahoogroups.com, twitter, blog

Deploy the website
--------------------------------------------------------------------------------------
- In local:
  - Go to the EasyMock root directory
  - Make sure the website directory is clean
  - `./deploy-website.sh`

Start next version
--------------------------------------------------------------------------------------
In local:
```bash
mvn versions:set -DnewVersion=X.Z-SNAPSHOT -Pall
mvn versions:commit -Pall
```
- Create next version in Jira (http://jira.codehaus.org/plugins/servlet/project-config/EASYMOCK/versions)
