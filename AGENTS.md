# AGENTS.md

Guidance for AI coding agents working **in this repository**. For how message types published from
this registry are consumed, read [README.md](README.md) and the [docs/](docs/) folder instead.

## Project

This repository is a **test-only message-type registry** — a
[message-type registry](https://github.com/jeap-admin-ch/jeap-message-type-registry)-style instance
used exclusively by the integration tests of
[jeap-messaging](https://github.com/jeap-admin-ch/jeap-messaging). **Not for productive use, not a
template.** It contains JSON type descriptors and Avro `.avdl` schemas for a set of stable test events
and commands, plus the Maven build that validates and publishes them as Java artifacts under a
`.test.` group-id prefix.

## Repository layout

```
pom.xml                                  # Single module (packaging=pom); drives validation + publishing
messagetype.template.pom.xml             # POM template for each generated test message-type artifact
descriptor/
  jme/event/<typename-lowercase>/        # test events for system JME
  jme/command/<typename-lowercase>/      # test commands for system JME
  jme/_common/                           # shared records for system JME
  jeap/event/<typename-lowercase>/       # test events for system JEAP
Jenkinsfile, publiccode.yml, LICENSE
```

There are **no CHANGELOG.md** and **no child modules** in this repository.

## Build & validate

```bash
./mvnw verify
```

- Parent: `ch.admin.bit.jeap:jeap-spring-boot-parent`.
- The build runs the `jeap-messaging-registry-maven-plugin` (`registry` goal) and the
  `jeap-messaging-avro-maven-plugin` (`compile-message-types`, `deploy-message-type-artifacts`), same
  as the real `jeap-message-type-registry`.
- Generated artifacts use `groupIdPrefix = ch.admin.bit.jeap.messaging.test.messagetype` (see `pom.xml`)
  — distinct from the real registry's `ch.admin.bit.jeap.messagetype` prefix, so test artifacts can
  never be mistaken for production message types.
- Validation **must pass** before merging to `master`. It rejects edits to schema versions that are
  already released — never change or delete an existing `.avdl`; add a new version instead.

## Conventions (load-bearing)

Follows the same conventions as
[jeap-message-type-registry](https://github.com/jeap-admin-ch/jeap-message-type-registry) (directory
naming, descriptor fields, Avro naming, immutability) — see that repository's
`docs/registry-structure.md` for the authoritative reference. When adding new test types here, prefer
adding ones that exercise a specific jeap-messaging feature (schema evolution, enums, shared keys,
etc.) rather than duplicating existing coverage.

## Docs

When adding new test message types intended to demonstrate a specific registry/messaging feature,
mention the intent in [docs/architecture.md](docs/architecture.md) so future contributors understand
why the type exists.

## Versioning

- Semantic Versioning. The project `<version>` lives directly in `pom.xml` (single module).
- On a feature branch keep the `-SNAPSHOT` suffix; CI removes it when releasing.
- This repo has **no CHANGELOG.md**. When bumping the version, also update `softwareVersion` and
  `releaseDate` in `publiccode.yml`.
- Use the JIRA ID from the branch name as the commit-message prefix (e.g. `JEAP-1234 Add ...`); do not
  use conventional commits.
