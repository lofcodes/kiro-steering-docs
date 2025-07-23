# Building Block Best Practices

## Structure

* Building blocks are isolated piece of functionality which are encapsulated in their own directory.
* Building blocks do not rely on external utils or functions.
* Building blocks have a zod schema, with some defaults, in schema.ts
* Building blocks have an input and output interface type in types.ts
* Buidling blocks have at least one example configuration in examples.ts showcasing the input type being used and potentially defaults being overriden..
* Building blocks have stacks in the stacks/ folder if more than one stack else use stack.ts
* Building blocks have stages in the stages/ folder for more complex multi-account building blocks
* Building blocks have constructs in the constructs/ folder for more complex building blocks else use construct.ts
* Building blocks have custom resources in the custom/ folder. Each custom resource has its own named folder with a custom-resource.ts file and a handler.ts.
* Building blocks have a resources/ folder containing supporting resources for the building block such as Service Control Policies.
* Building blocks have an index.ts file which exports construct(s), stack(s), stage(s), resource, schema.ts, types.ts and examples.ts.
* Building blocks have an utils.ts if required.

## Schemas and types

For more information on schema and types standards see [here](./zod-schema-to-interface.md)
