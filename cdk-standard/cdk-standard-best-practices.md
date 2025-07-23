# CDK Best Practices (Inspired by mbonig version - credit - https://github.com/mbonig/kiro-steering-docs)

## Basics

* Use projen for project initialization and file management
* Use the latest version of the CDK, found here: https://github.com/aws/aws-cdk/releases
* Use cdk-iam-floyd for IAM policy generation
* Additional CDK apps should have a projen task with a prefix `app:`. E.g. `app:iam-roles`.

## Structure

* All files in the `src/**` directory.
* Applications in the `src/` directory.
* Stacks in the `src/stacks/**` directory.
* Constructs in the `src/constructs/**` directory.
* Stages in the `src/stages/**` directory.
* Lambda function handlers in a sub-directory of the defining construct, called `handler`.
* Kebab-casing for filenames with dot between name and purpose of file (e.g. some-construct.construct.ts)
* Each custom construct (stack/stage/construct) should reside in its own file that is named the same as the construct. Interfaces for the props will be in the same file. For example, if the construct is named SomeConstruct and uses the SomeConstructProps, both the class and the interface would be in the some-construct.construct.ts file.

## Apps

* SHOULD contain distinct stack/stage instances for each environment.
* SHOULD provide each stack/stage with account/region specific values
* Context SHOULD NOT be used for anything, at all.

## Stacks

* SHOULD be responsible for importing resources (`Vpc.fromLookup()`, `Bucket.fromBucketName()`, etc.)
* SHOULD be responsible for instantiating constructs

## Constructs

* SHOULD save the incoming constructor props as a private field to be used later
* SHOULD create all resources in protected methods, not in the constructor.
* SHOULD NOT import resources (e.g. `Vpc.fromLookup()`)
* SHOULD be passed concrete objects representing resources
* Properties that represent well-formed resource identifies (e.g. VPC ID, Security Group ID, etc.) should be defined as a template literal type (https://www.typescriptlang.org/docs/handbook/2/template-literal-types.html), e.g. `vpc-${string}`
* Constructs SHOULD check private properties are set before using them and throw an error indicating the protected method that should have been called and the private property that was not set.

## Tests

* All tests in the `test/` directory.
* Tests should reside in a file with a name matching the construct name. For example, SomeConstruct.ts is tested with some-construct.construct.test.ts.
* Constructs should have thorough test coverage using fine-grained assertions (https://docs.aws.amazon.com/cdk/v2/guide/testing.html#testing-fine-grained)
* Stacks should be tested using snapshot tests.
* Code.fromAsset and ContainerImage.fromAsset calls should be mocked in all tests using:
```
let fromAssetMock: jest.SpyInstance;

beforeAll(() => {
  fromAssetMock = jest.spyOn(Code, 'fromAsset').mockReturnValue({
    isInline: false,
    bind: (): CodeConfig => {
      return {
        s3Location: {
          bucketName: 'my-bucket',
          objectKey: 'my-key',
        },
      };
    },
    bindToResource: () => {
      return;
    },
  } as any);
});

afterAll(() => {
  fromAssetMock?.mockRestore();
});
```

## Lambda Functions

* Lambda functions should use NodejsFunction or PythonFunction whenever possible
* X-Ray tracing and logging with powertools should be enabled for functions. (https://docs.powertools.aws.dev/lambda/python/latest/) and (https://docs.powertools.aws.dev/lambda/typescript/latest/)
