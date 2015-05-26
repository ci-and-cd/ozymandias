maven-parent: Generally usefull maven parent artifact
=====================================================

Part of [ozymandias](https://github.com/sviperll/ozymandias).

Usage
-----

Set `maven-parent` as parent project and activate required profiles with `activateparentprofiles` property.
Profile activation is implemented with [maven-profiledep-extension](https://github.com/sviperll/ozymandias/tree/master/maven-profiledep-extension)

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>com.github.sviperll</groupId>
        <artifactId>maven-parent</artifactId>
        <version>0.6</version>
    </parent>
    <groupId>group</groupId>
    <artifactId>myartifact</artifactId>
    <version>version</version>
    <!-- ... -->
    <properties>
        <activateparentprofiles>java6,nexus-deploy</activateparentprofiles>
        <!-- ... -->
    </properties>
    <!-- ... -->
</project>
````

Profiles
--------

### java6 ###

Use version 1.6 of java language and runtime

### java7 ###

Use version 1.7 of java language and runtime

### java8 ###

Use version 1.7 of java language and runtime

### bootclasspath ###

Fork javac and specify bootclasspath compilation argument using
JDK6_HOME, JDK7_HOME or JDK8_HOME environment variable

### nexus-deploy ###

Configure nexus-staging-maven-plugin to run instead of standard deploy-plugin
to deploy artifact to running nexus instance

### executable ###

Add main class and classpath to jar manifest and create archives with
jar file and it's dependencies

### no-deploy-to-maven-repository ###

Do not deploy artifacts to maven repository.
No plugins are executed on deploy phase.