[![license](doc/license-APACHE-2.0-brightgreen.svg)](https://www.apache.org/licenses/LICENSE-2.0.html)
[![Maven Central](https://maven-badges.herokuapp.com/maven-central/net.sf.opk/opk-parent/badge.svg)](https://maven-badges.herokuapp.com/maven-central/net.sf.opk/opk-parent)

OPK-Parent
==========

This is the parent POM for the group ID `net.sf.opk`.

It is a boring project containing sensible defaults for plugins, as well as an enforcer
configuration suitable for Maven Central deployment via Sonatype.

Goals:

* Like the standard super POM, configure an OSS project for Maven Central deployment without
  additional configuration.
* Provide properties to update the plugin versions with, allowing fewer releases of this pom.
* Provide no dependencies, nor dependency management: projects should have their own opinion.

To deploy to Maven Central, this POM requires you to:

1. Set up a Sonatype account
2. Configure your system
3. Configure your project

These are explained below.

Set up a Sonatype account
-------------------------

This is a one-time task.

Sonatype has all documentation: https://central.sonatype.org/publish/publish-portal-guide/

Configure your system
---------------------

This is a one-time task.

You'll need to [set up GnuPG](https://central.sonatype.org/publish/requirements/gpg/):

* Install `gpg`,
* have or generate a key pair, and
* distribute your private key.

Beware that if you have more than one private key, your installation needs to select the correct one
*by default* (this is step 3 below).

Additionally, you'll need to configure Maven:

1. Configure [Maven with a master
   password](https://maven.apache.org/guides/mini/guide-encryption.html)
2. Add a `<server>` to your Maven settings with id `maven-central-portal` and the username &
   encrypted password of your Sonatype account token. You can give it a different id, but then
   you'll need to configure your projects with a property `publishingServerId` containing the id.
3. Have `gpg` configured to use the correct private key by default
    * The correct private is automatically selected if you have only one.
    * To use the key, the gpg pinentry must use the correct tty (`export GPG_TTY=$(tty)` on
      Linux/Mac),
    * Or you should have the password in an environment variable, specified by the Maven property
      `gpg.passphraseEnvName` (defaults to `MAVEN_GPG_PASSPHRASE`).

Configure your project
----------------------

This is a one-time task *per project*.

You must:

1. Have a project with this POM as `<parent>`, and at least its own `<artifactId>`
2. Include a licence file (named `LICENSE.md`, `LICENSE.txt` or `LICENSE`)
3. Include correct values for the following tags (the defaults are unlikely to be correct):
   `<version>`, `<inceptionYear>`, `<name>`, `<description>`, `<developers>`, `<organisation>`,
   `<licenses>`

Additionally, you should define:

* The correct Java version & source file encoding using the following properties, if they are not
  correct:
    * `maven.compiler.release`: 21
    * `project.build.sourceEncoding`: UTF-8
    * `project.reporting.outputEncoding`: whatever `project.build.sourceEncoding` is set to
* The version of plugins using these properties, if you want different versions:
    * `maven-enforcer-plugin.version`
    * `maven-resources-plugin.version`
    * `maven-compiler-plugin.version`
    * `maven-source-plugin.version`
    * `maven-surefire-plugin.version`
    * `maven-failsafe-plugin.version`
    * `maven-javadoc-plugin.version`
    * `maven-gpg-plugin.version`
    * `maven-install-plugin.version`
    * `maven-release-plugin.version`
    * `central-publishing-maven-plugin.version`
* If necessary/wanted, define/override these properties:
    * `publishingServerId` (`maven-central-portal` unless overridden), if your Maven settings use a
      different server id, as defined in step 2 of "[Configure your system](#configure-your-system)"
    * `autoPublish` (`true` unless overridden), `waitUntil` (`validated` unless overridden), or any
      other [publishing plugin configuration option](https://central.sonatype.org/publish/publish-portal-maven/#plugin-configuration-options)

Deploy a release to Maven Central
---------------------------------

This task is repeated for every release.

To release, you'll need to ensure the following:

* Your project adheres to the requirements above,
* has a release version,
* has no snapshot dependencies,
* follows your own requirements as well
  good requirements are that your working tree is clean and that you've tagged the current commit

Then, releasing is as simple as running `mvn deploy`


Deploy a snapshot to Maven Central
----------------------------------

If you set the property `enforcer.skip` to `true`, you can also deploy `-SNAPSHOT` releases.

Please note:
* skipping the enforcer for non-snapshot releases will still enforce the requirements, but later (
  you may need to do a manual cleanup)
* snapshot releases are removed after some time (90 days at the time of writing)
