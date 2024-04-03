+++
title = 'Advanced Types in TypeScript'
date = 2023-02-20T18:00:21Z
draft = true
tags = ['TypeScript', 'Notes']
+++

These are just some notes I've compiled on some of the more advanced types that are available in TypeScript, this isn't intended to be a comprehensive listing, these are just examples that I wanted to note down for possible future use. 

### Intersection Types

Intersection types essentially let you join types together. You might use this in scenarios where you have some shared types.

In the example below we have two types Admin and Employee. Admin has two values (name and privileges) and Employee has two values (name and startDate). We need another type (ElevatedEmployee) that combines all three of these values, we can do this by declaring a new type and using the & operator to join these two types together

```tsx
type Admin = {
	name: string;
	privileges: string[];
}

type Employee = {
	name: string;
	startDate: Date;
}

type ElevatedEmployee = Admin & Employee;

const e1: ElevatedEmployee = {
	name: 'Max',
	privileges: ['create-server'],
	startDate: new Date()
};
```

Note that we can also achieve the same thing using interfaces and extending them. 

```tsx
interface Admin = {
	name: string;
	privileges: string[];
}

interface Employee = {
	name: string;
	startDate: Date;
}

interface ElevatedEmployee extends Admin, Employee {}

const e1: ElevatedEmployee = {
	name: 'Max',
	privileges: ['create-server'],
	startDate: new Date()
};
```

In the case of type intersection with union types, it will assign the created intersection type, the type that is commonly shared, for example:

```tsx
type Combinable = string | number;
type Numeric = number | string;

type Universal = Combinable & Numeric;
// The type of Universal is number, as it is the only common type between the two.
```

### Type Guards

Type guards are methods which are used to check if certain types exist. Sometimes you will not know what a type will be before you access it. A basic example of this is:

```tsx
type Combinable = string | number;

function add(a: Combinable, b: Combinable) {
	if (typeof a === 'string' || typeof b === 'string') {
		return a.toString() + b.toString();
	}
	return a + b;
}
```

Here we are checking whether the a and b argument are strings or numbers. If they are strings then we can concat them, otherwise we just return the two numbers added. 

Another example of this that builds on our Admin and Employee Types. If we write a function to print out employee information using a UnknownEmployee type, then we will have no issues with printing the Name, as both the Employee and Admin types have a Name type, however if we try to print the Privileges we start to encounter issues, as TypeScript won’t know if the Unknown Employee type will contain Privileges, as it might be an Admin type and not an Employee type. 

We can’t use typeof here as Employee and Admin are not JavaScript types, whereas things like ‘string’ and ‘number’ are. In order to write a type guard for this we need to use the **in** keyword

```tsx
type UnknownEmployee = Employee | Admin

function printEmployeeInformation(emp: UnknownEmployee) {
	console.log('Name: ' + emp.name);
	if ('privileges' in emp) {
		console.log('Privileges: ' + emp.privileges);
	}
	if ('startDate' in emp) {
		console.log('StartDate: ' + emp.startDate);
	}
}
```

There is also a similar technique that you can use with Classes by using the instanceof keyword. In the example below we have two types (Car and Truck), the truck can have cargo but both can be driven. If we create a function called useVehicle which takes in a vehicle argument with the type of vehicle (which is a union type of Car and Truck), we need a type guard to see if the vehicle is of Truck type, and therefore if cargo can be loaded. 

```tsx
class Car {
	drive() {
		console.log('Driving...')
	}
}

class Truck {
	drive() {
		console.log('Driving a truck...')
	}

	loadCargo(amount: number) {
		console.log('Loading cargo ...' + amount);
	}
}

type Vehicle = Car | Truck;

const v1 = new Car();
const v2 = new Truck();

function useVehicle(vehicle: Vehicle) {
	vehicle.drive();
	if (vehicle instanceof Truck) {
		vehicle.loadCargo(1000);
	}
}
```

Note that this only works with classes, as classes use constructor functions, so TypeScript is able to determine if ‘vehicle’ was built based on the ‘Truck’ constructor function.

### Discriminated Unions

A Discriminated Union is a patten that can be used in situations where we have types that are similar in different object types. We basically add in a common type (in most cases this will be something like “type” or “kind”), and then we check against this to ensure we have access to the right type. It is another kind of type guard.

In this example we have two types “Bird” and “Horse” they each have a separate type for their movement speed, however within our moveAnimal function we want to access this speed regardless of the object type (i.e. if the animal is a bird, we want flying speed, if it is a horse we want running speed). We add in a “type” type, which is a literal type, it can either be bird or horse, then we can use a switch case for our type guards. 

```tsx
interface Bird {
	type: 'bird';
	flyingSpeed: number;
}

interface Horse {
	type: 'horse';
	runningSpeed: number;
}

type Animal = Bird | Horse;

function moveAnimal(animal: Animal) {
	let speed;
	switch (animal.type) {
		case 'bird':
			speed = animal.flyingSpeed;
			break;
		case 'horse':
			speed = animal.runningSpeed;
			break;
	}
	console.log('Moving at speed: ' + speed);
}

moveAnimal({type: 'bird', flyingSpeed: 10});
```

