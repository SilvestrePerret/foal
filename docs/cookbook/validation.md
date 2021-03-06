# Validation

Data sent from client side cannot be trusted. This document shows you different tools to perform server side input validation.

## Ajv, the JSON Schema Validator

FoalTS validation is based on [Ajv](https://github.com/epoberezkin/ajv), a fast JSON Schema Validator. You'll find more details on how to define a shema on its [website](http://epoberezkin.github.io/ajv/). 


## The `validate` util

The `validate` util throws a `ValidationError` if the given data does not fit the shema.

*Example*
```typescript
import { validate } from '@foal/core';

const schema ={
  properties: {
    a: { type: 'number' }
  }
  type: 'object'
};
const data = {
  a: 'foo'
};

validate(schema, data);
// => Throws an error (ValidationError)
// => error.content contains the details of the validation error.
```

## The `ValidateBody` and `ValidateQuery` hooks

`ValidateBody` and `ValidateQuery` are hooks to control the body and the query of the requests received by the server. They validate the `context.request.body` with the given schema. If the validation fails then an `HttpResponseBadRequest` is returned with the validation errors as `content`.

Note: You can provide your own instance of Ajv.

*Example*:
```typescript
@Controller()
export class MyController {

  @Post('/user')
  @ValidateBody({
    additionalProperties: false,
    properties: {
      firstName: { type: 'string' },
      lastName: { type: 'string' },
    }
    required: [ 'firstName', 'lastName' ],
    type: 'object'
  })
  postUser(ctx: Context) {
    // In this function we are sure that firstName and lastName
    // are defined thanks to the above hook.
    console.log(ctx.request.body.firstName, ctx.request.body.lastName);
  }

}

```


