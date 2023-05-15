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

Sonatype has all documentation: https://central.sonatype.org/publish/publish-guide/

Configure your system
---------------------

This is a one-time task.

You'll need to [set up GnuPG](https://central.sonatype.org/publish/requirements/gpg/):

* Install `gpg`,
* have or generate a key pair, and
* distribute your private key.

Beware that if you have more than one private key, your installation needs to select the correct one
*by default*.

Additionally, you'll need to configure Maven:

1. Configure [Maven with a master
   password](https://maven.apache.org/guides/mini/guide-encryption.html)
2. Add a `<server>` to your Maven settings with the value of the property `nexusServerId` as
   id (`ossrh` by default),
   and the username & (encrypted) password of your Sonatype account.
3. Have `gpg` configured to select the correct private key by default (this is automatically the
   case if you have only one)
4. Set up a Maven `<server>` in your settings with id `gpg.passphrase`, defining the (encrypted)
   passphrase of your private key.

Configure your project
----------------------

This is a one-time task *per project*.

You must:

1. Have a project with this POM as `<parent>`, and at least its own `<artifactId>`
2. Include a license file (named `LICENSE.md`, `LICENSE.txt` or `LICENSE`)
3. Update the property `nexusUrl` if it is not https://oss.sonatype.org/ (this is the value for old
   accounts)
4. Include correct values for the following tags (the defaults are unlikely to be correct):
   `<version>`, `<inceptionYear>`, `<name>`, `<description>`, `<developers>`, `<organisation>`, `<license>`

Additionally, you should define:

* The correct Java version & source file encoding using the following properties, if they are not
  correct:
	* `maven.compiler.release`: 17
	* `project.build.sourceEncoding`: UTF-8
	* `project.reporting.outputEncoding`: whatever `project.build.sourceEncoding` is set to
* The last version of plugins using these properties, if different from the listed values:
	* `maven-enforcer-plugin.version`: 3.3.0
	* `maven-compiler-plugin.version`: 3.11.0
	* `maven-source-plugin.version`: 3.2.1
	* `maven-javadoc-plugin.version`: 3.5.0
	* `maven-gpg-plugin.version`: 3.0.1
	* `maven-install-plugin.version`: 3.1.1
	* `maven-scm-plugin.version`: 2.0.0
	* `nexus-staging-maven-plugin`: 1.6.13
* If necessary/wanted, update these properties:
	* `nexusServerId` (defaults to `ossrh`), if your Maven settings use a different server id, as
	  defined in step 2 of "[Configure your system](#configure-your-system)"
	* `nexusUrl` (defaults to `https://oss.sonatype.org/`): use `https://s01.oss.sonatype.org/` for
	  newer projects
	* `nexusAutoReleaseAfterClose` (defaults to `true`), if set to false, you must point your
      browser to $nexusUrl, inspect the release, and then use 'mvn nexus-staging:release' or
	  'mvn nexus-staging:drop' afterward to proceed with or cancel the release

Deploy a release to Maven Central
---------------------------------

This task is repeated for every release.

To release, you'll need to ensure the following:

* Your project adheres to the requirements above,
* has a release version,
* has no snapshot dependencies,
* follows your own requirements as well

