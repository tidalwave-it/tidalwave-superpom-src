Documentation
-------------

* [Using the SuperPOM](#using-the-superpom)
* [What this SuperPOM adds](#what-this-superpom-adds)
* [Project metadata](#project-metadata)
* [Repositories](#repositories)
* [Managed dependencies](#managed-dependencies)
* [Dependency rules](#dependency-rules)
* [Tests](#tests)
* [Javadoc](#javadoc)
* [License headers](#license-headers)
* [Site](#site)
* [XML binding (JAXB)](#xml-binding-jaxb)
* [Profiles](#profiles)
* [Spring Message Bus](#spring-message-bus)
* [Dependency graphs](#dependency-graphs)
* [Building and releasing this project](#building-and-releasing-this-project)
* [Properties reference](#properties-reference)
* [Properties you must provide](#properties-you-must-provide)
* [Unused properties](#unused-properties)


### Using the SuperPOM

Declare it as the parent of your project:

```xml
<parent>
    <groupId>it.tidalwave.superpom</groupId>
    <artifactId>superpom</artifactId>
    <version>-- version --</version>
    <relativePath />
</parent>
```

This SuperPOM extends the [TheseFoolishThings SuperPOM](https://bitbucket.org/tidalwave/thesefoolishthings-superpom-src)
(`it.tidalwave.thesefoolishthings:superpom`) and contains configurations specific to Tidalwave
projects. Everything described in the
[TheseFoolishThings SuperPOM manual](https://bitbucket.org/tidalwave/thesefoolishthings-superpom-src/src/master/MANUAL.md)
— the default build, compiler, tests, quality tools, profiles, the release cycle, the `tft.*` properties — applies
unchanged, unless this document says otherwise. This manual only describes what is added or changed.

As with the parent, you should override the project metadata and the SCM coordinates (`scm.repo.url`,
`scm.repo.readonly.url`, `scm.repo.browse.url`), and set the Tidalwave properties described in
[Project metadata](#project-metadata).


### What this SuperPOM adds

* **Project metadata**: Jira issue management, Jenkins CI management and an explicit SCM section, driven by the
  `tidalwave.*` properties.
* **Repositories**: the Sonatype release repository and the Tidalwave third-party repository.
* **Dependency management** for a set of commonly used libraries (logging, injection, annotations, Lombok, testing),
  whose versions are exposed as `tft.*.version` properties.
* **Enforcer rules**: dependency convergence and a list of banned dependencies.
* **Tests**: the TheseFoolishThings `TestLogger` is registered as a TestNG listener.
* **Javadoc**: custom tags to mark the status of an API (`stable`, `draft`, `experimental`), stereotypes and hidden
  types.
* **License headers** managed with the [Mycila License Maven Plugin](https://github.com/mycila/license-maven-plugin),
  with three dedicated profiles.
* **Site**: the [Maven Fluido Skin](https://maven.apache.org/skins/maven-fluido-skin/) is made available to the Site
  Plugin.
* **JAXB**: the JAXB2 Maven Plugin is patched to use Jakarta XML Binding.
* **Profiles** for the TheseFoolishThings Spring Message Bus and for dependency graphs.

None of these additions binds new goals to the lifecycle, with the exception of the enforcer rules: a plain
`mvn install` behaves as with the parent, plus the extra checks.


### Project metadata

The following sections of the POM are driven by properties, which are empty by default and should be set by each
project:

| Property                        | Used for                                                                                                                  |
|---------------------------------|---------------------------------------------------------------------------------------------------------------------------|
| `tidalwave.issues`              | Jira project key: `<issueManagement>` points to `http://services.tidalwave.it/jira/browse/${tidalwave.issues}`            |
| `tidalwave.ci.view`             | Jenkins view: `<ciManagement>` points to `http://services.tidalwave.it/ci/view/${tidalwave.ci.view}`                      |
| `tidalwave.github.readonly.url` | URL of the GitHub mirror, used in license headers                                                                         |
| `tidalwave.slogan`              | Short description used in license headers (default `${project.name}`)                                                     |
| `tidalwave.project.url`         | Project home page, used in license headers (not defined, see [Properties you must provide](#properties-you-must-provide)) |

The `<scm>` section is repeated from the parent (because of [SCM-442](http://jira.codehaus.org/browse/SCM-442)) with
the `git` provider hard-coded: `<connection>` is `scm:git:${scm.repo.readonly.url}`, `<developerConnection>` is
`scm:git:${scm.repo.effective.url}` and `<url>` is `${scm.repo.browse.url}`.


### Repositories

Two repositories are added to those of the parent:

| Id                                          | URL                                                                    | Contents                                   |
|---------------------------------------------|------------------------------------------------------------------------|--------------------------------------------|
| `releases-oss.sonatype.org`                 | `https://oss.sonatype.org/content/repositories/releases/`              | Sonatype releases                          |
| `maven2-thirdparty-repository.tidalwave.it` | `https://services.tidalwave.it/nexus/content/repositories/thirdparty/` | Third-party artifacts not on Maven Central |

They are inherited by every project that uses this SuperPOM.


### Managed dependencies

The following dependencies are declared in `<dependencyManagement>`, so projects can use them without specifying a
version. Each version can be overridden by setting the corresponding property.

| Dependency                                  | Version property                             |
|---------------------------------------------|----------------------------------------------|
| `org.aspectj:aspectjrt`                     | `tft.aspectjrt.version`                      |
| `org.aspectj:aspectjweaver`                 | `tft.aspectjrt.version`                      |
| `org.slf4j:slf4j-api`                       | `tft.slf4j.version`                          |
| `org.slf4j:jcl-over-slf4j`                  | `tft.slf4j.version`                          |
| `org.slf4j:jul-to-slf4j`                    | `tft.slf4j.version`                          |
| `ch.qos.logback:logback-core`               | `tft.logback.version`                        |
| `ch.qos.logback:logback-classic`            | `tft.logback.version`                        |
| `javax.inject:javax.inject`                 | `tft.jsr330.version`                         |
| `javax.annotation:javax.annotation-api`     | `tft.javax-annotation.version`               |
| `jakarta.inject:jakarta.inject-api`         | `tft.jakarta.inject-api.version`             |
| `jakarta.annotation:jakarta.annotation-api` | `tft.jakarta.common-annotations-api.version` |
| `org.apiguardian:apiguardian-api`           | `tft.apiguardian-api.version`                |
| `com.github.spotbugs:spotbugs-annotations`  | `tft.spotbugs-annotations.version`           | 
| `org.projectlombok:lombok`                  | `tft.lombok.version`                         |
| `org.junit.jupiter:junit-jupiter`           | `tft.junit.version`                          |
| `org.testng:testng`                         | `tft.testng.version`                         |
| `org.mockito:mockito-core`                  | `tft.mockito.version`                        |
| `org.hamcrest:hamcrest`                     | `tft.hamcrest-matchers.version`              |
| `org.testfx:testfx-core`                    | `tft.testfx.version`                         |
| `org.testfx:testfx-junit5`                  | `tft.testfx.version`                         |
| `org.assertj:assertj-core`                  | `tft.assertj.core.version`                   |

Since `tft.aspectjrt.version` is defined here, the AspectJ profiles of the parent (which require it) work without
further configuration.

`tft.spring.version` and `tft.spring-boot.version` are defined too, but **no Spring dependency is
managed**: projects using Spring must manage its versions themselves (e.g. by importing the Spring BOM with those
properties in their `<dependencyManagement>`).


### Dependency rules

Two rule sets are added to the Maven Enforcer Plugin, both run at `validate`:

* the `enforce-maven` execution inherited from the parent gets the **`dependencyConvergence`** rule in addition to its own
  rules: the build fails if different versions of the same dependency are reachable through the dependency tree. Solve
  conflicts with `<dependencyManagement>` or exclusions;
* a new execution **`enforce-banned-dependencies`** fails the build if any of these artifacts is among the dependencies:

| Banned artifact                        | Notes                                          |
|----------------------------------------|------------------------------------------------|
| `commons-logging:commons-logging`      | Use `org.slf4j:jcl-over-slf4j`                 |
| `com.kenai.nbpwr:org-slf4j-jdk14`      | Old repackaging of the SLF4J JDK 1.4 binding   |
| `com.google.code.findbugs:annotations` | Use `com.github.spotbugs:spotbugs-annotations` |

`org.aspectj:aspectjweaver` used to be banned too (Tidalwave projects only use static weaving), but the rule is now
commented out because recent Spring versions require it anyway.


### Tests

The TestNG listener `it.tidalwave.util.test.TestLogger` from TheseFoolishThings, which provides enhanced test logging,
is registered with Surefire through the `listener` property, whose value is `tft.test.listeners`. The class must be
available on the test classpath. To use different listeners, set `tft.test.listeners` to a comma-separated list of
class names; **don't set it to an empty value**, since Surefire fails with an empty `listener` property (this is also
the reason why the parent doesn't configure it).

All the other test settings (`tft.test.*`) are inherited from the parent.


### Javadoc

The following custom tags are defined, both in the build and in the reporting section (the two configurations must be
kept aligned). All of them can be placed on types only (`t`):

| Tag                                  | Heading in the generated documentation |
|--------------------------------------|----------------------------------------|
| `@it.tidalwave.javadoc.stable`       | `Status: stable API`                   |
| `@it.tidalwave.javadoc.draft`        | `Status: draft API`                    |
| `@it.tidalwave.javadoc.experimental` | `Status: experimental API`             |
| `@stereotype`                        | `Stereotype:`                          |
| `@hidden`                            | none                                   |

Javadoc is otherwise configured as described in the parent manual (UML Doclet on JDK 9+, `tft.javadoc.*` properties).


### License headers

In addition to the Codehaus License Maven Plugin configured by the parent, this SuperPOM configures the Mycila License
Maven Plugin (version `tft.mycila-license-maven-plugin.version`, managed by the parent):

* the header is `tft.header` (default `it/tidalwave/header/header.txt`), loaded from the plugin classpath, to which
  `it.tidalwave.thesefoolishthings:superpom-config` (`tft.superpom.config.version`) is added;
* the header template is filled with these properties: `scm.repo.readonly.url`, `tidalwave.slogan`,
  `tidalwave.project.url`, `tidalwave.github.readonly.url`, `tft.header.year` (default `2021`),
  `project.organization.name` and `project.organization.url`;
* these files are excluded: `**/README.md`, `*.txt` and `*.properties` files in `src/main/resources` and
  `src/test/resources` (XML resources are kept), `.maven-env`, `.maven-local-env`, `lombok.config`, `*.form`,
  `*-profile` marker files, `.idea`, `bitbucket-pipelines.yml`, `LICENSE.*` and `.lgtm.yml`.

The plugin has no executions bound to the lifecycle. It's run by three profiles, which only change the default goal, so
they must be activated with no goal on the command line:

| Command                                | Effect                                                                                     |
|----------------------------------------|--------------------------------------------------------------------------------------------|
| `mvn -Pit.tidalwave-update-headers-v2` | Adds or updates the license header of each file (`com.mycila:license-maven-plugin:format`) |
| `mvn -Pit.tidalwave-check-headers-v2`  | Fails if a file has a missing or wrong header (`com.mycila:license-maven-plugin:check`)    |
| `mvn -Pit.tidalwave-remove-headers-v2` | Removes the license headers (`com.mycila:license-maven-plugin:remove`)                     |

The `it.tidalwave-update-headers` and `it.tidalwave-check-headers` profiles of the parent (without the `-v2` suffix),
based on the Codehaus plugin, are still available.


### Site

The [Maven Fluido Skin](https://maven.apache.org/skins/maven-fluido-skin/) (`org.apache.maven.skins:maven-fluido-skin`,
version `tft.maven-fluido-skin.version`) is added as a dependency of the Site Plugin, so projects can
reference it in their `src/site/site.xml`.

The profile `it.tidalwave-site-docs-maven-skin-v1` is redeclared with no activation trigger
(`<activeByDefault>false</activeByDefault>`), since Tidalwave projects don't use the Docs Maven Skin.

Site generation is otherwise unchanged: see the `it.tidalwave-site-v1` and `it.tidalwave-metrics-v2` profiles in the
parent manual.


### XML binding (JAXB)

The JAXB2 Maven Plugin (version `tft.jaxb2-maven-plugin.version`, managed by the parent) gets these additional plugin
dependencies, all with version `tft.jaxb.version`, so that `xjc` generates code for Jakarta XML Binding
(`jakarta.xml.bind`) instead of `javax.xml.bind`:

* `org.glassfish.jaxb:jaxb-xjc`;
* `jakarta.xml.bind:jakarta.xml.bind-api`;
* `com.sun.xml.bind:jaxb-impl`.

No execution is declared: projects add their own (e.g. the `xjc` goal) in their POM.


### Profiles

The profiles defined in this SuperPOM are summarised below. All the profiles of the parent are available as well.

| Profile                                | Activation                                                           | Purpose                                          |
|----------------------------------------|----------------------------------------------------------------------|--------------------------------------------------|
| `it.tidalwave-spring-messagebus-v1`    | file `src/config/activate-it.tidalwave-spring-messagebus-v1-profile` | TheseFoolishThings Spring Message Bus            |
| `graph`                                | `-P`                                                                 | Make the Maven Graph Plugin available            |
| `it.tidalwave-update-headers-v2`       | `-P`                                                                 | Update license headers (Mycila)                  |
| `it.tidalwave-check-headers-v2`        | `-P`                                                                 | Check license headers (Mycila)                   |
| `it.tidalwave-remove-headers-v2`       | `-P`                                                                 | Remove license headers (Mycila)                  |
| `it.tidalwave-site-docs-maven-skin-v1` | none                                                                 | Redeclared without activation, see [Site](#site) |

As in the parent, marker files are empty and live under `src/config`:

```shell
mkdir -p src/config
touch src/config/activate-it.tidalwave-spring-messagebus-v1-profile
```


### Spring Message Bus

**`it.tidalwave-spring-messagebus-v1`** (file `src/config/activate-it.tidalwave-spring-messagebus-v1-profile`) adds what
is needed to use the [TheseFoolishThings](http://tidalwave.it/projects/thesefoolishthings) message bus with Spring:

* the dependencies `it.tidalwave.thesefoolishthings:it-tidalwave-messagebus-spring`,
  `it.tidalwave.thesefoolishthings:it-tidalwave-messagebus`, `org.springframework:spring-aspects` and
  `org.aspectj:aspectjweaver`;
* `it-tidalwave-messagebus-spring` as an additional aspect library of the AspectJ Maven Plugin (appended to the aspect
  libraries configured by the parent).

Note that:

* the versions of the two TheseFoolishThings artifacts and of `spring-aspects` are not managed here, so the project must
  manage them (e.g. by importing the TheseFoolishThings BOM and the Spring BOM);
* the profile doesn't declare executions of the AspectJ plugin: weaving happens only when one of the AspectJ profiles of
  the parent is active too, typically `it.tidalwave-aspectj-springaop-v2`
  (file `src/config/activate-it.tidalwave-aspectj-springaop-v2-profile`).


### Dependency graphs

**`graph`** (`-P`) adds the plugin repository `http://mvnplugins.fusesource.org/repo/release` and declares the
[Maven Graph Plugin](https://github.com/fusesource/mvnplugins) (`org.fusesource.mvnplugins:maven-graph-plugin`, version
`tft.maven-graph-plugin.version`, managed by the parent). No goal is bound: invoke the plugin explicitly, e.g.:

```shell
mvn -Pgraph graph:reactor
```

Since the plugin repository uses plain HTTP, which Maven blocks by default since version 3.8.1, it must be allowed in
`settings.xml` (or the plugin must be available in another repository) for the profile to work.


### Building and releasing this project

To build this project:

```shell
mvn -DskipTests
```

`.maven-env` sets `JAVA_HOME` to `$JAVA21_HOME` for the Tidalwave build scripts.

The release follows the transactional cycle described in the parent manual (`mvn -Prelease release:prepare
release:perform`, then `mvn -N -Prelease-commit-central` or `mvn -N -Prelease-cancel`).

The assembly descriptors in `src/assemble` activate the parent profiles `it.tidalwave-assembly-bin-descriptor` and
`it.tidalwave-assembly-project-descriptor`: `bin` contains `README*`, `LICENSE*` and `pom.xml`, and `project` contains
the whole source tree except `target`, `.git` and `.description`.


### Properties reference

Only the properties defined or overridden here are listed; see the parent manual for all the others.

**General**

| Property                        | Default           | Description                         |
|---------------------------------|-------------------|-------------------------------------|
| `tft.maven.requiredVersion`     | `3.9.9`           | Minimum Maven version               |
| `tidalwave.issues`              | empty             | Jira project key                    |
| `tidalwave.ci.view`             | empty             | Jenkins view name                   |
| `tidalwave.github.readonly.url` | empty             | GitHub mirror URL (license headers) |
| `tidalwave.slogan`              | `${project.name}` | Project slogan (license headers)    |

**Tests**

| Property             | Default                             | Description                                                  |
|----------------------|-------------------------------------|--------------------------------------------------------------|
| `tft.test.listeners` | `it.tidalwave.util.test.TestLogger` | TestNG listeners registered with Surefire; must not be empty |

**License headers (Mycila)**

| Property          | Default                          | Description                                    |
|-------------------|----------------------------------|------------------------------------------------|
| `tft.header`      | `it/tidalwave/header/header.txt` | Header resource, loaded from `superpom-config` |
| `tft.header.year` | `2021`                           | Year shown in the header                       |

**Site**

| Property                        | Description                |
|---------------------------------|----------------------------|
| `tft.maven-fluido-skin.version` | Version of the Fluido Skin |

**XML binding**

| Property           | Description                                                     |
|--------------------|-----------------------------------------------------------------|
| `tft.jaxb.version` | Version of the Jakarta JAXB artifacts added to the JAXB2 plugin |

**Library versions**

| Property                                     | Managed dependencies                                                                  |
|----------------------------------------------|---------------------------------------------------------------------------------------|
| `tft.apiguardian-api.version`                | `org.apiguardian:apiguardian-api`                                                     |
| `tft.aspectjrt.version`                      | `org.aspectj:aspectjrt`, `org.aspectj:aspectjweaver`; also used by the AspectJ plugin |
| `tft.assertj.core.version`                   | `org.assertj:assertj-core`                                                            |
| `tft.hamcrest-matchers.version`              | `org.hamcrest:hamcrest`                                                               |
| `tft.jsr330.version`                         | `javax.inject:javax.inject`                                                           |
| `tft.javax-annotation.version`               | `javax.annotation:javax.annotation-api`                                               |
| `tft.jakarta.inject-api.version`             | `jakarta.inject:jakarta.inject-api`                                                   |
| `tft.jakarta.common-annotations-api.version` | `jakarta.annotation:jakarta.annotation-api`                                           |
| `tft.spotbugs-annotations.version`           | `com.github.spotbugs:spotbugs-annotations`                                            |
| `tft.junit.version`                          | `org.junit.jupiter:junit-jupiter`                                                     |
| `tft.logback.version`                        | `ch.qos.logback:logback-core`, `ch.qos.logback:logback-classic`                       |
| `tft.lombok.version`                         | `org.projectlombok:lombok`                                                            |
| `tft.mockito.version`                        | `org.mockito:mockito-core`                                                            |
| `tft.slf4j.version`                          | `org.slf4j:slf4j-api`, `org.slf4j:jcl-over-slf4j`, `org.slf4j:jul-to-slf4j`           |
| `tft.spring.version`                         | none                                                                                  |
| `tft.spring-boot.version`                    | none                                                                                  |
| `tft.testfx.version`                         | `org.testfx:testfx-core`, `org.testfx:testfx-junit5`                                  |
| `tft.testng.version`                         | `org.testng:testng`                                                                   |


### Properties you must provide

In addition to those listed in the parent manual (note that `tft.aspectjrt.version` is now provided by this SuperPOM),
set these properties or versions when you use the corresponding feature:

| Property / version                                                                        | Needed by                                                  |
|-------------------------------------------------------------------------------------------|------------------------------------------------------------|
| `tidalwave.issues`, `tidalwave.ci.view`                                                   | Meaningful `<issueManagement>` and `<ciManagement>` URLs   |
| `tidalwave.project.url`                                                                   | Mycila license headers (not defined anywhere)              |
| `tidalwave.github.readonly.url`                                                           | Mycila license headers, if the template references it      |
| Versions of `it-tidalwave-messagebus`, `it-tidalwave-messagebus-spring`, `spring-aspects` | `it.tidalwave-spring-messagebus-v1`                        |
| The class in `tft.test.listeners` on the test classpath                                   | Surefire (by default, the TheseFoolishThings `TestLogger`) |


### Unused properties

These properties are defined but not currently used by any plugin configuration:

| Property                          | Notes                                                            |
|-----------------------------------|------------------------------------------------------------------|
| `tft.UmlGraphDoc.additionalParam` | Available for projects, e.g. to import the Spring BOM            |
| `tft.spring-boot.version`         | Available for projects, e.g. to import the Spring Boot BOM       |