# ES6 Container Classes

## Contents
- [`Set`](#section1)
- [`Map`](#section2)


<div id="section1"/>

## `Set`

The `Set` class holds a collection of unique objects held in the order that they are added. Object equivalence is determined by using the `Object.is()` method.

```js
const JOE = 'Joe';
const TOM = 'Tom';
const MIKE = 'Mike';
const AARON = 'Aaron';

// Note that Sets can contain any type of object or
// or primitive value including 'undefined' and 'null'

// Create a set from an array
const set = new Set([JOE, TOM, MIKE]);

// Test if set contains a value:
set.has(JOE); // <- be careful with objects

// Remove a member
set.delete(JOE);

// Add a member
set.add(AARON);

// remove all members
set.clear();

// return an iterator of all members
set.values();

// iterate over the values() using for-of
for(const value of set.values()) {
    console.log(value);
}

// execute provided function once for each member
// note that 'member1' and 'member2' are the same value
// and that 'set' refers to the set itself
set.forEach((member1, member2, setSelf) => console.log(member1));
```

Since instances of `Set` are iterable objects, you can easily convert a Set into an array using the spread operator, which allows you to use the various Array functions such as `map()`, et al.:
```js
const JOE = 'Joe';
const TOM = 'Tom';
const MIKE = 'Mike';
const AARON = 'Aaron';

const set = new Set([JOE, TOM, MIKE]);

// return an array containing all of the items in `set` converted to upper case.
const upperCaseNames = [...set].map(name => name.toUpperCase());
```

<div id="section2"/>

## `Map`

The `Map` container class allows storage of items keyed to an object of any type, in contrast to objects that only allow attributes to be keyed on strings. Although object literals can serve well as container classes, if you are adding and removing keys, it will probably be advantageous to use the new `Map` class for reasons of performance and flexibility. The keys are maintained in the order that they are added and key eqivalence is determined using the `Object.is()` method.

```js
const JOE = 'Joe';
const TOM = 'Tom';
const MIKE = 'Mike';
const AARON = 'Aaron';

// Note that Maps can contain any type of object or
// primitive value and the key can also be any type of object
// or primitive value, including 'undefined' and 'null'

// Create a set from an array of 2 element arrays
const map = new Map([[JOE, 'good'], [TOM, 'fair'], [MIKE, 'bad']]);

// Test if set contains a key:
map.has(JOE); // <- be careful with objects as keys

// Remove a member based on the key:
map.delete(JOE);

// Add a member
map.set(AARON, 'good');

// remove all members
map.clear();

// return an iterator of all keys
map.keys();

// iterate over the keys() using for-of
for(const key of map.keys()) {
    console.log(key);
}

// return an iterator of all members
map.values();

// iterate over the values() using for-of
for(const value of map.values()) {
    console.log(value);
}

// return an iterator of entries as [key, value] pairs
map.entries();

// iterate over the entries() using for-of
for(const entry of map.entries()) {
    console.log(entry);
}

// execute provided function once for each member
// note that 'map' refers to the map itself
map.forEach((key, value, mapSelf) => console.log(key + '=>' + value));
```

As with `Set`, instances of Map are iterable objects. Converting a `Map` to an array will result in an array of two-element arrays identical to the array format expected by the constructor:
```js
const JOE = 'Joe';
const TOM = 'Tom';
const MIKE = 'Mike';
const AARON = 'Aaron';

const map = new Map([[JOE, 'good'], [TOM, 'fair'], [MIKE, 'bad'], [AARON, 'good']]);

// return an array containing all of the items in `set` converted to upper case.
const goodNames = [...map].filter(([name, rating]) => rating === 'good');
```
