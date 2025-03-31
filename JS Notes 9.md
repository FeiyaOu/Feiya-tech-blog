# JS NOTES 9

## 1 prototype

### 1 setPrototypeOf

const Person = function (name, age) {
this.name = name;
this.age = age;
};

const Student=function (major){
this.major=major
}

Object.setPrototypeOf(Student,Person)

console.log(Student.prototype.**proto**===Person.prototype)
why it is false?

Awesome question — and you're bumping into one of the most confusing parts of JavaScript inheritance: the difference between a constructor function itself and its prototype.

Let’s unpack it step by step:
const Person = function (name, age) {
this.name = name;
this.age = age;
};

const Student = function (major) {
this.major = major;
};

Object.setPrototypeOf(Student, Person);

console.log(Student.prototype.**proto** === Person.prototype); // ❓ false

You're expecting this to be true, but it's false. Let's see why 👇

What's really happening?

Object.setPrototypeOf(Student, Person);
✅ This sets Student.**proto** = Person — you're modifying the constructor function's prototype chain, not the instances or the Student.prototype.

But the line you’re checking:
Student.prototype.**proto** === Person.prototype

This is about the instance prototype chain, not the constructor.
In other words:
Student → is a function

Student.prototype → is what instances of Student will inherit from

Student.**proto** → is what Student the function inherits from (not related to object instances)

How to make Student instances inherit from Person instances?
You should set the prototype of Student.prototype, not Student itself:

Object.setPrototypeOf(Student.prototype, Person.prototype);

OR even more idiomatically:
Student.prototype = Object.create(Person.prototype);

Then your check will work:
console.log(Student.prototype.**proto** === Person.prototype); // true ✅

Summary :
If you want Student instances to inherit from Person instances, you must set:
Student.prototype.**proto** = Person.prototype,
or better: Student.prototype = Object.create(Person.prototype)

### 2 static property/method of a function

Person.description1="Welcome";
Person.num=function(){
return 5;
}
are these static property and method of a function?

YES! You're absolutely right — and you're asking the perfect question to understand how JavaScript treats functions as objects.

Let’s break it down clearly:
Person.description1 = "Welcome";

Person.num = function () {
return 5;
};

So what’s happening here?
In JavaScript:

Functions are first-class objects.

That means you can add properties and methods to them just like any object.

In this case:
Person.description1 = "Welcome";
✅ You're adding a static property description1 to the Person constructor function.
Person.num = function () {
return 5;
};
✅ You're adding a static method num() to the Person function itself.

“Static” means:
These do not exist on instances of Person. They exist only on the constructor function itself.

const p = new Person("Alice", 25);

console.log(Person.description1); // ✅ Welcome
console.log(Person.num()); // ✅ 5

console.log(p.description1); // ❌ undefined
console.log(p.num); // ❌ undefined

### 3 how to define prototype property with "this"

const Person = function (name, age) {
this.name = name;
this.age = age;
};
Person.prototype.title = Mr/Ms ${this.name};
what is wrong with this?

Great catch — you're onto something that looks right but behaves wrong in JavaScript. 👇

The problem:
Person.prototype.title = `Mr/Ms ${this.name}`;

This line tries to define a prototype property, but it's using this.name outside of any function context.

❌ Why it's wrong:
At the time this line runs, this does not refer to a Person instance.

It refers to the global object (window in browsers, or undefined in strict mode).

So this.name is either:

undefined (in strict mode)

