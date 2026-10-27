---
name: prefer-static-decisions
description: Prefer static representations for facts, constraints, choices, relationships, and structure that are already known, and resolve genuine runtime configuration at the earliest boundary where its values are available. Match implementation dynamism to the actual variability required by the problem.
---

# Prefer Static Decisions

Prefer static representations for things that are already known. Resolve genuinely runtime-provided information at the earliest boundary where its required values are available.

## Core principles

- Express facts known at implementation time directly in code or data.
- Express closed sets of valid values with constrained types such as enums, tagged unions, literals, or equivalent language features.
- Express fixed relationships as static data, tables, mappings, modules, or direct references.
- Resolve choices at build or compile time when runtime variation is not required.
- Match the dynamism of the implementation to the variability required by the problem.
- Keep runtime decisions for information that can actually vary at runtime.

## Decision rule

When introducing a runtime branch, lookup, validation, configuration value, registry, strategy selection, or similar mechanism, first ask:

1. What information can vary at runtime?
2. Which parts are already known when the code is written or built?
3. Can those known parts be represented directly as types, values, structure, or static data?
4. What is the smallest amount of runtime variability the implementation actually needs?

Prefer the representation that answers these questions with the least unnecessary dynamism.

## Common applications

### Closed value sets

When only a finite known set of values is valid, represent that set directly.

For example, if a value can only be `1` or `2`, prefer a two-case type over a general integer plus repeated range checks inside the program.

External input may still begin as a broad type. Validate or parse it at the boundary, then convert it into the constrained internal representation.

### Fixed structure

When the number, order, or shape of elements is fixed, prefer a representation that encodes that structure directly.

Examples include tuples, records, structs, fixed-size arrays, dedicated fields, or other language-appropriate fixed forms.

### Fixed mappings

When a mapping is known in advance, prefer static data over procedural construction.

A table, map literal, match expression, or equivalent direct representation is usually clearer than a sequence of runtime mutations that always creates the same result.

### Fixed dependencies

When a dependency is fixed for the implementation, represent it directly.

Dynamic registries, provider selection, service lookup, and strategy switching are appropriate when those choices genuinely vary. A single fixed implementation usually benefits from direct composition.

### Fixed configuration

When a value is part of the implementation rather than an operational choice, keep it in the implementation.

Runtime configuration is most useful for values that operators, deployments, users, or environments are expected to change independently of the code.

Treat environment-variable-backed configuration as one startup boundary:

- Define the complete set of environment-derived settings in one configuration schema, constructor, or equivalent entry point.
- Read every declared environment-derived setting during startup.
- Resolve defaults and validate required presence, parsing, allowed values or ranges, and relationships whose inputs are available at startup.
- Complete this validation before application work, background workers, network listeners, or other dependent components start.
- Convert the validated values into a configuration representation and pass that representation inward. Application components consume the validated configuration as their source of runtime settings.
- When validation finds configuration errors, end startup with a configuration error that reports every detected setting or constraint failure from that validation pass. Identify settings and violated constraints directly.

### Build-time choices

When environments or targets require different implementations and that distinction is known during the build, prefer build-time or compile-time selection where the language and toolchain support it naturally.

## Boundaries

Runtime logic is appropriate when the result depends on runtime information such as:

- user input;
- external service responses;
- persisted state;
- permissions or capabilities discovered at runtime;
- deployment configuration intended to remain changeable without rebuilding;
- plugins or implementations that are genuinely selected or discovered dynamically.

The goal is not to maximize compile-time machinery. The goal is to represent each decision at the earliest stage where its required information is available.

Prefer simple static code over elaborate type-level or compile-time machinery when both provide the same practical guarantee.

## Review guidance

During implementation or review, look for places where the code creates runtime flexibility around something that is already fixed.

Typical signals include:

- broad primitive types followed by checks for a small known set of values;
- feature flags whose value is fixed in every supported execution;
- configuration entries for values that are implementation constants;
- environment-backed runtime settings that can be consolidated into one validated startup configuration;
- registries containing a single implementation;
- provider or strategy abstractions with only one real choice;
- runtime lookups for relationships known when writing the code;
- repeated validation of a constraint that can be encoded once at a boundary or in a type;
- conditional construction that always produces one known structure.

When one of these appears, prefer a direct static representation unless actual runtime variability justifies the dynamic mechanism.

## Examples

### Prefer a closed domain

Instead of accepting any integer internally and checking that it falls within a two-value domain, parse external input once and convert it to a type with exactly two valid cases.

### Prefer direct composition

If an application always uses one concrete implementation of a service, compose that implementation directly. Introduce runtime selection when the application actually supports multiple runtime-selectable implementations.

### Prefer static data

If a command maps a fixed set of modes to fixed labels, encode the mapping directly rather than constructing and validating it on every execution.

## Non-goals

This skill does not require replacing ordinary runtime logic with advanced metaprogramming, type-level programming, code generation, or compile-time computation.

It does not prefer static representations when doing so would obscure genuine runtime behavior or make the implementation harder to understand.

Its purpose is narrower: place decisions where their information naturally becomes known, and avoid carrying unnecessary dynamism further into the program.
