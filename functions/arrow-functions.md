# Arrow Functions and `this`

Unlike Java, JavaScript does not strongly bind `this`. Instead, `this` is derived from the context in which the function is called.


Here is an example that illustrates how this works with regular function syntax and arrow function syntax, including a non-working version:
```js
class Collector {
    constructor(name) {
        this.name = name;
        this.collection = [];
    }

    addItem(item) {
        this.collection.push(item);
    }

    formatItem(item) {
        return `${this.name} => ${item}`;
    }

    printItemsBroken() {
        // 1. This will break
        this.collection.forEach(function(item) {
            console.log(this.formatItem(item));
        });
    }

    printItemsSelf() {
        // 2. A solution (the pre-ES6 way)
        const self = this;
        this.collection.forEach(function(item) {
            console.log(self.formatItem(item));
        });
    }

    printItemsArrow() {
        // 3. A better solution
        this.collection.forEach(item => {
            console.log(this.formatItem(item));
        });
    }

    printItemsShortArrow {
        // 4. The simplest solution
        this.collection.forEach(item => console.log(this.formatItem(item)));
    }
}


const collector = new Collector('John Q Public');

collector.addItem('Item 1');
collector.addItem('Item 2');
collector.addItem('Item 3');

collector.printItemsBroken();
// collector.printItemsSelf();
// collector.printItemsArrow();
// collector.printItemsShortArrow();
```