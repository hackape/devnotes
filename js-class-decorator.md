# On js class decorator

## Typescript's polyfill of decorate function

So it seems like js is gonna provide `Reflect.decorate` function in stdlib.
This polyfill reveal the internal of this function, check it out:

```typescript
function __decorate(decorators, target, key, desc) {
  const FLAG_DECORATE_CLASS = arguments.length < 3;
  const FLAG_DECORATE_METHOD = arguments.length > 3 && desc === null;
  const FLAG_DECORATE_PROPERTY = arguments.length > 3 && desc === undefined;

  if (FLAG_DECORATE_CLASS) {
    return decorators.reduceRight(
      (target, decorator) => decorator(target),
      target
    );
  }

  if (FLAG_DECORATE_METHOD || FLAG_DECORATE_PROPERTY) {
    desc = FLAG_DECORATE_METHOD
      ? Object.getOwnPropertyDescriptor(target, key)
      : desc;
    desc = decorators.reduceRight(
      (desc, decorator) => decorator(target, key, desc) || desc,
      desc
    );
    if (desc) Object.defineProperty(target, key, desc);
    return desc;
  }
}
```

## Transpiled code example

```typescript
@foobar
class Foo {
  @observable bar = "zoo";

  @action
  greet() {}
}
```

About code is transpiled to:

```javascript
var Foo = /** @class */ (function() {
  function Foo() {
    this.bar = "zoo";
  }
  Foo.prototype.greet = function() {};

  // FLAG_DECORATE_METHOD
  __decorate([observable], Foo.prototype, "bar", void 0);

  // FLAG_DECORATE_PROPERTY
  __decorate([action], Foo.prototype, "greet", null);

  // FLAG_DECORATE_CLASS
  Foo = __decorate([foobar], Foo);
  return Foo;
})();
```

## Ref:

- [Decorators & metadata reflection in TypeScript](http://blog.wolksoftware.com/decorators-reflection-javascript-typescript)
- [Typescript Handbook - Decorators](http://www.typescriptlang.org/docs/handbook/decorators.html)
