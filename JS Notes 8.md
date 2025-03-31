# JS NOTES 8

## closure

### 1 First: What’s a closure?

A closure is when a function "remembers" the variables from the scope where it was created, even after that scope is gone.

### 2 Question Example

Example:

function fn1() {
const a1 = 5;

return function fn1() {
const a2 = 2;

    return function fn3() {
      const a3=3
      const c3=a1+a2+a3
      return c3;
    };

};
}

const res1 = fn1();
const res2 = res1();
const res3 = res2();
console.log(res3);
in this example, a1 is also remembered because of closure? why? I thought only a2 will be remembered because of closure.

Brilliant question! You’ve hit the heart of what makes closures so powerful (and sometimes confusing). Let's break it down — and yes, you're right to be surprised, but you’re about to fully understand why this works.

Your Code — Step by Step:

function fn1() {
const a1 = 5;

return function fn2() {
const a2 = 2;

    return function fn3() {
      const a3 = 3;
      const c3 = a1 + a2 + a3;
      return c3;
    };

};
}

Execution:

const res1 = fn1(); // res1 is fn2, closure created over a1
const res2 = res1(); // res2 is fn3, closure created over a2 AND a1
const res3 = res2(); // fn3 runs, uses a1, a2, a3

🤔 Why is a1 remembered?
✅ Because fn2 was created inside fn1, and it closes over a1.

✅ Then fn3 is created inside fn2, and it closes over everything available to it at the time — which includes:

a2 from fn2

a1 from fn1 (still in memory due to closure)

** Closures chain upward ** — not just to their direct parent, but through the entire scope chain.

Think of it like nested containers:

fn1 scope: { a1 }
↓
fn2 scope: { a2 } + inherits { a1 }
↓
fn3 scope: { a3 } + inherits { a2, a1 }

So when fn3 runs:

const c3 = a1 + a2 + a3;

👉 All three are in memory — even though fn1() already finished executing.

That’s the power of closures.

Closures don't just capture immediate outer variables — they preserve the entire lexical scope chain.
So fn3 has access to a1, a2, and a3 — even though a1 was defined 2 levels up.
