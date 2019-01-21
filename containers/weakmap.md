# A Use for WeakMap

In addition to Map, ES6 introduced the WeakMap class. Only objects can be used as keys for a WeakMap (although the values can be objects and primitives). The keys are weakly referenced, which means that they are eligible for garbage collection (and thus removal from the WeakMap) if there are no non-strong references to the key object.

Here is an example with some explanatory comments:  
```js
const wmap = new WeakMap();

// strong reference to the key
let key = { name: 'Steve' };

// this cannot be reclaimed until there is no strong reference to the 'key' object
wmap.set(key, 'a value');

// the entry in the WeakMap 'wmap' can now be reclaimed by garbage collection and removed from 'wmap'
key = null;
```

Now using the WeakMap (and closures), we can implement a class with true private members. In this case, we have made the 'name' field private and only provided an accessor (i.e. a 'getter') to access it. With this implementation, the name field cannot be accessed outside of this file (discounting special modules, such as `rewire`):
```js
const privateData = new WeakMap();

class Person {
	constructor(name) {
		privateData.set(this, { name });
	}

	getName() {
		return privateData.get(this).name;
	}

	toString() {
		return `Person { name: ${this.getName()} }`;
	}
}

module.exports = Person;
```

Since the key is `this`, the WeakMap will hold the reference to the value object for as long as there is a reference to that instance of Person. However, when the Person instance is no longer referenced, the object held in the WeakMap that is keyed to that `this` is eligible for garbage collection.

Here is another example that uses the `get` syntax that exposes `name` as a read-only attribute:
```js
const privateData = new WeakMap();

class Person {
	constructor(name) {
		privateData.set(this, { name });
	}

	get name() {
		return privateData.get(this).name;
	}

	toString() {
		return `Person { name: ${this.name} }`;
	}
}

module.exports = Person;
```
