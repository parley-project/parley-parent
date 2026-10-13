# parley-parent

The shared Maven parent POM of **Parley**, a family of Java libraries for implementing internet
protocols. Every Parley module inherits from it, so the common settings live in one place:

- Coordinates under `us.bringardner.parley`, and the metadata Maven Central requires: name, URL,
  Apache 2.0 license, developer and SCM (each module's URL and SCM point at its own GitHub repo).
- Java 11 (`maven.compiler.release`), UTF-8, and the versions of the build plugins every module uses
  (compiler, surefire, failsafe, jacoco, jar, source, javadoc, deploy, enforcer, gpg).
- Checks on every build: Maven 3.6.3+, Java 11+, and that each jar module sets `parley.moduleName`
  (written to the jar as `Automatic-Module-Name`).
- A `release` profile with everything a Maven Central release needs: sources and Javadoc jars, GPG
  signatures and the Central Portal publishing plugin.

## Using it

```xml
<parent>
    <groupId>us.bringardner.parley</groupId>
    <artifactId>parley-parent</artifactId>
    <version>1.0.0</version>
</parent>

<artifactId>parley-example</artifactId>
<properties>
    <parley.moduleName>us.bringardner.parley.example</parley.moduleName>
</properties>
```

## Releasing

A release is two separate steps, and a module can do either or both:

| Where | How | What you get |
|---|---|---|
| GitHub Packages | `mvn clean deploy` in the module (a `github` server in `~/.m2/settings.xml`) | The jar and sources jar |
| Maven Central | `mvn -Prelease clean deploy` | Sources and Javadoc jars, GPG signatures, uploaded to the Central Portal |

Tag each release `v<version>` and push the tag. The Central step needs a GPG key (passphrase in `MAVEN_GPG_PASSPHRASE`) and a Central Portal user
token in `~/.m2/settings.xml` under the server id `central`. The upload waits for you to press Publish
on central.sonatype.com, and the group id's namespace (`us.bringardner`) must be verified there.

### Order

Released versions can't have a SNAPSHOT parent or SNAPSHOT dependencies, so release in this order:

1. **parley-parent** 1.0.0.
2. Every module, starting with parley-core and following the dependencies (`mvn -f parley-build/pom.xml verify`
   builds them in that order). In each, point the parent at the released version first:
   `mvn versions:update-parent -DparentVersion=1.0.0`, and set the version of the other Parley modules it
   uses in the same way.
3. **parley-bom** last, with each module's released version in its properties.

## Modules

parley-core, parley-io, parley-net, parley-files (with parley-files-ftp, -sftp, -jdbc, -swing and
-fx), parley-ftp, parley-dns, parley-ssh, parley-mail, parley-smtp, parley-imap, parley-pop3 and
parley-brand (the logo, icons and mascot). Their versions are listed together in
[parley-bom](https://github.com/tony-bringardner/parley-bom).
`parley-build` is an aggregator for building them all with one command; it is never released.

## What Parley can help you build

These applications are built on Parley. They are not part of the library and not in the BOM; they
use it the same way your own application would:

- [parley-files-viewer](https://github.com/tony-bringardner/parley-files-viewer) and parley-files-viewer-fx:
  browse, copy, search and edit files on local, memory, FTP, SFTP and JDBC file systems (Swing and JavaFX).
- [fsh](https://github.com/tony-bringardner/fsh): a bash-like shell for any parley-files file system.
- [fsh-ide](https://github.com/tony-bringardner/fsh-ide): an IDE to edit, run and debug fsh scripts.
