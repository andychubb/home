+++
title = 'TypeScript Functions as Types'
date = 2023-02-10T17:48:16Z
draft = true
tags = ['TypeScript', 'Notes']
+++

There is a “function” type in TypeScript that you can use to set a variable to have a function type. For example you might have an add function, but you want to assign that function to another variable called combineValues as a pointer, then you can pass the arguments in and get the result of the two added numbers.

```tsx
function add (n1:number, n2:number) {
	return n1 + n2;
}

// By default combineValues will have the 'any' type
let combineValues;

combineValues = add;

console.log(combineValues(8,8);
```

Because combineValues has the ‘any’ type we can still assign anything to it, not just functions.

We can sort this by giving the combineValues variable a ‘Function’ type

```tsx
let combineValues: Function;
```

So this improves things, however we can still assign any function to combineValues, not just the add function we have written. 

We can improve this further by using function types. *Function types look like a Javascript arrow function but without the curly braces.* Within this we specify what the types of the arguments are and also, what the expected return type is. For example:

```tsx
let combineValues: (a: number, b: number) => number;
```

Here we are saying we want 2 arguments, both need to be numbers, and we are expecting a number to be returned. This means that when we try to assign a function to combineValues that doesn’t match this, it will warn us.

## Function Types and Callbacks

We can also include callbacks in function types. So we can a specify a callback function that can be passed in as an argument and then executed within the function for example we maight have a function which adds two numbers and then there is call back function in there to do something with the result

```tsx
function addAndHandle(n1: number, n2: number, cb: (num: number) => void) {
	const result = n1 + n2;
	cb(result);
}
```

We have added the function type `() => void` to the callback argument, to show that we are expecting a number to be passed into it as an argument. 

So now we can use this function, pass in two numbers and then create an anonymous function as the third argument, where we can console log out the result.

```tsx
addAndHandle(10, 20, (result) => {
	console.log(result);
});
```

Callback functions can return something, even if the argument on which they're passed does NOT expect a returned value. So this will still complile:

```tsx
function sendRequest(data: string, cb: (response: any) => void) {
  // ... sending a request with "data"
  return cb({data: 'Hi there!'});
}
 
sendRequest('Send this!', (response) => { 
  console.log(response);
  return true;
 });
```