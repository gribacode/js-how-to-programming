# Data Structure in Javascript

## Stack and Queue

**Queues** follow the First In First Out pattern. In short: FIFO - first in, first out

![fifo](./assets/fifo.png)

```js
const queueArray = [];
queueArray.push(6);
queueArray.push(8);

// queueArray = [6, 8]

queueArray.shift();

// queueArray = [8]
```

**Stacks** are linear data structures, they follow Last In Firs Out pattern. In short: LIFO - last in, first out

![lifo](./assets/lifo.png)

```js
const stackArray = [];
stackArray.push(4);
stackArray.push(12);
stackArray.push(8);

// stackArray = [4, 12, 8]

stackArray.pop();

// stackArray = [4, 12]
```

---

## Immutable

Immutable is an object whose state cannot be changed after creation. The result of modifying an immutable object will be a new object

Ex, method slice returns a shallow copy of a portion of an array into a new array

```js
const immutableArray = [2, 8, 6, 12, 16, 4];
const slicedArray = immutableArray.slice(0, 4);

// slicedArray = [2, 8, 6, 12]
// immutableArray = [2, 8, 6, 12, 16, 4]
```

But methods: pop, push, shift, unshift, sort, reverse — modify an current array

```js
const mutableArray = [2, 8, 6, 12, 16, 4];
const reversedArray = mutableArray.reverse();

// reversedArray = [4, 16, 12, 6, 8, 2]
// mutableArray = [4, 16, 12, 6, 8, 2]
```

How to clone an _array_ and keep the immutability property?

```js
const array = [4, 12, 8, 24];
const clonedArray = [...array];
```

How to clone an _object_ and keep the immutability property?

```js
const immutableObject = {
  a: 12,
  b: 16,
  c: 8,
  d: 4,
};

// clone with spread
const clonedObject = { ...immutableObject };

// clone with Object.assign
const clonedObject = Object.assign({}, immutableObject);

// clone with Object.freeze
Object.freeze(immutableObject);
const modufiedObject = immutableObject;
modifiedObject.b = 2;

// clone with custom function
function shallowCopy(objForShallowCopy) {
  const clonedObject = {};

  for (let key in objForShallowCopy) {
    clonedObject[key] = objForShallowCopy[key];
  }

  return clonedObject;
}
```

When cloning an object with `Object.freeze()` in `use strict` mode app will crash with an error, in normal mode mutation will not work

`Object.freeze()`, `spread operator`, `Object.assign()`, `shallowCopy()`, works on _shallow copy_. I wrote a deep copy function with `Object.freeze()`:

```js
function deepFreeze(objForDeepFreeze) {
  const propItems = Object.getOwnPropertyNames(objForDeepFreeze);

  propItems.forEach(function (item) {
    const prop = objForDeepFreeze[item];

    if (prop !== null && typeof prop === "object") {
      deepFreeze(prop);
    }
  });

  return Object.freeze(objForDeepFreeze);
}
```

When cloning an object with `deepFreeze()` in `use strict` mode app will crash with an error, in normal mode mutation will not work

`shallowCopy()` improvements for deep copying:

1 option:

```js
function deepCopy(objForDeepCopy) {
  const clonedObject = Object.assign({}, objForDeepCopy);

  const propItems = Object.keys(clonedObject);

  propItems.forEach(function (key) {
    const prop = objForDeepCopy[key];

    if (prop !== null && typeof prop === "object") {
      clonedObject[key] = deepCopy(prop);
    } else {
      clonedObject[key] = prop;
    }
  });

  return clonedObject;
}
```

2 option:

```js
function deepCopy(objForDeepCopy) {
  const clonedObject = { ...objForDeepCopy };

  Object.keys(clonedObject).forEach(
    (key) =>
      (clonedObject[key] =
        objForDeepCopy[key] !== null && typeof objForDeepCopy[key] === "object"
          ? deepCopy(objForDeepCopy[key])
          : objForDeepCopy[key])
  );

  return clonedObject;
}
```

3 option:

```js
function deepCopy(objForDeepCopy) {
  const clonedObject = {};

  for (let key in objForDeepCopy) {
    if (objForDeepCopy[key] !== null && objForDeepCopy[key] instanceof Object) {
      clonedObject[key] = deepCopy(objForDeepCopy[key]);
      continue;
    }
    clonedObject[key] = objForDeepCopy[key];
  }

  return clonedObject;
}
```
