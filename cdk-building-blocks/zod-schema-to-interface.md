# Zod based schema interfaces best practices.

The below are a set of practices and standard to apply when creating building blocks which use zod schemas and input and output interface types.

## Why?

To create easy-use, configurable and portable and re-usable complex building blocks (think CDK L3's but often with additional complexity regarding multi-account, multi-region complexity). These are useful in cases of organization management, infrastructure composability with ease, and also building new infra at pace.

## About schemas and interfaces

* Input types typically have optional default properties as they have been defaulted with zod.
* Output types expect defaulted properties as non optional values when the whole configuration type is being loaded.
* Input types are utilised to provide an interface to a building block with intellisense support.
Output types are loaded prior to configuring a building block with both provided and defaulted values

## The basics

* Humans or agents author zod schemas in schema.ts.
* Interface types are generated from zod schemas and subsequent types reside in type.ts, based on the following rules:
* Generated interfaces and interface properties have TSDoc comments.

## How to convert

For every Zod schema in schema.ts with the word schema in its name, generate two TypeScript interfaces:

1.
Name: XSchemaInput
Goals:
- Retain optional fields as defined in the schema.ts
- Retain describe comments as tsdoc in the generated types.ts

2.
Name: X
Goals:
- An output interface that makes all fields required (e.g., using .parse() output), suitable for usage after validation.
- Retain describe comments as tsdoc in the generated types.ts

Example:

schema.ts

```typescript
const accountInfoSchema = z.object({
  id: z.string().describe('The ID of the AWS account'),
  name: z.string().describe('The alias of the AWS account'),
  email: z.string().describe('The email of the AWS account'),
  arn: z.string().describe('The ARN of the AWS account'),
  joinedMethod: z.string().describe('The method of joining the AWS account'),
  joinedTimestamp: z.string().describe('The timestamp of joining the AWS account'),
  status: z.string().describe('The status of the AWS account'),
});
```

In types.ts in the same folder as the schema.ts should result in 2 new interfaces:

```typescript
export interface AccountInfoSchemaInput {
  /**
   * The ID of the AWS account
   */
  readonly id: string;
  /**
   * The alias of the AWS account
   */
  readonly name: string;
  /**
   * The email of the AWS account
   */
  readonly email: string;
  /**
   * The ARN of the AWS account
   */
  readonly arn: string;
  /**
   * The method of joining the AWS account
   */
  readonly joinedMethod: string;
  /**
   * The timestamp of joining the AWS account
   */
  readonly joinedTimestamp: string;
  /**
   * The status of the AWS account
   */
  readonly status: string;
}

export interface AccountInfo {
  /**
   * The ID of the AWS account
   */
  readonly id: string;
  /**
   * The alias of the AWS account
   */
  readonly name: string;
  /**
   * The email of the AWS account
   */
  readonly email: string;
  /**
   * The ARN of the AWS account
   */
  readonly arn: string;
  /**
   * The method of joining the AWS account
   */
  readonly joinedMethod: string;
  /**
   * The timestamp of joining the AWS account
   */
  readonly joinedTimestamp: string;
  /**
   * The status of the AWS account
   */
  readonly status: string;
}
```

# Working with enums

Ensure zod enum types in schema.ts are rendered as proper enums as follows:

schema.ts

```typescript
const featureToggleSchema = z.enum(['ENABLE', 'DISABLE']);
```

Should result in

```typescript
/**
 * Feature toggle states for AWS Organization features
 */
export enum FeatureToggle {
  ENABLE = 'ENABLE',
  DISABLE = 'DISABLE',
}

```

## Summary

Naming Conventions:

- Input interfaces: use PascalCase + SchemaInput suffix.
- Output interfaces: use PascalCase (no Schema in the name).

Goals:

- Reflect optional types as-is in the input interfaces.
- Output interfaces must reflect fully validated, required structures.
- Retain describe comments as tsdoc in the generated types.ts
