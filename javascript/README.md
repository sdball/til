# til JavaScript

## General

- really only use `===` for equality since `"1" == 1` is true but `"1" === 1` is false
- `&&` and `||` produce values based on truthiness:
    - `1 && 2` produces `2`
    - `1 || 2` produces `1`
- the `typeof` operator produces the following values
    - `"number"`
    - `"string"`
    - `"boolean"`
    - `"undefined"`
    - `"function"`
    - `"object"`
    - If the operand is an array or `null` then the result is `"object"` which is wrong

## Numbers

- JavaScript has a single number type internally represented as 64-bit floating point
- `1` and `1.0` are the same number (no separate decimal type)
- `100` and `1e2` are the same number (no separate exponent type)
- `100.0` and `1e2` are the same
- `100.0` and `1.0e2` are the same
- `NaN` is not equal to anything, including itself
- `Infinity` represents all values greater than `1.79769313486231570e+308`

## Strings

- all characters are 16 bits
- JavaScript does not have a character type
- strings are immutable

## Values

- falsy values: `false`, `null`, `undefined`, the empty string `''`, the number `0`, the number `NaN`
- all other values are truthy

## Objects

Simple types:

- numbers
- strings
- booleans
- `null`
- `undefined`

Everything else is an `object`. Objects in JavaScript are mutable, keyed, collections.

Array? Object. Function? Object. Object? Object.

### Properties

- An object is a container of properties
- A property has a name and a value
- a property name can be any string, even the empty string
- a property value can be any value except `undefined`
- objects can use a prototype linkage feature to inherit properties of another object
    - this can reduce object initialization time and memory consumption
- object properties iterated via `in` must be verified as actually part of the object itself instead of its prototype chain
- to verify if a property belongs to an object use the `hasOwnProperty` function: `object.hasOwnProperty(property)`

### Literals

A convenient notation for creating new object values.

```javascript
var empty_object = {};

var not_empty_object = {
  "property name": "property value",
  "another name": "another value",
  yet_another: "yet another value"
};
```

In the above code `yet_another` doesn't need quotes because it is a valid JavaScript name.

### Values

Retrive values from an object using either a `[]` or `.` syntax. The dot syntax is preferred but only available if the property name is a valid JavaScript name.

```javascript
not_empty_object["property name"]; // "property value"
not_empty_object.yet_another; // "yet another value"
```

`undefined` is returned if an object is asked for a property it doesn't have

```javascript
not_empty_object.not_a_property; // undefined
```

Attempting to retrieve a value from `undefined` will throw a `TypeError` exception.

Object values can be updated

```javascript
not_empty_object.yet_another = "something else";
```

And added

```javascript
not_empty_object.new_property = "something new";
```

### Reference

JavaScript objects are passed by reference, never copied.

### Prototype

Every object is linked to a prototype object from which it can inherit properties.

All objects created from object literals are linked to `Object.prototype`

The prototype link is only used for retriving property values. If we request a property from an object and the object lacks the property then JavaScript next looks into the prototype object. If that prototype object also lacks the property then it goes to the next prototype object until finally reaching `Object.prototype`. If the property isn't defined anywhere in the chain then the end result is `undefined`.

If a prototype is updated then the update will immediately be available to all objects based on that prototype.

## Reflection

Asking an object for its properties can be difficult because any property in the prototype chain can produce a value.

Two approaches:

1. Reject values that are functions using `typeof`
2. Use the `hasOwnProperty` method which doesn't look at the prototype chain

## Enumeration

The `for ... in` statement loops over all the property names in an object. The enumeration will include all properties including functions and prototype properties. A common approach is to use `typeof` and `hasOwnProperty` to filter the resulting properties.

Properties are not guaranteed to be enumerated in any specific order. To have an ordered properties list you have to make an array.

## Delete

The `delete` operator removes a property from an object. If that property has been masking the value from the prototype chain then the prototype chain value is now returned.

## Global Abatement

JavaScript allows global variables but their use should be limited.

One approach is to have one global variable that becomes the container of the application.

## Functions

The fundamental modular unit of JavaScript.

- code reuse
- information hiding
- composition
- specify behavior of objects

### Function Objects

Functions in JavaScript are objects. Function objects are prototype linked to `Function.prototype` (which is in turn linked to `Object.prototype`)

Every JavaScript function also has two hidden properties

1. the function's context
2. the code that implements its behavior

Functions are truly objects and can have methods. Functions can be put into variables, passed as arguments, returned from functions.

The thing that makes them special is that they can be invoked.

