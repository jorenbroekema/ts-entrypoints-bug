# Typescript bug resolving entrypoints

> run `npm install`, then `npm run build` to see the error.
> Error should also be visible in the foo-test.ts file in your editor.

Given a dependency "foo" inside `node_modules/foo` with package entrypoints (aka export map):

```json
{
  "exports": {
    ".": {
      "types": "./types/foo.d.ts",
      "default": "./index.js"
    }
  }
}
```

We would expect the following to work:

```js
import { isFoo } from "foo";

const whatever = isFoo();

export { whatever };
```

However, it throws an error `'isFoo' only refers to a type, but is being used as a value here.ts(2693)` on the third line of code.

It seems like it finds the "types" conditional entrypoint and prioritizes it over the "default" entrypoint.

I expect that the compiler uses the `foo/types/foo.d.ts` file for determining the types, but uses the "value" from the `foo/index.js` file for the actual value.

This error occurs both for:

- `"module": "ESNext"` and `"moduleResolution": "bundler"`
- `"module": "NodeNext"` and `"moduleResolution": "NodeNext"`
