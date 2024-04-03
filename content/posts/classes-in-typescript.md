+++
title = 'Classes in TypeScript'
date = 2023-02-15T17:55:21Z
draft = true
tags = ['TypeScript', 'Notes']
+++

Some notes about using classes in TypeScript and things to look out for.

A basic definition for a class in TypeScript

```tsx
class Department {
	name: string;
	
	constructor(n: string) {
		this.name = n;
	}
}

// Creating an instance of this class...
const = accounting = new Department('Accounting');
```

Think of a class as a ‘blueprint’ for an object. So here we want to create a Department object that we are going to assign names to for different departments. So we define the name property as a string type. Then following this is a constructor function which gets fired automatically when the class is instantiated, kind of like a callback. So in here we can create the argument ‘n’ which is a string type, and then set whatever value is passed to the name property. 

### Functions in classes

We can run functions within classes, however there are one or two things to watch out for. Here is an example of a function within a class:

```tsx
class Department {
	name: string;

	constructor(n: string) {
		this.name = n;
	}

	describe(this:Department) {
		console.log('Department: ' + this.name);
	}

	// Creating an instance of this class...
	const = accounting = new Department('Accounting');

	// Running the describe function/method
	accounting.describe();
	
}
```

The key thing to note here is the argument that we are passing in to the describe method. It is a special kind of argument that helps inform the method of what *this* refers to. So here we are setting it to the type of our department class. This way the method knows that when we are using *this*, it is referring to our class itself. 

### Private and Public access modifiers

Any methods or properties within our class can be made private (public being the default). This is to restrict access to that method or property from outside the class itself.

```tsx
class Department {
	name: string;
	private employees: string[] = [];

	constructor(n: string) {
		this.name;
	}

	describe(this: Department) {
		console.log('Department' + this.name);
	}

	addEmployee(employee: string) {
		this.employees.push(employee);
	}
}

// Normal way of adding employees
accounting.addEmployee('Max');
accounting.addEmployee('Manu');

// This way we don't want to allow
accounting.employees[2] = 'Anna';
```

Here we have prevented any way of accessing the employees array, by prefixing it with the **private** keyword. So in order to add employees, people must use the addEmployee method.

It’s important to note too that JavaScript isn’t really aware of the public and private modifiers, so this is currently something TypeScript is doing during compilation.

### Shorthand Initialisation

We can reduce some of the code written when initialising classes, currently we are defining each of the properties of the function, then below this we are using the constructor function to take the arguments and assign them to the relevant properties. This can be condensed to all be done within the constructor function. However we do need to define which properties are public and which ones are private

```tsx
class Department {
	//private id: string;
	//private name: string;

	constructor(private id: string, public name: string) {
		//this.id = id
		//this.name = name	
	}
}
```

We are now creating properties from the arguments that are getting passed in, as well as assigning them values.

### Read-Only Properties

It is possible to make some properties within the class ‘read-only’. Again this is a TypeScript thing that happens at compilation time, but it prevents a property being written to once it has been initialised. You just need to add the readonly keyword to the property definition.

```tsx
class Department {
	constructor(private readonly id: string, public name: string) {}
```

### Inheritance

We can inherit from classes to create new classes. In this example we are looking to create a couple of new departments ‘IT’ and ‘Accounting’ but each of these will have their own special date. The IT department might have a list of admins and the Accounting department might have a list of reports. To do this we can extend out our Department class a couple of times, adding in any extra fields we may need.

```tsx
class Department {
	name: string;
	private employees: string[] = [];

	constructor(n: string) {
		this.name;
	}

	describe(this: Department) {
		console.log('Department' + this.name);
	}

	addEmployee(employee: string) {
		this.employees.push(employee);
	}
}

// Specialised department class for IT
class ITDepartment extends Department {
	constructor(id: string, public admins: string[]) {
		// Use super to call the constructor of the base class and pass along any values
		super(id, 'IT');
	}
}

// Specialised department class for Accounting
class AccountingDepartment extends Department {
	constructor(id: string, private reports: string[]) {
		super(id, 'Accounting');
	}

	addReport(text: string) {
		this.reports.push(text);
	}

	getReports() {
		console.log(this.reports);
	}
}

const accounting = new AccountingDepartment('d2', []);

accounting.addReport('Something went wrong...');

accounting.getReports();
```

### Overriding properties

You can override properties and functions of a base class from within an extended class, by writing a function with the same name in the extended class, however in this case still cannot access any private properties in the parent class, if you need to access to them you should change them from private to ‘protected’. This allows them to edited from inside class extensions.
