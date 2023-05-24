# Typescript-Summary

# Ref:

-[Jonas course]

(1) : https://github.com/jonasschmedtmann/complete-javascript-course/blob/master/14-OOP/final/script.js

(2) : https://www.udemy.com/course/the-complete-javascript-course/

-[TypeScript Tutorial]: https://www.typescripttutorial.net/

## OOP in Javascript

### 1. `Constructor Functions` and `new` Operator

---

**What happens after we execute the following code:**

```javascript
const Person = function (firstName, birthYear) {
  this.firstName = firstName;
  this.birthYear = birthYear;

  //Never to this! Because whenever create a new Person -> method of instance
  this.calcAge = function () {
    console.log(2037 - this.birthYear);
  };
  const vanhien = new Person("TVH", 1998);
};
//Should be:
Person.calcAge = function () {
  console.log(2037 - this.birthYear);
};
```

```
1. New {} is created
2. function is called, this = {}
3. {} linked to prototype
4. function automatically return {}
```

---

### 2. `Prototype` && `__proto__`

---

(1) CF.`prototype` === instanceof.`__proto__`

(2) CF.`prototype`.`constructor` === CF

(3) instanceof.`__proto__`.`__proto__` === CF.`__proto__`

(4) instanceof.`__proto__`.`__proto__` === Object.`prototype` (top of prototype chain)

(4) instanceof.`__proto__`.`__proto__`.`__proto__` === null

(5) Array.`__proto__`.`__proto__`.`__proto__` === null

(6) Function`__proto__`.`__proto__`.`__proto__` === null

> > > > > > > > > > ![Xinchao](./assets/prototype1.png)

---

### 3. `Prototypal Inheritance` on Built-In Objects

```javascript
Array.prototype.unique = function () {
  return [...new Set(this)];
};

[1, 1, 3].unique(); // [1,3], Because [1,1,3] is instance of Array.
```

> > > > > > > > > > ![Xinchao](./assets/prototype2.png)

### 4. `ES6 Class`

---

ES6 Class is `Syntactic Sugar` and work **exactly** like constructor functions.
There are a few points to note:

- Method in Class will be added to `prototype` property:

> > > > > > > > > > ![Xinchao](./assets/prototype3.png)

- `set` & `get` on class:

  - `get` on class:

    > > > > > > > > > ![Xinchao](./assets/prototype4.png)

  - `set` any value on class:

    > > > > > > > > > ![Xinchao](./assets/prototype5.png)

  - `set` a property that already exists on class:

    > > > > > > > > > ![Xinchao](./assets/prototype6.png)
    > > > > > > > > >
    > > > > > > > > > > > > > > > > ![Xinchao](./assets/arrow-down.png)
    > > > > > > > > >
    > > > > > > > > > ![Xinchao](./assets/prototype7.png)

  - `set` syntax:
    > instanceof.setValue `=` value

- Classes are NOT `hoisted`
- Classes are first-class citizens
- Classes are executed in strict mode

### 5. `Object.create`

- `Property` and `Method` of class || obj will be added `.prototype` of obj which is created by Object.create()
  > > > > > > > > > ![Xinchao](./assets/prototype8.png)
- > instanceof.`__proto__` === `Object`
- Can do like this:

  ```javascript
  // Object.create
  const PersonProto = {
    calcAge() {
      console.log(2037 - this.birthYear);
    },

    init(firstName, birthYear) {
      this.firstName = firstName;
      this.birthYear = birthYear;
    },
  };
  const steven = Object.create(PersonProto);
  console.log(steven); // {}
  steven.name = "Steven";
  steven.birthYear = 2002;
  steven.calcAge();
  ```

### 6. `Inheritance` Between "Classes": `Constructor Functions`

