# schema

```
npm install @orioro/schema
yarn add @orioro/schema
```

Define complex data schemas and validation steps. Supports:
- Conditional validation
- Customizable validation criteria

# Steps:

- Value resolution:
  - Resolve schema
  - Resolve value
- Value validation
  - Resolve schema
  - Resolve value
  - Validate

## Schema environment definition (`environment(options: EnvironmentOptions)`)

Schema module is extremely extensible to allow for definition of custom types, custom keywords
in schema body, etc.

Most customizations are defined through `resolvers` for each of the steps (schema resolution, 
value resolution, validation resolution, validation) and `expression interpreters`.

## Schema resolution (`resolveSchema(rawSchema, rawValue)`)

Schemas may define conditional declarations at any level/position, which should be resolved
before any other operation. Otherwise, conditional logic statements will not be interpreted
correctly by value resolution or value validation methods (`resolveValue` and `validate` respectively).

The schema resolution algorithm traverses the schema tree looking for expressions, such as
`['$if', {CONDIITON}, {THENVALUE}, {ELSEVALUE}]` and evaluates all of them using the provided
`value` as context. If the value returned by the evaluation of those expressions is an
`Object` or an `Array`, they are recursively traversed by `resolveSchema`, looking for nested
expressions, with 2 exceptions:
- `items` property (see Array item validation)
- `validations` property (see validation)

## Value resolution (`resolveValue(resolvedSchema, rawValue)`)

User input (both end-user and the programmer library user) may be faulty or not conform to
specs. For example:
- value is expected to be either a `string` or `null`, but the raw input may come as
  `undefined`.
- if an object's property value is not given (it is `undefined`), it should default to some
  value that is derived from the value of other properties

The value resolution step applies those rules by traversing the resolved schema and checking
the values at corresponding paths. Values that are not defined in the schema are removed
from the retuned resolvedValue. Custom value resolvers may be defined at schema environment
definition through the option `valueResolvers`.

## Value validation (`validate(resolvedSchema, rawOrResolvedValue, valueIsResolved = false)`)

The value validation step works with 2 inner steps:
1. Parse schema validations: traverse the schema tree and collect validations for each path
2. Execute validations against the value

Validation function comes in two fashions:
- `validate`: returns either `null` (if no errors are found) or an array of error
  specifications
- `validateThrow`: if any error is found, throws a ValidationError; otherwise simply returns
  `undefined`

# API Docs