```javascript
var add = function(a,b) {
  return a + b;
};
```

A function can either be given a name or be anonymous (as in the above code).

Functions can be nested (they can be placed anywhere an expression can be placed).

A nested function will have access to its parameters and variables but also have access to the parameters and variables of its parent function. The function object created by a function literal contains a link to the outer context. The link to the outer context is called _closure_.

## Function Invocation

In addition to declared parameters all JavaScript functions receive two more parameters: `this` and `arguments`

The value of `this` is determined by the invocation pattern.

There are four patterns of invocation in JavaScript

1. the method invocation pattern
2. the function invocation pattern
3. the constructor invocation pattern
4. the apply invocation pattern

When invoking a function there's no error if the number of arguments don't match. If there are too many arguments then any extra arguments are ignored. If there are too few then `undefined` is substituted for any that are missing.

There is no type checking on the argument values.

### Method Invocation Pattern

When a function is stored as the property of an object it is called a method.

When a method is invoked then `this` is bound to the object itself.

```javascript
var myObject = {
  value: 0,
  increment: function(inc) {
    this.value += typeof inc === 'number' ? inc : 1;
  }
};

myObject.increment( ); // myObject.value === 1
myObject.increment(2); // myObject.value === 3
```

A method can use `this` to access the object and retrieve values or modify the object. The binding of `this` to the object happens at invocation time. This (very) late binding makes functions that use `this` highly reusable.

Methods that get their object context from `this` are called public methods.

### Function Invocation Pattern

When a function is not the property of an object then it is invoked as a function.

When a function is invoked `this` is bound to the global object.

```
var sum = add(2,3); // sum === 5
```

The binding of `this` to the global object from the function invocation creates some unusual problems. For example a method invocation cannot call a function invocation to do some work involving the `this` variable.

- `this` in the method invocation: the containing object
- `this` in the function invocation within the method: the global object

```javascript
var obj = {
  value: 123,
  method_invocation: function() {
    document.writeln("in method_invocation `this` is " + this);
  },
  method_that_calls_a_function() {
    document.writeln("in method_that_calls_a_function `this` is " + this);
    var that = this;
    var function_invocation = function() {
      document.writeln("in function_invocation `this` is " + this);
      document.writeln("in function_invocation `that` is " + that);
    }
    function_invocation();
  }
}

obj.method_invocation();
obj.method_that_calls_a_function();
```

```
in method_invocation `this` is [object Object]
in method_that_calls_a_function `this` is [object Object]
in function_invocation `this` is [object Window]
in function_invocation `that` is [object Object]
```

The workaround, as shown above, is to have a local variable in the method assigned the method's version of `this`. By convention the variable is called `that`.

### Constructor Invocation Pattern

JavaScript does not have classes. Objects inherit directly from other objects. Unfortunately JavaScript provides a syntax that makes object creation appear to be classed based.

If the `new` function is called and passed another function then

- a new object will be created
- the new object will have a hidden link to the value of the function's prototype
- `this` will be bound to the new object

Functions that are intended to be passed to `new` are called constructors. By convention constructors are capitalized.

### Apply Invocation Pattern

The `apply` method of a function takes two arguments:

- the value that should be bound to `this` within the function
- an array of values that will be passed as parameters to the function

```javascript
var foo = function(a) {
  document.writeln("in function foo: this is " + this);
  document.writeln("in function foo: passed argument is " + a);
}

foo();
foo.apply(123, [789]);
```

```
in function foo: this is [object Window]
in function foo: passed argument is undefined
in function foo: this is 123
in function foo: passed argument is 789
```

This can be used as a kind of duck typing for functions. The value dropped into `this` must simply be compatible with what the function expects.

### Arguments

Functions are not limited to the parameters that they automatically assign from the function call. They also have access to an `arguments` array that contains all of the arguments that were passed to the the function.


```javascript
var foo = function(a,b,c) {
  document.writeln("a, b, c are assigned to : " + [a, b, c]);
  document.writeln("but the arguments variable is " + JSON.stringify(arguments));
}

foo(1,2,3);
foo(1,2,3,4,5,6);
```

```
a, b, c are assigned to : 1,2,3
but the arguments variable is {"0":1,"1":2,"2":3}
a, b, c are assigned to : 1,2,3
but the arguments variable is {"0":1,"1":2,"2":3,"3":4,"4":5,"5":6}
```

From this sample code we can see that the `arguments` variable is not actually an array, but an object made to look like one. An unusual choice.

### Return

JavaScript functions always return a value.