```javascript
const Person = function (firstName, birthYear) {
  this.firstName = firstName;
  this.birthYear = birthYear;
};

// Prototype of instance's Person
Person.prototype.calcAge = function () {
  console.log(2037 - this.birthYear);
};

const Student = function (firstName, birthYear, course) {
  //this.firstName = firstName;
  //this.birthYear = birthYear; //bad practice
  Person.call(this, firstName, birthYear); // should have knowledge about call/bind
  this.course = course;
};

// Linking prototypes
Student.prototype = Object.create(Person.prototype);
//Don't do like this
//Student.prototype = Person.prototype;

// Prototype of instance's Student
Student.prototype.introduce = function () {
  console.log(`My name is ${this.firstName} and I study ${this.course}`);
};
```

![Xinchao](./assets/prototype9.png)

### 7. `Inheritance` Between "Classes": `ES6 Classes`

#### 7.1 TypeScript `Access Modifiers`

- TypeScript provides three access modifiers to class properties and methods: private, protected, and public.

- The private modifier allows access `within` the same class.

- The protected modifier allows access `within` the same class and subclasses.

- The public modifier allows access from any location.

```typescript
class Person {
  private ssn: string;
  private firstName: string;
  private lastName: string;

  constructor(ssn: string, firstName: string, lastName: string) {
    this.ssn = ssn;
    this.firstName = firstName;
    this.lastName = lastName;
  }

  getFullName(): string {
    //access `within` the same class
    return `${this.firstName} ${this.lastName}`;
  }
}

let person = new Person("153-07-3130", "John", "Doe");
console.log(person.ssn); // compile error
```

#### 7.2 TypeScript `readonly`

```typescript
class Person {
  readonly birthDate: Date;

  constructor(birthDate: Date) {
    this.birthDate = birthDate;
  }
}

let person = new Person(new Date(1990, 12, 25));
person.birthDate = new Date(1991, 12, 25); // Compile error
```

- Use the readonly access modifier to mark a class property as immutable.
- A readonly property must be initialized as a part of the declaration or in the constructor of the same class.

  |                | Second Header                                              | First Header       |
  | -------------- | ---------------------------------------------------------- | ------------------ |
  | Use for        | Class properties                                           | Variables          |
  | Initialization | In the declaration or in the constructor of the same class | In the declaration |

#### 7.3 TypeScript `Inheritance`

- Use the extends keyword to allow a class to inherit from another class.

- Use `super()` in the constructor of the child class to call the constructor of the parent class. Also, use the super.methodInParentClass() syntax to invoke the methodInParentClass() in the method of the child class.

  ```typescript
  class Employee extends Person {
    constructor(firstName: string, lastName: string, private jobTitle: string) {
      //the same Person.call(this,firstName, lastName )
      super(firstName, lastName);
    }

    describe(): string {
      return super.describe() + `I'm a ${this.jobTitle}.`;
    }
  }
  ```

#### 7.4 TypeScript `Static`

> Cl.prototype.constructor.`staticMethodInHere()` === Cl.`staticMethodInHere();`

- Static properties and methods are shared by all instances of a class.

- Use the static keyword before a property or a method to make it static.

#### 7.5 TypeScript `Abstract Classes`

```typescript
abstract class Employee {
  constructor(private firstName: string, private lastName: string) {}

  //An Abstract class has at least one abstract method.
  abstract getSalary(): number;

  get fullName(): string {
    return `${this.firstName} ${this.lastName}`;
  }

  compensationStatement(): string {
    return `${this.fullName} makes ${this.getSalary()} a month.`;
  }
}
```

- Abstract classes cannot be instantiated.

```typescript
let employee = new Employee("John", "Doe");
//error TS2511: Cannot create an instance of an abstract class.
```

- To use an abstract class, you need to inherit it and provide the implementation for the abstract methods.

```typescript
class FullTimeEmployee extends Employee {
  constructor(firstName: string, lastName: string, private salary: number) {
    super(firstName, lastName);
  }
  getSalary(): number {
    return this.salary;
  }
}
```
