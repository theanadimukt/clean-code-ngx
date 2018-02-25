# clean-code-ngx

## Table of Contents
  1. [Introduction](#introduction)
  2. [Variables](#variables)
  3. [Functions](#functions)
  4. [Objects and Data Structures](#objects-and-data-structures)
  5. [Classes](#classes)


## Introduction  

[*angular-cli*](https://angular.io/docs) is written in typescript and in latest javascript. Angualr has official style guide to [Angular syntax, conventions, and application structure](https://angular.io/guide/styleguide). Which is adapted for clean code ngx. This repo is not a offical guidlines but a concept for how we write good code. 

Not every principle herein has to be strictly followed, and even fewer will be
universally agreed upon. These are guidelines and nothing more, but they are
ones codified over years of collective experience by developers.

Our craft of software engineering is just a bit over 50 years old, and we are
still learning a lot. When software architecture is as old as architecture
itself, maybe then we will have harder rules to follow. For now, let these
guidelines serve as a touchstone by which to assess the quality of the
Typescript/JavaScript code that you and your team produce.

One more thing: knowing these won't immediately make you a better software
developer, and working with them for many years doesn't mean you won't make
mistakes. Every piece of code starts as a first draft, like wet clay getting
shaped into its final form. Finally, we chisel away the imperfections when
we review it with our peers. Don't beat yourself up for first drafts that need
improvement. Beat up the code instead!

Inspired from [clean-code-javascript](https://github.com/ryanmcdermott/clean-code-javascript) and [clean-code-php](https://github.com/jupeter/clean-code-php). Thanks to [Mr. Ryan McDermott](https://github.com/ryanmcdermott) and [Mr. Piotr Plenik](https://github.com/jupeter). 

## **Variables**
### Use meaningful and pronounceable variable names with data type
#### Global Variables

If you defined variable as global variable provide control access keyword with variable data type
**Bad:**
```typescript
userid: any;
```

**Good:**
```typescript
public id: number;
protected password: string;
public primes: number[] = [10, 2, 3]; 
```

#### variables with scope
If variable has scope with function only then define it as local variable 
**Bad:**
```typescript
carid: number;

let getCar = (car) => {
  this.carid = car.id;
  // ....
}
```

**Good:**
```typescript
let getCar = (car) => {
  const id = car.id;
  // ....
}
```

### Use identical comparison
Identical comparision has more efficent then simple comparison. Identical comparison will compare type and value  
**Bad:**
```typescript
const x = '10';
const y = 10;

if( x == y ) {
  //The expression will always passes
}
```
Apparently both variable has same value but different type so in simple comparison it will return true and sometimes it leads to unexpected results 

**Good:**
```typescript
if( x === y ) {
    //The expression isn't verified
}
```

### Use the same vocabulary for the same type of variable

**Bad:**
```typescript
let getClientInfo = () => {};
let getClientData = () => {};
let getClientRecord = () => {};
```

**Good:**
```typescript
let getClient = () => {};
```

#### Use searchable names
We will read more code than we will ever write. It is important that the code we
do write is readable and searchable. By *not* naming variables that end up
being meaningful for understanding our program, we hurt our readers.
Make your names searchable. Tools like
[ESLint](https://github.com/eslint/eslint/blob/660e0918933e6e7fede26bc675a0763a6b357c94/docs/rules/no-magic-numbers.md)
can help identify unnamed constants.

**Bad:**
```typescript
// What the heck is 1 for?
if (user.access && 4) {
  ...
}
```

**Good:**
```typescript
class User {
  public ACCESS: object = {
    READ: 1,
    CREATE: 2,
    UPDATE: 4,
    DELETE: 8
  };
}

if (user.access && this.ACCESS.UPDATE) {
    ...
}
```

### Don't add unneeded context
If your class/object name tells you something, don't repeat that in your
variable name.

**Bad:**
```typescript
let Car = {
  carMake: 'Honda',
  carModel: 'Accord',
  carColor: 'Blue'
};

let paintCar = (car) => {
  car.carColor = 'Red';
}
```

**Good:**
```typescript
let Car = {
  make: 'Honda',
  model: 'Accord',
  color: 'Blue'
};

let paintCar = (car) => {
  car.color = 'Red';
}
```

### Use default arguments instead of short circuiting or conditionals
Default arguments are often cleaner than short circuiting. Be aware that if you use them, your function will only provide default values for `undefined`
arguments. Other "falsy" values such as `''`, `""`, `false`, `null`, `0`, and
`NaN`, will not be replaced by a default value.

**Bad:**
```typescript
let countryCode = (code) => {
  const code = code || '+01';
  ...
}
```

**Good:**
```typescript
let countryCode = (code = '+01') => {
  ...
}
```

**[⬆ back to top](#tabl2e-of-contents)**

## **Functions**
### Function arguments type and return type 

you have to passed argument type and return type to function where its necessary
```typescript
let sumAmount = function(x: number, y: number): number { return x + y; };
```

### Function arguments (2 or fewer ideally)

Number of argument parameters is apparently gives you convenient way to interact data or makes testing with function easily. Having more than three leads to a
combinatorial explosion where you have to test tons of different cases with
each separate argument.

One or two arguments is the ideal case, and three should be avoided if possible.
Anything more than that should be consolidated. Usually, if you have
more than two arguments then your function is trying to do too much. In cases
where it's not, most of the time a higher-level object will suffice as an
argument.

Since Typescript allows you to make objects on the fly, without a lot of class
boilerplate, you can use an object if you are finding yourself needing a
lot of arguments.

To make it obvious what properties the function expects, you can use the ES2015/ES6
destructuring syntax. This has a few advantages:

1. When someone looks at the function signature, it's immediately clear what
properties are being used.
2. Destructuring also clones the specified primitive values of the argument
object passed into the function. This can help prevent side effects. Note:
objects and arrays that are destructured from the argument object are NOT
cloned.
3. Linters can warn you about unused properties, which would be impossible
without destructuring.

**Bad:**
```typescript
let showMenu = (title, body, buttonText, cancellable) => {
  ...
}
```

**Good:**
```typescript
let showMenu = (options: object) => {
  ...
}

let options = {
  title: 'Foo',
  body: 'Bar',
  buttonText: 'Baz',
  cancellable: true
}

showMenu(options);
```

### Functions should do one thing
When functions do more than one thing, they are harder to compose, test, and reason about.
When you can isolate a function to just one action, they can be refactored
easily and your code will read much cleaner. If you take nothing else away from
this guide other than this, you'll be ahead of many developers.

**Bad:**
```typescript
let emailClients = (clients) => {
  clients.forEach((client) => {
    let record = database.lookup(client);
    if (record.isActive()) {
      email(client);
    }
  }
}
```

**Good:**
```typescript
let emailActiveClients = (clients) => {
  clients
    .filter(isActiveClient)
    .forEach(email);
}

let isActiveClient = (client) => {
  let record = database.lookup(client);
  return record.isActive();
}
```

### Function names should say what they do

**Bad:**
```typescript
let fetchList = (parent) => {
  ...
}

// It's hard to tell from the function name what is fetched
this.fetchList('document');
```

**Good:**
```typescript
let fetchFoldersList = (parent) => {
  ...
}
```

### Avoid nesting too deeply and return early
Explicit is better than implicit.

**Bad:**
```typescript
let isHoliday = (day) => {
  if (day) {
    if (typeof day === 'string') {
      day = day.toLowerCase();
      if (day === 'saturday') {
        return true;
      } else if (day === 'sunday') {
        return true;
      } else {
        return false;
      }
    } else {
      return false;
    }
  } else {
    return false;
  }
}

```

**Good:**
```typescript
let isHoliday = (day: string): boolean => {
  if (!day) {
   return false; 
  }
  const holidays = ['saturday', 'sunday'];

  return (holidays.some(x => x === day.toLowerCase())) ? true : false;
}
```

### Functions should only be one level of abstraction
When you have more than one level of abstraction. Make code to reusable and flexible.

**Bad:**
```typescript
let parseBetterJSAlternative = (code) => {
  const REGEXES = [
    // ...
  ];

  const statements = code.split(' ');
  let tokens = [];
  REGEXES.forEach((REGEX) => {
    statements.forEach((statement) => {
      // ...
    });
  });

  let ast = [];
  tokens.forEach((token) => {
    // ...
  });

  ast.forEach((node) => {
    // ...
  });
}
```

**Good:**
```typescript
let tokenize = (code) => {
  const REGEXES = [
    // ...
  ];

  let tokens = [];
  REGEXES.forEach((REGEX) => {
    code.split(' ').forEach((statement) => {
      tokens.push( /* ... */ );
    });
  });
  return tokens;
}

let lexer = (tokens) => {
  let ast = [];
  tokens.forEach((token) => {
    ast.push( /* ... */ );
  });
  return ast;
}

let parseBetterJSAlternative = (code) => {
  let tokens = this.tokenize(code);
  let ast = this.lexer(tokens);
  ast.forEach((node) => {
    // parse...
  });
}
```

### Remove duplicate code
Do your absolute best to avoid duplicate code. Duplicate code is bad because it
means that there's more than one place to alter something if you need to change
some logic.

Imagine if you run a restaurant and you keep track of your inventory: all your
tomatoes, onions, garlic, spices, etc. If you have multiple lists that
you keep this on, then all have to be updated when you serve a dish with
tomatoes in them. If you only have one list, there's only one place to update!

Oftentimes you have duplicate code because you have two or more slightly
different things, that share a lot in common, but their differences force you
to have two or more separate functions that do much of the same things. Removing
duplicate code means creating an abstraction that can handle this set of
different things with just one function/module/class.

Getting the abstraction right is critical, that's why you should follow the
SOLID principles laid out in the *Classes* section. Bad abstractions can be
worse than duplicate code, so be careful! Having said this, if you can make
a good abstraction, do it! Don't repeat yourself, otherwise you'll find yourself
updating multiple places anytime you want to change one thing.

**Bad:**
```typescript
let showDevelopersList = (developers) => {
  developers.forEach((developer) => {
    let salary = developer.calculateSalary();
    let experience = developer.getExperience();
    let projects = developer.getExecutedProjects();
    let data = {
      salary: salary,
      experience: experience,
      projects: projects
    };
    render(data);
  });
}

let showManagerList = (managers) => {
  managers.forEach((manager) => {
    let salary = manager.calculateSalary();
    let experience = manager.getExperience();
    let projects = developer.getManagedProjects();
    let data = {
      salary: salary,
      experience: experience,
      projects: projects
    };
    render(data);
  });
}
```

**Good:**
```typescript
let showEmployeeList = (employees) => {
  employees.forEach((employee) => {
    let salary = employee.calculateSalary();
    let experience = employee.getExperience();
    let data = {
      salary: salary,
      experience: experience
    };

    switch (employee.type) {
      case 'manager':
        data.project = employee.getManagedProjects();
        break;
      case 'developer':
        data.project = employee.getExecutedProjects();
        break;
    }
    render(data);
  });
}
```

### Set default objects with Object.assign

**Bad:**
```typescript
let configurations = {
  host: null,
  user: null,
  password: null,
  dbname: 'database1'
};

let connection = (config: object) => {
  config.host = config.host || 'localhost';
  config.user = config.user || 'root';
  config.password = config.password || '123';
  config.dbname = config.dbname || 'defaultDB';
}

this.connection(configurations);
```

**Good:**
```typescript
let configurations = {
  dbname: 'database_new'
};

let connection = (config: object) => {
  config = Object.assign({
    host: 'localhost',
    user: 'root',
    password: '123',
    dbname: 'defaultDB'
  }, config);

  // config now equals: {host: "localhost", user: "root", password: "123", dbname: "defaultDB"}
  // ...
}

this.connection(configurations);
```

### Don't use flags as function parameters
Sometimes flags uses in function argument to perform different action based on flag. But function should do one thing. Spit funtions by thier different code paths based on flags

**Bad:**
```typescript
let createFile = (name, temp) => {
  if (temp) {
    fs.create(`./temp/${name}`);
  } else {
    fs.create(name);
  }
}
```

**Good:**
```typescript
let createFile = (name) => {
  fs.create(name);
}

let createTempFile = (name) => {
  createFile(`./temp/${name}`);
}
```

### Avoid Side Effects (part 1)
A function produces a side effect if it does anything other than take a value in
and return another value or values. A side effect could be writing to a file,
modifying some global variable, or accidentally wiring all your money to a
stranger.

Now, you do need to have side effects in a program on occasion. Like the previous
example, you might need to write to a file. What you want to do is to
centralize where you are doing this. Don't have several functions and classes
that write to a particular file. Have one service that does it. One and only one.

The main point is to avoid common pitfalls like sharing state between objects
without any structure, using mutable data types that can be written to by anything,
and not centralizing where your side effects occur. If you can do this, you will
be happier than the vast majority of other programmers.

**Bad:**
```typescript
// Global variable referenced by following function.
// If we had another function that used this name, now it'd be an array and it could break it.
let name = 'Ryan McDermott';

let splitIntoFirstAndLastName = () => {
  name = name.split(' ');
}

splitIntoFirstAndLastName();

console.log(name); // ['Ryan', 'McDermott'];
```

**Good:**
```typescript
let splitIntoFirstAndLastName = (name) => {
  return name.split(' ');
}

const name = 'Ryan McDermott';
const newName = splitIntoFirstAndLastName(name);

console.log(name); // 'Ryan McDermott';
console.log(newName); // ['Ryan', 'McDermott'];
```

### Avoid Side Effects (part 2)
In TypeScript, primitives are passed by value and objects/arrays are passed by
reference. In the case of objects and arrays, if your function makes a change
in a shopping cart array, for example, by adding an item to purchase,
then any other function that uses that `cart` array will be affected by this
addition. That may be great, however it can be bad too. Let's imagine a bad
situation:

The user clicks the "Purchase", button which calls a `purchase` function that
spawns a network request and sends the `cart` array to the server. Because
of a bad network connection, the `purchase` function has to keep retrying the
request. Now, what if in the meantime the user accidentally clicks "Add to Cart"
button on an item they don't actually want before the network request begins?
If that happens and the network request begins, then that purchase function
will send the accidentally added item because it has a reference to a shopping
cart array that the `addItemToCart` function modified by adding an unwanted
item.

A great solution would be for the `addItemToCart` to always clone the `cart`,
edit it, and return the clone. This ensures that no other functions that are
holding onto a reference of the shopping cart will be affected by any changes.

Two caveats to mention to this approach:
  1. There might be cases where you actually want to modify the input object,
but when you adopt this programming practice you will find that those cases
are pretty rare. Most things can be refactored to have no side effects!

  2. Cloning big objects can be very expensive in terms of performance. Luckily,
this isn't a big issue in practice because there are
[great libraries](https://facebook.github.io/immutable-js/) that allow
this kind of programming approach to be fast and not as memory intensive as
it would be for you to manually clone objects and arrays.

**Bad:**
```typescript
let addItemToCart = (cart, item) => {
  cart.push({ item, date: Date.now() });
};
```

**Good:**
```typescript
let addItemToCart = (cart, item) => {
  return [...cart, { item, date: Date.now() }];
};
```

### Favor functional programming over imperative programming
Typescript/JavaScript isn't a functional language in the way that Haskell is, but it has
a functional flavor to it. Functional languages can be cleaner and easier to test.
Favor this style of programming when you can.

**Bad:**
```typescript
const programmerOutput = [
  {
    name: 'Uncle Bobby',
    linesOfCode: 500
  }, {
    name: 'Suzie Q',
    linesOfCode: 1500
  }, {
    name: 'Jimmy Gosling',
    linesOfCode: 150
  }, {
    name: 'Gracie Hopper',
    linesOfCode: 1000
  }
];

let totalOutput = 0;

for (let i = 0; i < programmerOutput.length; i++) {
  totalOutput += programmerOutput[i].linesOfCode;
}
```

**Good:**
```typescript
const programmerOutput = [
  {
    name: 'Uncle Bobby',
    linesOfCode: 500
  }, {
    name: 'Suzie Q',
    linesOfCode: 1500
  }, {
    name: 'Jimmy Gosling',
    linesOfCode: 150
  }, {
    name: 'Gracie Hopper',
    linesOfCode: 1000
  }
];

const totalOutput = programmerOutput
  .map(output => output.linesOfCode)
  .reduce((totalLines, lines) => totalLines + lines);
```
### Encapsulate conditionals

**Bad:**
```typescript
if (user.access === 'edit' && this.authenticate(key, user.hash)) {
  ...
}
```

**Good:**
```typescript
let accessMenu = (user, key) => {
  return user.access === 'edit' && this.authenticate(key, user.hash);
}

if (this.accessMenu()) {
  ...
}
```

### Avoid negative conditionals

**Bad:**
```typescript
let isDOMNodeNotPresent = (node) => {
  // ...
}

if (!isDOMNodeNotPresent(node)) {
  // ...
}
```

**Good:**
```typescript
let isDOMNodePresent = (node) => {
  // ...
}

if (isDOMNodePresent(node)) {
  // ...
}
```

### Avoid duplication, Make common

**Bad:**
```typescript
export class Missile {
  // ...
  let getMaxAltitude () => {}
  let getAltitude = () => {
    return this.getMaxAltitude();
  }
}

export class Airplane {
  // ...
  let getMaxAltitude () => {}
  let getCruisingAltitude = () => {
    switch (this.type) {
      case '777':
        return this.getMaxAltitude() - this.getPassengerCount();
      case 'Air Force One':
        return this.getMaxAltitude();
    }
  }
}
```
Make one common service and placed all reusable and common function into that service
**Good:**
```typescript
// common.service.ts
@Injectable()
export class Common {
  // ...
  let getMaxAltitude = () => {}
}

export class Airplane {
  constructor(private common: Common) {}
  // ...
  getCruisingAltitude() {
    return this.common.getMaxAltitude() - this.getPassengerCount();
  }
}

export class Missile {
  constructor(private common: Common) {}
  // ...
  getCruisingAltitude() {
    return this.common.getMaxAltitude()
  }
}
```

### Avoid type-checking (part 1)
TypeScript is untyped, which means your functions can take any type of argument.
Sometimes you are bitten by this freedom and it becomes tempting to do
type-checking in your functions. There are many ways to avoid having to do this.
The first thing to consider is consistent APIs.

**Bad:**
```typescript
let travelToTexas = (vehicle) => {
  if (vehicle instanceof Bicycle) {
    vehicle.pedal(this.currentLocation, new Location('texas'));
  } else if (vehicle instanceof Car) {
    vehicle.drive(this.currentLocation, new Location('texas'));
  }
}
```

**Good:**
```typescript
let travelToTexas = (vehicle) => {
  vehicle.move(this.currentLocation, new Location('texas'));
}
```

### Avoid type-checking, pass type in arguments (part 2)
If you are working with basic primitive values like strings and integers,
and you can't use polymorphism but you still feel the need to type-check,
you should consider using TypeScript. We can add types to each of the parameters and then to the function itself to add a return type.

**Bad:**
```typescript
let combine = (val1, val2) => {
  if (typeof val1 === 'string' && typeof val2 === 'string') {
    return val1 + val2;
  }

  throw new Error('Must be of type String or Number');
}
```

**Good:**
```typescript
let combine = (val1: string, val2: string): string => { return val1 + val2; };
```

### Remove dead code & unwanted debug script
Dead code and debug code is just as bad as duplicate code. There's no reason to keep it in
your codebase. If it's not being called, get rid of it! It will still be safe
in your version history if you still need it.

**Bad:**
```typescript
let oldRequestModule = (url) => {
  // ...
}

let newRequestModule = (url) => {
  console.log('URL', url);
  // ...
}

const req = newRequestModule;
debugger;
let inventoryTracker = ('apples', req, 'www.inventory-awesome.io') => {};

```

**Good:**
```typescript
let newRequestModule = (url) => {
  // ...
}

const req = newRequestModule;
let inventoryTracker = ('apples', req, 'www.inventory-awesome.io') => {};
```

**[⬆ back to top](#table-of-contents)**

## **Objects and Data Structures**
### Use getters and setters
Using getters and setters to access data on objects could be better than simply
looking for a property on an object. "Why?" you might ask. Well, here's an
unorganized list of reasons why:

* When you want to do more beyond getting an object property, you don't have
to look up and change every accessor in your codebase.
* Makes adding validation simple when doing a `set`.
* Encapsulates the internal representation.
* Easy to add logging and error handling when getting and setting.
* You can lazy load your object's properties, let's say getting it from a
server.


**Bad:**
```typescript
class Bank {
  let makeBankAccount = () => {
    // ...
    return {
      balance: 0,
    };
  }
}

let bank = new Bank();
const account = bank.makeBankAccount();
account.balance = 100;

```

**Good:**
```typescript

class Bank {
  private _account: object;

  get balance(): object {
      return this._account;
  }

  set balance(amount: number) {
    this._account.balance = amount;
  }
}

let bank = new Bank();
const account = bank.makeBankAccount();
account.balance(100);

```

### Make objects have private members
This can be accomplished through closures (for ES5 and below).

**Bad:**
```typescript

let Employee = (name: string) => {
  this.name = name;
};

Employee.prototype.getName = function getName() {
  return this.name;
};

let employee = new Employee('John Doe');
console.log(`Employee name: ${employee.getName()}`); // Employee name: John Doe
delete employee.name;
console.log(`Employee name: ${employee.getName()}`); // Employee name: undefined
```

**Good:**
```typescript
let makeEmployee = (name) => {
  return {
    getName() {
      return name;
    },
  };
}

let employee = this.makeEmployee('John Doe');
console.log(`Employee name: ${employee.getName()}`); // Employee name: John Doe
delete employee.name;
console.log(`Employee name: ${employee.getName()}`); // Employee name: John Doe
```
**[⬆ back to top](#table-of-contents)**

## **Classes**
### Use method chaining
This pattern is very useful in TypeScript and you see it in many libraries such
as jQuery and Lodash. It allows your code to be expressive, and less verbose.
For that reason, I say, use method chaining and take a look at how clean your code
will be. In your class functions, simply return `this` at the end of every function,
and you can chain further class methods onto it.

**Bad:**
```typescript
class Car {
  public color: string;
  
  let setColor = (color) => {
    this.color = color;
  }

  let save = () => {
    console.log(this.color);
  }
}

const car = new Car();
car.setColor('pink');
car.save();
```

**Good:**
```typescript

const car = this.car.setColor('pink').save();
```
**[⬆ back to top](#table-of-contents)**
