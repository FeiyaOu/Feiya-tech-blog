# JS Notes X

## Set

###1 A Set is a collection of unique values. No duplicates allowed.

example:
const fruits = new Set();
fruits.add("apple");
fruits.add("orange");
fruits.add("apple"); // duplicate, ignored

console.log(fruits); // Set(2) { 'apple', 'orange' }

###2 5 Common Set Methods:

.add(value) Adds a value
.delete(value) Removes a value
.has(value) Checks if value exists
.clear() Removes all values
.forEach() Iterates over values: fruits.forEach(f => console.log(f))

###3 if I want the second value in a set, how to extract it?

Since Set and Map are both iterable, we can access specific values using iteration — even though they don’t offer direct index-based access like arrays.

Let’s see how to get the second value from both:

const fruits = new Set(["🍎", "🍊", "🍌"]);

const secondValue = [...fruits][1];
console.log(secondValue); // 🍊

Set doesn’t support index access (fruits[1] won’t work).

But you can spread it into an array: [...fruits] gives you ["🍎", "🍊", "🍌"]

Then you can safely use [1] to get the second value.

###4 so when we define a set , it is not like this: const fruits = new Set("🍎", "🍊", "🍌");?

Exactly. It's a very common confusion at first.

This is not valid:

const fruits = new Set("🍎", "🍊", "🍌"); // 🚫 Wrong

Why?

Because the Set constructor expects a single iterable argument — usually an array, string, or anything iterable.

Correct ways to define a Set:

1. Using an array of values:
   const fruits = new Set(["🍎", "🍊", "🍌"]);

   This creates a Set with 3 unique fruit emojis.

2. Using a string (each character becomes an item):

const letters = new Set("hello");
console.log(letters); // Set { 'h', 'e', 'l', 'o' } — duplicates removed

So this works because strings are iterable — but it's best used for characters.

###5 Why this works:
new Set(["🍎", "🍊", "🍌"])

This passes one iterable argument (an array), and Set grabs each item.

But this:new Set("🍎", "🍊", "🍌")
Passes multiple arguments, and Set ignores the rest — only "🍎" is used.

###6 what is the logic behind the design of sets in JS?

avaScript engines (like V8 in Chrome) usually implement Set as a hash-based data structure, very similar to how Map works.

Here’s what’s happening under the hood:
When you do:

const fruits = new Set();
fruits.add("🍎");
fruits.add("🍊");

The engine hashes each value to create a unique internal key

Stores the values in a hash table

Maintains insertion order (unlike traditional sets in some languages!)

###7 Key Features of JavaScript Set

Hash-based under the hood (like a lightweight Map)

Maintains insertion order

No duplicates

Works with primitive types and objects as values
