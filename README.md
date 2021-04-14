![Maven Central](https://img.shields.io/maven-central/v/it.tidalwave.superpom/superpom.svg)
[![Build Status](https://img.shields.io/jenkins/s/http/services.tidalwave.it/ci/job/_Build_from_Scratch.svg)](http://services.tidalwave.it/ci/view/)
[![Test Status](https://img.shields.io/jenkins/t/http/services.tidalwave.it/ci/job/.svg)](http://services.tidalwave.it/ci/view/)
[![Coverage](https://img.shields.io/jenkins/c/http/services.tidalwave.it/ci/job/.svg)](http://services.tidalwave.it/ci/view/)

Tidalwave - SuperPOM
================================

[![Build
Status](https://drone.io/bitbucket.org/tidalwave/tidalwave-superpom-src/status.png)](https://drone.io/bitbucket.org/tidalwave/tidalwave-superpom-src/latest)

The super POM for all Tidalwave projects. It is not designed for being used by others, as it contains some corporate-specific configurations, but its
ancestor [TheseFooolishThings SuperPOM](http://bitbucket.org/tidalwave/thesefoolishthings-superpom-src) has been designed to be reusable. Please have a
look at it.

This super POM adds to its ancestor:

+ some Tidalwave variables that refers to the issue tracker, continuous integration system, etc...;
+ the definitions of versions of a number of commonly used libraries and their dependency management:
    * [AspectJ](https://www.eclipse.org/aspectj)
    * [Hamcrest Matchers](http://hamcrest.org/JavaHamcrest)
    * [JSR 330](https://github.com/google/guice/wiki/JSR330)
    * [Jakarta XML Binding (JAXB)](https://eclipse-ee4j.github.io/jaxb-ri/)
    * [Spotbugs annotations](https://spotbugs.readthedocs.io)
    * [JUnit](https://junit.org/junit5)
    * [Logback](http://logback.qos.ch)
    * [Lombok](https://projectlombok.org)
    * [SLF4J](slf4j.org)
    * [Spring 5](https://spring.io/projects/spring-framework)
    * [TestNG](https://testng.org)
+ the definition for Tidalwave 3rd party repository (stuff that is not available on Maven Central);
+ a profile for using the [TheseFoolishThings](http://tidalwave.it/projects/thesefoolishthings) Event Bus (```it.tidalwave-spring-messagebus-v1```);
+ profiles for the [Mycila License plugin](https://github.com/mycila/license-maven-plugin);
+ configuration of the UMLGraphDoc maven plugin;
+ the configuration for the TheseFoolishThings TestNG listener (which provides enhanced test logging);
+ definitions of some custom javadoc tags;
+ a blacklist for some old artifacts;
+ some other minor customisations.

To learn more about the things of this project, please have a look at its site - http://tidalwave.it/projects - where more documentation, javadoc
and build reports are provided.


Bootstrapping
-------------

In order to build the project, run from the command line:

```mvn -DskipTests```

The project can be opened and built by a recent version of the NetBeans, Eclipse or Idea IDEs.


Documentation
-------------

More information can be found on the [homepage](http://tidalwave.it/projects) of the project.


Contributing
------------

We accept pull requests via Bitbucket or GitHub.

There are some guidelines which will make applying pull requests easier for us:

* No tabs! Please use spaces for indentation.
* Respect the code style.
* Create minimal diffs - disable on save actions like reformat source code or organize imports. If you feel the source
  ode should be reformatted create a separate PR for this change.
* Provide TestNG tests for your changes and make sure your changes don't break any existing tests by running
```mvn clean test```.

If you plan to contribute on a regular basis, please consider filing a contributor license agreement. Contact us for
 more information


License
-------

Code is released under the [Apache Licence v2](https://www.apache.org/licenses/LICENSE-2.0.txt).


Additional Resources
--------------------

* [Tidalwave Homepage](http://tidalwave.it)
