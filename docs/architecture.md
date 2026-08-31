# Architecture

`jeap-test-message-type-registry` is a
[message-type registry](https://github.com/jeap-admin-ch/jeap-message-type-registry) instance used by
integration tests in [jeap-messaging](https://github.com/jeap-admin-ch/jeap-messaging). It is **not a
template for a new registry and not for productive use** — it exists purely to give the jeap-messaging
test suite a set of stable, versioned test message types (events and commands) to build and publish
Java bindings from.

It follows the same descriptor/registry layout, validation, and publishing mechanics as
[jeap-message-type-registry](https://github.com/jeap-admin-ch/jeap-message-type-registry) — see that
repository's docs for the full explanation of the registry structure, descriptor fields, and the
build/publish pipeline (`jeap-messaging-registry-maven-plugin`, `jeap-messaging-avro-maven-plugin`).

## Contents

```text
descriptor/
  jme/
    event/                 test events for system JME (e.g. JmeSimpleTestEvent, JmeEnumTestEvent, ...)
    command/                test commands for system JME (e.g. JmeCreateDeclarationCommand)
    _common/                shared records for system JME
  jeap/
    event/                 test events for system JEAP (e.g. JeapInitializerSimpleTestEvent)
```

Test types intentionally exercise registry features such as schema evolution
(`JmeBackwardSchemaEvolutionTestEvent`), enums (`JmeEnumTestEvent` / `JmeEnumTestV2Event`), and shared
message keys, so that jeap-messaging's integration tests can verify behavior against realistic,
versioned message types.

Generated Java bindings use the group id prefix `ch.admin.bit.jeap.messaging.test.messagetype` (see
`groupIdPrefix` in `pom.xml`), separate from the `ch.admin.bit.jeap.messagetype` prefix used by the
real `jeap-message-type-registry`, so test artifacts can never be mistaken for production ones.

## Related

- [jeap-message-type-registry](https://github.com/jeap-admin-ch/jeap-message-type-registry) — the
  registry structure, descriptor fields, and build/publish pipeline this repository follows
- [jeap-messaging](https://github.com/jeap-admin-ch/jeap-messaging) — the consumer of these test types
