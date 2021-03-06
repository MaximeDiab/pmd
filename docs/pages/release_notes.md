---
title: PMD Release Notes
permalink: pmd_release_notes.html
keywords: changelog, release notes
---

## {{ site.pmd.date }} - {{ site.pmd.version }}

The PMD team is pleased to announce PMD {{ site.pmd.version }}.

This is a {{ site.pmd.release_type }} release.

{% tocmaker %}

### New and noteworthy

#### Drawing a line between private and public API

Until now, all released public members and types were implicitly considered part
of PMD's public API, including inheritance-specific members (protected members, abstract methods).
We have maintained those APIs with the goal to preserve full binary compatibility between minor releases,
only breaking those APIs infrequently, for major releases.


In order to allow PMD to move forward at a faster pace, this implicit contract will
be invalidated with PMD 7.0.0. We now introduce more fine-grained distinctions between
the type of compatibility support we guarantee for our libraries, and ways to make
them explicit to clients of PMD.

##### `.internal` packages and `@InternalApi` annotation

*Internal API* is meant for use *only* by the main PMD codebase. Internal types and methods
may be modified in any way, or even removed, at any time.

Any API in a package that contains an `.internal` segment is considered internal.
The `@InternalApi` annotation will be used for APIs that have to live outside of
these packages, e.g. methods of a public type that shouldn't be used outside of PMD (again,
these can be removed anytime).

##### `@ReservedSubclassing`

Types marked with the `@ReservedSubclassing` annotation are only meant to be subclassed
by classes within PMD. As such, we may add new abstract methods, or remove protected methods,
at any time. All published public members remain supported. The annotation is *not* inherited, which
means a reserved interface doesn't prevent its implementors to be subclassed.

##### `@Experimental`


APIs marked with the `@Experimental` annotation at the class or method level are subject to change.
They can be modified in any way, or even removed, at any time. You should not use or rely
 on them in any production code. They are purely to allow broad testing and feedback.

##### `@Deprecated`

APIs marked with the `@Deprecated` annotation at the class or method level will remain supported
until the next major release but it is recommended to stop using them.


##### The transition

*All currently supported APIs will remain so until 7.0.0*. All APIs that are to be moved to
`.internal` packages or hidden will be tagged `@InternalApi` before that major release, and
the breaking API changes will be performed in 7.0.0.

#### Modified Rules

*   The rule {% rule java/errorprone/MissingSerialVersionUID %} (`java-errorprone`) has been modified
    in order to recognize also missing `serialVersionUID` fields in abstract classes, if they are serializable.
    Each individual class in the inheritance chain needs an own serialVersionUID field. See also [Should an abstract class have a serialVersionUID](https://stackoverflow.com/questions/893259/should-an-abstract-class-have-a-serialversionuid).
    This change might lead to additional violations in existing code bases.

### Fixed Issues

*   apex-bestpractices
    *   [#1348](https://github.com/pmd/pmd/issues/1348): \[apex] AvoidGlobalModifierRule gives warning even when its a webservice - false positive
*   java-codestyle
    *   [#1329](https://github.com/pmd/pmd/issues/1329): \[java] FieldNamingConventions: false positive in serializable class with serialVersionUID
    *   [#1334](https://github.com/pmd/pmd/issues/1334): \[java] LinguisticNaming should support AtomicBooleans
*   java-errorprone
    *   [#1350](https://github.com/pmd/pmd/issues/1350): \[java] MissingSerialVersionUID false-positive on interfaces
    *   [#1352](https://github.com/pmd/pmd/issues/1352): \[java] MissingSerialVersionUID false-negative with abstract classes
*   java-performance
    *   [#1325](https://github.com/pmd/pmd/issues/1325): \[java] False positive in ConsecutiveLiteralAppends

### API Changes

*   A couple of methods and fields in `net.sourceforge.pmd.properties.AbstractPropertySource` have been
    deprecated, as they are replaced by already existing functionality or expose internal implementation
    details: `propertyDescriptors`, `propertyValuesByDescriptor`,
    `copyPropertyDescriptors()`, `copyPropertyValues()`, `ignoredProperties()`, `usesDefaultValues()`,
    `useDefaultValueFor()`.

*   Some methods in `net.sourceforge.pmd.properties.PropertySource` have been deprecated as well:
    `usesDefaultValues()`, `useDefaultValueFor()`, `ignoredProperties()`.

*   The class `net.sourceforge.pmd.lang.rule.AbstractDelegateRule` has been deprecated and will
    be removed with PMD 7.0.0. It is internally only in use by RuleReference.

*   The default constructor of `net.sourceforge.pmd.lang.rule.RuleReference` has been deprecated
    and will be removed with PMD 7.0.0. RuleReferences should only be created by providing a Rule and
    a RuleSetReference. Furthermore the following methods are deprecated: `setRuleReference()`,
    `hasOverriddenProperty()`, `usesDefaultValues()`, `useDefaultValueFor()`.

### External Contributions

*   [#1309](https://github.com/pmd/pmd/pull/1309): \[core] \[CPD] Decouple Antlr Tokenizer implementation from any CPD language supported with Antlr - [Matías Fraga](https://github.com/matifraga)
*   [#1339](https://github.com/pmd/pmd/pull/1339): \[ci] Improve danger message - [BBG](https://github.com/djydewang)
*   [#1340](https://github.com/pmd/pmd/pull/1340): \[java] Derive correct classname for non-public non-classes - [kris-scheibe](https://github.com/kris-scheibe)

{% endtocmaker %}

