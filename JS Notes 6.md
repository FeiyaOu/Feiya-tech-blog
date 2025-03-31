#JS Notes 6

## Map

###1 A Map is a collection of key-value pairs, like an object, but with ordered keys and any type allowed as keys.

const scores = new Map();
scores.set("Alice", 90);
scores.set("Bob", 85);
scores.set("Alice", 95); // updates existing key

console.log(scores); // Map(2) { 'Alice' => 95, 'Bob' => 85 }

###2

5 Common Map Methods:

.set(key, value) Adds or updates a key-value pair: scores.set("Eve", 88)
.get(key) Retrieves value by key: scores.get("Alice")
.has(key) Checks if a key exists: scores.has("Bob")
.delete(key) Removes a key-value pair: scores.delete("Bob")
.clear() Removes all entries: scores.clear()

###3
if I want the second key-value in a map, how to extract it without knowing the key?

Get the second entry in a Map (without knowing the key):
const scores = new Map([
["Alice", 90],
["Bob", 85],
["Eve", 95],
]);

const [secondKey, secondValue] = [...scores.entries()][1];
console.log(secondKey, secondValue); // Bob 85

Map.entries() gives you an iterator of [key, value] pairs.

Spread it: [...scores.entries()] → [["Alice", 90], ["Bob", 85], ...]

Then grab [1] for the second pair.

###4
Map.entries() gives you an iterator of [key, value] pairs, what about Maps.keys(), Maps.values(), do they exist?

Yes — absolutely!

JavaScript Map Iteration Methods:

.entries() An iterator of [key, value] pairs: for (const [k, v] of map.entries())
.keys() An iterator of keys: for (const k of map.keys())
.values() An iterator of values: for (const v of map.values())
.forEach() Runs a function for each [value, key]: map.forEach((v, k) => ...)

Example:
const userRoles = new Map([
["Alice", "admin"],
["Bob", "editor"],
["Eve", "viewer"]
]);

// Entries
for (const [user, role] of userRoles.entries()) {
console.log(`${user} => ${role}`);
}

// Keys
for (const user of userRoles.keys()) {
console.log(user);
}

// Values
for (const role of userRoles.values()) {
console.log(role);
}

###5
Map.entries() returns an array? and just like sets, we need to put all the entries pairs into an array as a single argument when create a Map?

1. Does Map.entries() return an array?

👉 Not exactly.

❌ It does not return an array
✅ It returns an iterator (a special object you can loop over)
So this:
const map = new Map([
["a", 1],
["b", 2],
]);

console.log(map.entries());
// 👉 [Map Entries] { [ 'a', 1 ], [ 'b', 2 ] } ← iterable, not an array

If you want a real array:

const entriesArray = [...map.entries()];
console.log(entriesArray);
// 👉 [ [ 'a', 1 ], [ 'b', 2 ] ]

Same with .keys() and .values() — they all return iterators, not arrays, but you can spread them.

🔹 2. Do you need to pass entries as an array of pairs to create a Map?

✅ Yes, exactly!

When you create a Map, you pass one argument, which must be an iterable of [key, value] pairs — usually an array of arrays:

const myMap = new Map([
["apple", 1],
["banana", 2],
]);

This works because:

You're passing one iterable argument (the outer array)

Each inner element is a [key, value] pair

❌ This won’t work:
const badMap = new Map("apple", 1); // ❌ wrong — too many arguments

###6 { [ 'a', 1 ], [ 'b', 2 ] } this iterator, special object returned by Map.entries() can be spreaded into an array ?

✅ Absolutely — you can spread the iterator returned by Map.entries() into an array.

Why does that work?
Because Map.entries() returns an iterator that is also iterable.

That means:

const map = new Map([
["a", 1],
["b", 2],
]);

const entriesIterator = map.entries();

console.log(entriesIterator);
// [Map Entries] { [ 'a', 1 ], [ 'b', 2 ] } ← Not an array, but iterable!

So you can spread it into a real array like this:

const entriesArray = [...entriesIterator];
console.log(entriesArray);
// 👉 [ [ 'a', 1 ], [ 'b', 2 ] ]

Map.entries() returns an iterator, but because it implements [Symbol.iterator], it can be used in:

for...of

[...spread]

Array.from(...)

###7 these features of map and set all come from its deeper logic, which is the internal hash table?

YES — you’ve got it!

The awesome features of Map and Set in JavaScript — like:

Fast lookups (.has())

Unique value enforcement (for Set)

Key-value pairs with any type as key (for Map)

Preserving insertion order

…are all possible thanks to their underlying internal implementation, which is usually a hash table.

What’s a Hash Table?
A hash table (or hash map) is a data structure that:

Uses a hash function to turn a key into an index.

Stores the value at that index in memory.

Allows constant time access to keys/values in most cases — O(1).

In JavaScript:

Map is essentially a key-value hash table with some bonus features (like ordered keys).

Set is like a value-only hash table, where keys and values are the same (because it only stores unique values).

But: It’s not just a hash table
In most JavaScript engines (like V8):

Map and Set are built on hash tables + linked lists

The linked list part helps maintain insertion order while the hash table keeps it fast

So it’s a hybrid：

Hash Table + Order Tracking = JavaScript Map and Set

###8 const scores = new Map([
["Alice", 90],
["Bob", 85],
["Eve", 95],
]);

const [secondKey, secondValue] = [...scores.entries()][1];
console.log(secondKey, secondValue);
in this case, if I write const [secondKey, secondValue] = [...scores][1], would it work?

YES, absolutely! 🎯

You’re spot on again — in this case:
const [secondKey, secondValue] = [...scores][1];
…works exactly the same as:
const [secondKey, secondValue] = [...scores.entries()][1];

Why does this work?
Because in JavaScript:
scores[Symbol.iterator] === scores.entries
That means Map is iterable, and its default iterator is .entries() — the [key, value] pairs.

So when you do:
[...scores]
…it’s really doing:
[...scores.entries()]

Result:
const scores = new Map([
["Alice", 90],
["Bob", 85],
["Eve", 95],
]);

const [secondKey, secondValue] = [...scores][1];
console.log(secondKey, secondValue); // Bob 85 ✅
So yes — your version is clean, correct, and totally works.
