Ozymandias: Collection of maven plugins, extensions and other generally useful artifacts
========================================================================================

This is an umbrella project that unifies several artifacts.
Every artifact provides some potentially useful function for maven build.

This package is used to share building tools for other projects.

Child projects
--------------

 * [mustache-maven-plugin](https://github.com/sviperll/ozymandias/tree/master/mustache-maven-plugin)

   Render mustache templates during maven build using data from some json- or .properties-file.

 * [versioning-maven-plugin](https://github.com/sviperll/ozymandias/tree/master/versioning-maven-plugin)

   Support consistent versioning policy. Store publicly available stable and unstable version numbers in .properties-file.

 * [maven-profiledep-extension](https://github.com/sviperll/ozymandias/tree/master/maven-profiledep-extension)

   Tool (maven core-extension) used to modularize build by spliting build configuration into set of inter-dependent profiles.

 * [coreext-maven-plugin](https://github.com/sviperll/ozymandias/tree/master/coreext-maven-plugin)

   Manages `.mvn/extensions.xml` file introduced in maven 3.3.1.
   Checks if all required core maven extensions are installed and have required
   version numbers.

License
-------

Ozymandias is under BSD 3-clause license.

Flattr
------

[![Flattr this git repo](http://api.flattr.com/button/flattr-badge-large.png)](https://flattr.com/submit/auto?user_id=sviperll&url=https%3A%2F%2Fgithub.com%2Fsviperll%2Fozymandias&title=ozymandias&language=Java&tags=github&category=software)
