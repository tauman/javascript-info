# Getters and Setters


JavaScript has a specific syntax to enable you to create getters and setters as a specific construct. It is not madatory to use this syntax, so the "java-style" form of `getAttribute()` and `setAttribute()` can still be used:
```js
class Person {
    constructor(firstname, lastname) {
        this.firstname = firstname;
        this.lastname = lastname;
    }

    getFirstname() {
        return this.firstname;
    }

    setFirstname(firstname) {
        this.firstname = firstname;
    }

    getLastname() {
        return this.lastname;
    }

    setLastname(lastname) {
        this.lastname = lastname;
    }

    getFullname() {
        return `${this.getFirstname()} ${this.getLastname()}`;
    }
}
```

```js
class Person {
    constructor(firstname, lastname) {
        this.firstname = firstname;
        this.lastname = lastname;
    }

    get firstname() {
        return this.firstname;
    }

    set firstname(value) {
        this.firstname = value;
    }

    get lastname() {
        return this.lastname;
    }

    set lastname(value) {
        this.lastname = value;
    }

    get fullname() {
        return `${this.firstname()} ${this.lastname()}`;
    }
}
```