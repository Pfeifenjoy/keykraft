# ⚒️ KeyKraft

![Status](https://img.shields.io/badge/status-work%20in%20progress-orange)
![CI](https://github.com/Pfeifenjoy/keykraft/actions/workflows/ci.yml/badge.svg)

A TypeScript-first DynamoDB library built around single-table design. Define your table schema once with Zod — KeyKraft enforces access patterns, generates type-safe query builders, and ships a CDK construct that provisions the exact table your schema describes.

## 💡 Motivation

DynamoDB's power comes from single-table design, but the ergonomics are rough:

- Keys are stringly-typed magic strings scattered across your codebase
- GSIs are defined in CDK, duplicated in query code, and drift silently
- Nothing stops you from putting a `User` item where an `Order` belongs
- Marshalling/unmarshalling is boilerplate that hides bugs

KeyKraft treats the schema as the single source of truth. Your entity definitions drive the CDK table construct, the query client, and your TypeScript types — all from one place.

## 🧠 Core concepts

### Single-table design

KeyKraft assumes one DynamoDB table per domain, with composite keys (`PK` / `SK`) and overloaded item types. Entities share the same table and are distinguished by their key prefixes. This is a constraint, not a configuration option — it's what makes the type safety tractable.

### Entity key builders

Each entity declares how its primary key is constructed from its attributes. KeyKraft uses these builders to:

- Generate keys on write automatically
- Derive `KeyConditionExpression` values on query without you writing expression strings
- Prevent you from querying an entity type through a key pattern that doesn't belong to it

### Zod as the schema layer

Zod schemas serve three purposes:

1. **Validation** — items are validated on put/update, catching shape errors before they reach DynamoDB
2. **Types** — `z.infer<>` drives all TypeScript types; there is no separate type definition
3. **Serialisation** — custom Zod types handle `Date ↔ ISO string`, `Set ↔ DynamoDB SS/NS`, and `bigint` transparently

### CDK integration

The CDK construct reads your table definition at synth time and provisions the DynamoDB table — GSIs, TTL, and stream settings included. Because the construct is driven by the same schema object your client uses, the two can never drift.

## 🤔 Design decisions

**Why not support multi-table design?**  
Single-table design is the access pattern DynamoDB is optimised for. Supporting arbitrary schemas would mean giving up the key-prefix safety that makes the type system valuable. If you need multiple tables, instantiate multiple `defineTable` schemas.

**Why Zod and not TypeBox, Valibot, etc.?**  
Zod has the widest adoption in the TS ecosystem and the most mature ecosystem of transformers and plugins. The core is decoupled enough that a Valibot adapter is feasible later, but Zod ships first.

**Why not wrap an existing ORM (Dynamoose, DynamoDB Toolbox)?**  
Existing libraries predate the modern Zod ecosystem and carry design decisions that conflict with strict single-table patterns. KeyKraft is designed schema-first with CDK parity as a first-class constraint.

## 🤝 Contributing

This is a monorepo managed with npm workspaces. See `CONTRIBUTING.md` for guidelines.

## 📄 License

MIT
