# JavaScript Basics

It's usual to use camelcase for variables. When we first define a variable, we should use the `var` keyword, but not when we reassign. If we have a global scope variable `x`, we can use it everywhere. If we define a local variable `x` using `var x;` inside a function, it will create a new local variable without affecting the global one.

Methods on strings include:

`charAt(x)`
`length`

## Loops
```javascript
while(something) {
    //do this
}
```

```javascript
for(start;loop_if_true;do_after_each_loop) {
    //do this
}
```

## Function Expressions
These are essentially "functions on the fly". Normal functions are loaded into memory when the program loads. If we put a function into a variable, it will only be loaded into memory when the line is executed:

Loaded when the program loads:
```javascript
function thing(a,b) {
    return a + b;
}
```

Non loaded when the program loads:
```javascript
var doThing = function thing(a,b) {
    return a + b;
};

doThing(1,2); //Function is only loaded into memory when this is called. Note we call via the variable name, not the function name.
```

Since we're never using the `thing` function name, we can declare it as an anonymous function:

```javascript
var doThing = function (a,b) {
    return a + b;
};

doThing(1,2);
```


