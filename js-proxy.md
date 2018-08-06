Read [ExploringJS: Metaprogramming with proxies](http://exploringjs.com/es6/ch_proxies.html#sec_proxies-explained).

Experiment template:
```js
var handler = {
  // @fundamental
  defineProperty(target, propKey, propDesc) {
    // @return boolean
    // Object.defineProperty(proxy, propKey, propDesc)
    console.log('defineProperty', arguments)
    return Reflect.defineProperty(...arguments)
  },

  // @fundamental
  deleteProperty(target, propKey) {
    // @return boolean
    // delete proxy[propKey]
    // delete proxy.foo // propKey = 'foo'
    console.log('deleteProperty', arguments)
    return Reflect.deleteProperty(...arguments)
  },

  get(target, propKey, receiver) {
    // receiver[propKey]
    // receiver.foo // propKey = 'foo'
    console.log('get', arguments)
    return Reflect.get(...arguments)
  },

  // @fundamental
  getOwnPropertyDescriptor(target, propKey) {
    // @return PropDesc|Undefined
    // Object.getOwnPropertyDescriptor(proxy, propKey)
    console.log('getOwnPropertyDescriptor', arguments)
    return Reflect.getOwnPropertyDescriptor(...arguments)
  },

  // @fundamental
  getPrototypeOf(target) {
    // @return Object|Null
    // Object.getPrototypeOf(proxy)
    console.log('getPrototypeOf', arguments)
    return Reflect.getPrototypeOf(...arguments)
  },

  has(target, propKey) {
    // @return boolean
    // propKey in proxy
    console.log('has', arguments)
    return Reflect.has(...arguments)
  },

  // @fundamental
  isExtensible(target) {
    // @return boolean
    // Object.isExtensible(proxy)
    console.log('isExtensible', arguments)
    return Reflect.isExtensible(...arguments)
  },

  // @fundamental
  ownKeys(target) {
    // @return Array<PropertyKey>
    // Object.getOwnPropertyPropertyNames(proxy) (only uses string keys)
    // Object.getOwnPropertyPropertySymbols(proxy) (only uses symbol keys)
    // Object.keys(proxy) (only uses enumerable string keys; enumerability is checked via Object.getOwnPropertyDescriptor)
    console.log('ownKeys', arguments)
    return Reflect.ownKeys(...arguments)
  },

  // @fundamental
  preventExtensions(target) {
    // @return boolean
    // Object.preventExtensions(proxy)
    console.log('preventExtensions', arguments)
    return Reflect.preventExtensions(...arguments)
  },

  set(target, propKey, value, receiver) {
    // @return boolean
    // receiver[propKey] = value
    // receiver.foo = value // propKey = 'foo'
    console.log('set', arguments)
    return Reflect.set(...arguments)
  },

  // @fundamental
  setPrototypeOf(target, proto) {
    // @return boolean
    // Object.setPrototypeOf(proxy, proto)
    console.log('setPrototypeOf', arguments)
    return Reflect.setPrototypeOf(...arguments)
  },

  // @fundamental
  apply(target, thisArgument, argumentsList) {
    // proxy.apply(thisArgument, argumentsList)
    // proxy.call(thisArgument, ...argumentsList)
    // proxy(...argumentsList)
    console.log('apply', arguments)
    return Reflect.apply(...arguments)
  },

  construct(target, argumentsList, newTarget) {
    // @return Object
    // new proxy(..argumentsList)
    console.log('construct', arguments)
    return Reflect.construct(...arguments)
  }
}

var target = {}

var p = new Proxy(target, handler)
```