- `return` statement? value of the statement returned
- no `return` statement and not called with `new`? `undefined` is returned
- no `return` statement and called with `new`? `this` (the new object) is returned
- return statement and value is an object and called with `new`? object is returned
- return statement and value is not an object and function is called with `new`? `this` (the new object) is returned

### Exceptions

- `throw` interrupts function execution
- each `try` can only have one `catch`
- to respond differently to different exceptions the `catch` block needs to analyze the passed exception object

### augmenting types

JavaScript allows basic types to be augmented in that new methods can be added dynamically.

e.g.

- add `method` method to make method declaration easier
- add `integer` method to all numbers
- add `trim` method to strings

All this power means that libraries can easily collide. Do things like checking to see if a method is defined already before adding your own.

### Recursion

JavaScript does _not_ have tail recursion optimization. So watch out for that.

### Scope

Despite the syntax appearance, JavaScript does _not_ have block scope. JavaScript has function scope.

Parameters and variables defined in a function are not visible outside the function, parameters and variables declared within a function are available everywhere within the function.

It's best to declare all function variables at the top of the function for clarity.

### Closure

Inner functions get access to the parameters and variables of the functions they are defined within (excepting `this` and `arguments`).

This gets very interesting when an inner function lasts longer than the function that contained it. The inner function continues to have access to the context in which it was created: this is called closure. Importantly the inner functions are not copying the values of the outer variables but have direct access to them.

This behavior can be confusing, e.g. creating functions within a loop.

```javascript
var i;
var list = [];

for (i=0; i<5; i++) {
  list[i] = function() { return i; }
}

list[0](); // 5
list[1](); // 5
list[2](); // 5
list[3](); // 5
list[4](); // 5
```

Why are they all five? Because the function being created and assigned to each element of the list has direct access to the actual `i` variable, not the `i` variable as it was when the function was created.

We can even change the `i` variable and see that the functions update as well.

```javascript
i = 17;
list[4](); // 17
```

Instead of declaring functions in the loop, declare a single function that returns a function bound to the current value of i in the loop.

```javascript
var i;
var list = [];

var bound_i = function(i) {
  return function() {
    return i;
  };
};

for (i=0; i<5; i++) {
  list[i] = bound_i(i);
}

list[0](); // 0
list[1](); // 1
list[2](); // 2
list[3](); // 3
list[4](); // 4
```

### Curry

Combine a function and an argument? That's _currying_!

JavaScript does not have a `curry` method, but it can be added to the Function prototype.

### Memoization

Storing the result of previous operations in a data structure. There's nothing specifically unusual about JavaScript here.

### Inheritance

JavaScript is loosely typed and never casts values. What matters is what an object can do, not what it is descended from.

JavaScript is a prototypal language: objects inherit from other objects. But it's syntax tries to make things look as though the language has classical inheritance. Instead of having a syntax that allows objects to directly declare what they inherit from, JavaScript requires constructor functions to produce objects.

Recall that constructor functions are meant to be called with the `new` modifier. If they are _not_ called with `new` then `this` in the constructor will not be bound to the new object. Instead `this` will be bound to the **global** object. That means any modification that would be done to form the new object will instead be done to the global object. That's bad. In other words calling a constructor function without new will clobber the global object with no warnings and no errors. The conventional way to avoid this serious problem is to name all constructor functions with a leading capital letter.

But even better is to not have constructors at all.

### Prototypal

In a purely prototypal pattern there are no classes but focus on the objects.

Using the `Object.create` method from _JavaScript: The Good Parts_ chapter 3.

```javascript
var myMammal = {
  name: 'Herb the Mammal',
  get_name: function (  ) {
    return this.name;
  },
  says: function (  ) {
    return this.saying || '';
  }
};

var myCat = Object.create(myMammal);
myCat.name = 'Henrietta';
myCat.saying = 'meow';
myCat.purr = function (n) {
  var i, s = '';
  for (i = 0; i < n; i += 1) {
    if (s) {
      s += '-';
    }
    s += 'r';
  }
  return s;
};
myCat.get_name = function (  ) {
  return this.says() + ' ' + this.name + ' ' + this.says();
};
```

That is differential inheritence: customizing a new object by differentiating from an existing object.

### Functional

None of the inheritence patterns so far have provided a means for privacy. But there is an approach that can provide private data and methods.

The approach is to write a function to produce objects. The function

1. creates a new object
2. defines private instance variables and methods (optional)
3. Augments the new object with methods. The methods will have access to the private variables and methods from 2.
4. returns the new object