or just wrong (it doesn't refer to the object you think)

What it actually does:
Person.prototype.title = "Mr/Ms undefined";

Because this.name is undefined at the time that line runs.

✅ How to do it right
If you want title to be computed per instance, you should use a getter method:

Option 1: Use a method
Person.prototype.getTitle = function () {
return `Mr/Ms ${this.name}`;
};

Usage:

const p = new Person("Alice", 30);
console.log(p.getTitle()); // Mr/Ms Alice

Option 2: Use a getter property (with Object.defineProperty)
Object.defineProperty(Person.prototype, "title", {
get: function () {
return `Mr/Ms ${this.name}`;
},
});

Usage:

const p = new Person("Alice", 30);
console.log(p.title); // Mr/Ms Alice

Fix it using a function or getter, so this is evaluated in the right context — when an instance is actually used.

### 4 how to add prototype property in a class

class Fruit {
constructor(nameFruit, originFruit) {
this.nameFruit = nameFruit;
this.originFruit = originFruit;
this.comment = function () {
return ${this.nameFruit} from ${this.originFruit}, Great taste! ;
};
}

recommend1() {
return 'Recommend to friends!';
}
}
how to add a prototype property in this class?

You're doing great already with methods and constructor logic — and you're super close to understanding how to add prototype properties to a class in JavaScript!

Let’s walk through your question:

✅ How to add a prototype property (outside the class)
You can attach a property to the class prototype like this:

Fruit.prototype.category = "General fruit";

This means all instances of Fruit will inherit that property unless they override it.

example:

const apple = new Fruit("Apple", "USA");

console.log(apple.category); // "General fruit"
console.log(apple.recommend1()); // "Recommend to friends!"
console.log(apple.comment()); // " Apple from USA, Great taste! "

### 5 “How do I define a prototype property inside the class definition itself?”

You can’t directly define prototype properties inside the class body — only methods.
But there are some smart ways around this 👇

✅ 1. Add the property after the class is defined (standard way)

class Fruit {
constructor(nameFruit, originFruit) {
this.nameFruit = nameFruit;
this.originFruit = originFruit;
}

recommend1() {
return "Recommend to friends!";
}
}

// ✅ Add prototype property after definition
Fruit.prototype.category = "General fruit";

🌀 2. Use a getter method to simulate a prototype property
This is a clean, class-friendly trick:

class Fruit {
constructor(nameFruit, originFruit) {
this.nameFruit = nameFruit;
this.originFruit = originFruit;
}

get category() {
return "General fruit";
}

recommend1() {
return "Recommend to friends!";
}
}

❗ Why can’t you write this directly?
JavaScript class syntax doesn’t support prototype properties directly — only methods and static properties.

class MyClass {
// This is a method
myMethod() {}

// ❌ This is NOT allowed:
// myProp = "some value"; // (this goes to the instance, not the prototype)
}

How does myProp = "some value" go to the instance?

👇 When you write:

class MyClass {
myProp = "some value";
}

✅ This is valid syntax!
It’s called a public class field, and it adds the property to each instance, not to the prototype.

🔍 Behind the scenes, this:

class MyClass {
myProp = "some value";
}

is equivalent to :

class MyClass {
constructor() {
this.myProp = "some value"; // ✅ instance property
}
}

Example:
class MyClass {
myProp = "some value";
}

const instance1 = new MyClass();
const instance2 = new MyClass();

console.log(instance1.myProp); // "some value"
console.log(instance2.myProp); // "some value"
console.log(instance1.hasOwnProperty("myProp")); // true ✅
console.log(MyClass.prototype.hasOwnProperty("myProp")); // false ❌

✅ Summary:

Declaration :
myProp = "value" in class body
//Where it ends up: Instance (this.myProp)
myMethod() in class body
//Where it ends up: Prototype (MyClass.prototype.myMethod)
MyClass.staticProp = "value"
//Where it ends up: Static on constructor (MyClass)
MyClass.prototype.prop = value
//Where it ends up: Prototype property

### 6 prototype lookup

class Fruit {
constructor(nameFruit, originFruit) {
this.nameFruit = nameFruit;
this.originFruit = originFruit;
this.comment = function () {
return ${this.nameFruit} from ${this.originFruit}, Great taste! ;
};
}

recommend1() {
return 'Recommend to friends!';
}
}

const fruit1 = new Fruit('apple', 'Germany');
Fruit.prototype.taste = 'Best ever!';
console.log(fruit1.taste);

here, I define the taste property on Fruit prototype after I declare fruit1, and then fruit1 can have access to the taste, I am a bit confused about this.this is not related to hoist? it works because when I write fruit1.taste, it just search the prototype chain and find this property?

🎯 YES YES YES — you're exactly right, and you're describing it perfectly! Let’s break it down and make it crystal clear for you:

Why does fruit1.taste work even though you added it after creating fruit1?

Because prototype lookup is dynamic.
When you write:

fruit1.taste

JavaScript doesn't say:

“What properties did fruit1 have when it was created?”

Instead, it says:

“Let me check fruit1 → doesn’t have taste → let me look up the prototype chain... oh! Found taste on Fruit.prototype!”

Prototype lookup works at access time, not at construction time.
That’s why even if you add something to the prototype later, all instances (even existing ones) can see it!

So no — this is not about hoisting at all.
Hoisting is about variables/functions being moved to the top of their scope during compilation.

This is about: ✅ Dynamic prototype resolution,
✅ via the internal [[Prototype]] chain.
