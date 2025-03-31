# JS Notess 7

## cheat sheet on all the ways to go between Map ⇆ Object ⇆ Array

🔹 1. Object → Map
const obj = { a: 1, b: 2 };
const map = new Map(Object.entries(obj));

✅ Object.entries(obj) returns [["a", 1], ["b", 2]]

✅ new Map([...]) accepts that array of entries

🔹 2. Map → Object
const map = new Map([["a", 1], ["b", 2]]);
const obj = Object.fromEntries(map);

✅ Object.fromEntries(map) converts a Map (or array of key-value pairs) to an object

🔹 3. Map → Array
const map = new Map([["a", 1], ["b", 2]]);
const arr = [...map]; // or Array.from(map)

Result: [["a", 1], ["b", 2]]

Use map.entries(), map.keys(), or map.values() to extract pieces:

const keys = [...map.keys()]; // ['a', 'b']
const values = [...map.values()]; // [1, 2]

🔹 4. Array → Map

const arr = [["a", 1], ["b", 2]];
const map = new Map(arr);
Must be an array of [key, value] pairs

🔹 5. Object → Array
const obj = { a: 1, b: 2 };
const entries = Object.entries(obj); // [['a', 1], ['b', 2]]
const keys = Object.keys(obj); // ['a', 'b']
const values = Object.values(obj); // [1, 2]

🔹 6. Array → Object
Using .reduce():
const arr = [["a", 1], ["b", 2]];
const obj = arr.reduce((acc, [key, value]) => {
acc[key] = value;
return acc;
}, {});
Or simply:

const obj = Object.fromEntries(arr);

## Summary

Object ⇄ Map → via Object.entries / fromEntries
Object ⇄ Array → via Object.entries, keys, values
Map ⇄ Array → via spread [...] or new Map(array)

## Deep Clone of Map/Object

For Object:
const clonedObj = structuredClone(obj);
For Map:
const clonedMap = new Map(JSON.parse(JSON.stringify([...map])));
