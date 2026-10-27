# prefer-static-decisions

A lightweight coding skill for preferring static representations when relevant facts, constraints, choices, relationships, or structure are already known, and for resolving genuine runtime configuration at the earliest boundary where its values are available.

## Principle

Express decisions at the earliest stage where the required information is available.

Prefer static types, values, data, structure, and composition when runtime variability is not required. Use runtime logic for information that genuinely becomes available or changes at runtime.

## What it helps with

This skill is useful when code introduces more runtime flexibility than the problem requires, for example:

- using a broad primitive type for a small closed set of valid values;
- repeatedly validating constraints that can be encoded once in a type or at a boundary;
- introducing runtime configuration for implementation constants;
- loading environment-backed runtime settings outside a single validated startup boundary;
- using registries, providers, or strategy selection for a single fixed implementation;
- performing runtime lookups for relationships already known when the code is written;
- procedurally constructing data that can be expressed directly as static data;
- carrying build-time decisions into runtime branches.

External input can still begin in a broad representation. Parse or validate it at the boundary, then convert it into the constrained internal representation.

For environment-backed runtime configuration, define the application-used settings together, load and validate them during startup, and pass the resulting validated configuration inward.

## Example

If an internal value can only be `1` or `2`, model those two valid cases directly instead of accepting an arbitrary integer throughout the program and checking its range repeatedly.

The same principle applies beyond types: fixed mappings, dependencies, configuration, structure, and build-time choices can often be represented directly.

## Scope

The goal is not to maximize compile-time machinery.

The goal is to match implementation dynamism to actual variability and to keep decisions close to the stage where their inputs become known.

For the complete instructions, see [`SKILL.md`](./SKILL.md).

## License

MIT