### Type Casting

Type Casting is used when Typescript is unable to tell the type of something, or it might not get the exact type. In this example we are grabbing an input field from the DOM, but it will be grabbed by it’s id. So all TypeScript knows is that this element is just some kind of HTMLElement, so this is the type that is assigned, however if we want to change the value of the input, TypeScript will complain as it won’t know if value is a valid property of our element. In this case we need to cast the type, so essentially tell TypeScript what type to expect. 

In our case we can cast the HTMLInputElement type, as we have access to the DOM library. There are two main ways to cast types, the first uses the expected type inside angle brackets. However due to possible confusion with React, which uses these same angle brackets for JSX templated, there is a second way to cast types, this uses the ‘as’ keyword, and the type is placed at the end.

```jsx
// This is the first way of casting a type
const userInputElement = <HTMLInputElement>document.getElementById('user-input')!;

// This is the alternative way of casting a type - better for react codebases
const userInputElement = document.getElementById('user-input')! as HTMLInputElement;

userInputElement.value = 'Hi There!';
```

However you will notice in the above example that we are using the ! at the end of the DOM fetch, essentially telling TypeScript that we know this will not be null. However this is quite bad practice and will fail linting. What we should do is place a Type Guard around our value setter, to check if userInputElement is not null. However in order to to that we need to move our type casting, as it only makes sense when userInputElement is not null, so we move this to inside the if block, and now we need to wrap it in brackets to ensure that it does the type casting first, before it tries to set the value

```tsx
const userInputElement = document.getElementById('user-input');

if (userInputElement) {
	(userInputElement as HTMLInputElement).value = 'Hi';
}
```

### Index Properties

Index properties can be used when we are working with object types but we know very little about the type. An example here is a form that needs validation. We can create an ErrorContainer interface which will hold any generated error messages, however we don’t know in advance which fields will require validation. This is where we use index properties. We initialize the ErrorContainer interface object, then we create our key or prop value and make it indexable by placing it in square brackets. Then we give it a type, in our case this is string, but it could also be Number or Symbol. Then we add the type for this property, which again is string. This allows us to easily set multiple object properties on the fly as long as we are aware of these constraints. 

```tsx
interface ErrorContainer {
	[prop: string]: string;
}

const errorBag: ErrorContainer = {
	email: "Not a valid email!",
	username: "Must start with a capital letter"
};
```

### Function Overloads

Function overloads are useful for when TypeScript is unable to infer types correctly on it’s own. It allows us to control what the expected output type of a function is based on the input types. Typically you would write your overloads above your main function. So in our example we are using our add function, but we want to know whether it will return a string or a number, so we can run string operations on it. By default the type of the add function is just going to be Combinable

```tsx
type Combinable = string | number;

function add(a: number, b: number): number;
function add(a: string, b: string): string;
function add(a: string, b: number): string;
function add(a: number, b: string): string;
function add(a: Combinable, b: Combinable) {
	if (typeof a === 'string' || typeof b === 'string') {
		return a.toString() + b.toString();
	}
	return a + b;
}

const result = add('Max', ' Schwarz');
result.split(' ');
```

This is probably a little excessive, but it’s just to illustrate what function overloads do. So say you are putting at least one of the arguments into the add function as a string, the overloads will see this, and ensure that the return type is string. The only scenario where we will expect a return type of number will be when passing in two numbers. So in our example we will get an IDE error on our result.split call if we use two numbers, as TypeScript knows that the return type will be a number, whereas it will be fine if we are passing at least one string in.

### Optional Chaining

Optional Chaining is useful when working with datasets where we don’t really know or aren’t able to parse the data that is coming in. In this example we have a fetchedUserData object which we will assume is coming from the user, then within **job** there is another object with details about the user’s job. Let’s say we don’t know what is going to be in the job object. In traditional JavaScript we can check this by looking for the existence of the job object ie

```tsx
const fetchedUserData = {
	id: "u1",
	name: "Max"
	job: { title: "CEO", description: "My Company" }
}

console.log(fetchedUserData.job && fetchedUserData.job.title);
```

This would work fine and would only show the job title, if job was present on the object. However in TypeScript we can use optional chaining to make this a bit neater, the same logic applies, we are just using the question mark to indicate where we should stop when we are looking at objects ie

```tsx
const fetchedUserData = {
	id: "u1",
	name: "Max"
	job: { title: "CEO", description: "My Company" }
}

console.log(fetchedUserData?.job?.title);
```

### Nullish Coalescing Operator

The nullish coalescing operator works in the same way it typically does in other languages, it uses the double question mark ?? to evaluate whether something is either null or undefined then if it is, it will default to whatever is on the right side of the question marks. In this example we might want to store the value of userInput, even if it is an empty string. Only if it is null or undefined to we want to return ‘DEFAULT’. 

```tsx
const userInput = '';

const storedData = userInput ?? 'DEFAULT';

// storedData => ''
```

In this case stored data would hold the empty string because it is not null or undefined. If we change it to undefined then we will get the default fallback

```tsx
const userInput = undefined;

const storedData = userInput ?? 'DEFAULT';

// storedData => 'DEFAULT'
```