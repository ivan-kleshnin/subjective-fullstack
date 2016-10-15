# Immutability

At least 7 approaches are possible here.

## 1. Defensive copying

```js
let ys = xs.slice(0, xs.length)
ys.splice(...)
```

### Benefits

Not found

### Drawbacks

* exta code
* clutter
* error-prone

## 2. Manual freezing

```js
Object.freeze({name: "Jack"})
```

### Benefits

* preserves native API

### Drawbacks

* exta code
* clutter
* error-prone

## 3. Auto freezing

[**Seamless-Immutable**](https://github.com/rtfeldman/seamless-immutable)

```
let xs = Immutable(["I'm", "immutable"]);
xs[0] = "let's verify" // error
```

### Benefits

* preserves native API

### Drawbacks

* exta code (for little gain)
* clutter

## 4. Persistent datastructures (library)

[**ImmutableJS**](https://github.com/facebook/immutable-js)

```js
let map1 = Immutable.Map({a:1, b:2, c:3});
let map2 = map1.set('b', 50);
map1.get('b'); // 2
map2.get('b'); // 50
```

[**Mori**](https://github.com/swannodette/mori)

### Benefits

* memory usage (real win)

### Drawbacks

* specific API (unnecessary hard to combine with other libs)
* negatively affects bundle size (such libs are relatively heavy)

## 4. Persistent datastructures (embedded)

Not available in JS.

## 5. Immutability as a side-effect

Some libraries can freeze data in addition to their primary actions (type validation, etc.).

[**Tcomb**](https://github.com/gcanti/tcomb)

```js
let Person = T.struct({
  name: T.String,
}, 'Person');

let person = Person({name: "John"})
person.name = "Jack" // error
```

### Benefits

* preserves native API
* immutability as a bonus

### Drawbacks

* limited usage (you can't generally get **total** immutability by this method)

## 6. "Just never mutate" (manual)

```js
xs.concat([4])
// instead of
xs.push(4)
```

### Benefits

* preserves native API
* no additional deps / conditions

### Drawbacks

* exta code
* clutter
* error-prone

You can use [**eslint-plugin-immutable**](https://github.com/jhusain/eslint-plugin-immutable) plugin to discover mutable
operations at lint time.

You can also use [**deep-freeze**](https://github.com/substack/deep-freeze) which install
tests for mutation and asserts as your tests run. Obviously you need a good coverage ;)

## 7. "Just never mutate" (library)

[**Ramda**](http://ramdajs.com/)

```js
let xs = [1, 2, 3]
let ys = drop(1, xs) // xs is untouched
```

### Benefits

* preserves native API
* immutability as a bonus

### Drawbacks

* negatively affects bundle size (effect will significantly reduce with upcoming [tree shaking](www.2ality.com/2015/12/webpack-tree-shaking.html))

Our favorites are **5** and **7**. Try **4** when you have to deal with heavy memory load (games, graphics).
Never use **1**.
