versioning-maven-plugin: Maven plugin to support versioning policy
==================================================================

Part of [ozymandias](https://github.com/sviperll/ozymandias).

Prompt goal of the plugin shows two levels of menus to choose version number.
The idea is to rule out typos from version numbers and make it easy to support consistent versioning policy.

Goals
-----

 * *versioning:prompt* prompt user to choose new version number
 * *versioning:classify* parse version number and set kind parameter to one of `alpha`, `beta`, `rc`, `final`, `other`.
 * *versioning:update-file* update given file, write `version.stable` and `version.unstable` properties to it, based on given version

Usage
-----

You can set up your build using some property to pass version number from versioning-plugin to release-plugin and use following
command to prepare release.

```
$ mvn versioning:prompt release:prepare
```

Configuration example
---------------------

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    ...
    <build>
        ...
        <plugins>
            ...
            <plugin>
                <groupId>com.github.sviperll</groupId>
                <artifactId>versioning-maven-plugin</artifactId>
                <version>0.22</version>
                <inherited>false</inherited>
                <configuration>
                    <!-- Version to use. ${project.version} is default. -->
                    <version>${project.version}</version>

                    <!--
                      -- Property to set as a result of prompt goal.
                      -- Decided version is based on version configured above.
                      -->
                    <decidedVersionPropertyName>release.version</decidedVersionPropertyName>

                    <!-- Property to set as a result of classify goal. -->
                    <versionKindPropertyName>project.version.kind</versionKindPropertyName>

                    <!-- File to update by update-file goal -->
                    <versionFile>
                        <!-- File encoding -->
                        <encoding>UTF-8</encoding>

                        <!-- Path to file -->
                        <file>version.properties</file>

                        <!-- Policy used to set version.stable and version.unstable properties in this file -->
                        <stability>
                            <!-- One of stable, unstable or none -->
                            <defaultStability>unstable</defaultStability>

                            <!-- Final versions are considered stable -->
                            <stableKinds>
                                <stableKind>final</stableKind>
                            </stableKinds>

                            <!-- Alpha, beta and rc versions are considered unstable -->
                            <unstableKinds>
                                <unstableKind>alpha</unstableKind>
                                <unstableKind>beta</unstableKind>
                                <unstableKind>rc</unstableKind>
                            </unstableKinds>
                        </stability>
                    </versionFile>
                </configuration>
            </plugin>
            ...
        </plugins>
        ...
    </build>
    ...
</project>

```

With the above configuration `prompt` goal execution

 * sets `release.version` property to selected version, for example `1.2-rc3`
 * sets `release.version.kind` property to one of `alpha`, `beta`, `rc`, `final`, `other` (`rc` in above example)

`classify` goal execution sets `project.version.kind` property to one of `alpha`, `beta`, `rc`, `final`, `other`

`update-file` goal overwrites `version.properties` file.
`version.stable` and `version.unstable` properties are replaced in this file according to following rules:

 * when current version is final, `version.stable` will be set to it, `version.unstable` property will be removed.
 * when current version is alpha, beta or rc, `version.unstable` will be set to it, `version.stable` will be left intact.

Different set of suffixes
-------------------------

You can customize used suffixes and use your own custom suffixes instead of `alpha`, `beta` and `rc`

In the simpliest case you can add `versionOrder` directive into plugin configuration.
For example, you can use `Alpha`, `Beta` and `CR` suffixes instead of `alpha`, `beta` and `rc` like this:

````xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    ...
    <build>
        ...
        <plugins>
            ...
            <plugin>
                <groupId>com.github.sviperll</groupId>
                <artifactId>versioning-maven-plugin</artifactId>
                <configuration>
                    ...
                    <versionOrder>Alpha,Beta,CR</versionOrder>
                    ...
                </configuration>
            </plugin>
            ...
        </plugins>
        ...
    </build>
    ...
</project>
````

With `versionOrder` directive you can also configure wheather final version should have some suffix at the end
(like `5.0.3-Final`) or it should be bare suffixless version string (like `5.0.3`).
To add suffix to final version, you can just list it as a last component of `versionOrder` directive:

````xml
                    <versionOrder>Alpha,Beta,RC,Final</versionOrder>
````

To leave suffix out for final versions, you leave final suffix out of `versionOrder` directive:

````xml
                    <versionOrder>Alpha,Beta,RC</versionOrder>
````

To make above `versionOrder` directive work the plugin needs some information about
wich suffixes are final and wich are not.
Besides, the plugin supports aliases for suffixes.
For example, `a` suffix is treated like an alias for `alpha` suffix, by default.
You can define custom sets of aliases with `suffixes` and `finalVersionSuffix` directive.
Here is the default configuration:

````xml
                    <suffixes>
                        <suffix>
                            <variants>a,alpha,Alpha</variants>
                        </suffix>
                        <suffix>
                            <variants>b,beta,Beta</variants>
                        </suffix>
                        <suffix>
                            <description>release candidate</description>
                            <variants>rc,RC,CR</variants>
                        </suffix>
                    </suffixes>
                    <finalVersionSuffix>
                        <variants>final,Final,GA</variants>
                    </finalVersionSuffix>
                    <versionOrder>alpha,beta,rc</versionOrder>
````

Aliases listed in `versionOrder` directive are considered to be prefered aliases and
are used in version strings generated by the plugin.
Other aliases are used to define correct order between user supplied version strings.

Combining with release plugin
-----------------------------

You can combine versioning-plugin with release-plugin to use following command to start release preparation:

````
$ mvn versioning:prompt release:prepare
````

As a result, you will interactively decide which version you are going to release.
`version.properties` file is modified as part of preparation step.
`version.properties` file and possibly other files dependent on it are checked-in (commit) as a last step
of release preparation.

Below is the full configuration used to achieve described behavior.

````xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
    <!-- ... -->
    <build>
        <!-- ... -->
        <plugins>
            <!-- ... -->
            <plugin>
                <groupId>com.github.sviperll</groupId>
                <artifactId>versioning-maven-plugin</artifactId>
                <version>0.22</version>
                <configuration>
                    <version>${project.version}</version>
                    <decidedVersionPropertyName>release.version</decidedVersionPropertyName>
                    <versionFile>
                        <file>version.properties</file>
                        <stability>
                            <defaultStability>unstable</defaultStability>
                            <stableKinds>
                                <stableKind>final</stableKind>
                            </stableKinds>
                        </stability>
                    </versionFile>
                </configuration>
            </plugin>
            <!-- ... -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-release-plugin</artifactId>
                <version>2.5.1</version>
                <configuration>
                    <releaseVersion>${release.version}</releaseVersion>
                    <developmentVersion>${release.version}-successor-SNAPSHOT</developmentVersion>
                    <preparationGoals>-DdoReleasePreparation=true clean versioning:update-file verify scm:checkin</preparationGoals>
                </configuration>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-scm-plugin</artifactId>
                <version>1.9.2</version>
            </plugin>
        </plugins>
        <!-- ... -->
    </build>
    <!-- ... -->
    <profiles>
        <!-- ... -->
        <profile>
            <id>release-preparation</id>
            <activation>
                <property>
                    <name>doReleasePreparation</name>
                </property>
            </activation>
            <build>
                <plugins>
                    <plugin>
                        <groupId>org.apache.maven.plugins</groupId>
                        <artifactId>maven-scm-plugin</artifactId>
                        <executions>
                            <!--
                              -- Commit all files dependent on version.stable and version.unstable properties
                              -- from version.properties file
                              -->
                            <execution>
                                <id>default-cli</id>
                                <configuration>
                                    <includes>version.properties</includes>
                                    <message>[maven-release-plugin] update release version information</message>
                                </configuration>
                            </execution>
                        </executions>
                    </plugin>
                </plugins>
            </build>
        </profile>
        <!-- ... -->
    </profiles>
    <!-- ... -->
</project>

````
