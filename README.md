# Understanding JavaScript Closures
Closures in JavaScript can be a difficult concept to grasp for those either new to the language or new to programming. As part of my own learning process I want to explain closures here to anyone who has struggled to find a simple and concise set of examples.

## What problem do closures solve?
Closures are one way to solve the problem of having private variables in Javascript.

In a language like C#, the "private" keyword can be used to protect class members from being directly accessed from outside the class. 

Example below taken and adapted from https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/private
```c#
class Employee
{
    private string name = "FirstName, LastName";
    private double salary = 100.0;

    public string GetName()
    {
        return name;
    }

    public double GetSalary()
    {
        return salary;
    }
}

class PrivateTest
{
    static void Main()
    {
        Employee e = new Employee();

        // The data members are inaccessible (private), so
        // they can't be accessed like this:
        //    string n = e.name;
        //    double s = e.salary;

        // 'name' is indirectly accessed via method:
        string n = e.GetName();

        // 'salary' is indirectly accessed via method:
        double s = e.GetSalary();
    }
}
```
In JavaScript, we don't have this option so readily available. We have to implement private variables in a different way. Closures are just one way of getting this done.

## What is a closure?
I'll save my attempt to define this for you for later. I'd like to build up to a definition one example at a time.

To understand closures, we first need to talk a bit about variable scope in JavaScript. We can define a variable in JavaScript with the "var", "let", and "const" keywords. We'll just concentrate on "var" and "let".

```javascript
{
    var foo = "bar";
}
console.log(foo); // "bar"
```
In the example above, variable foo is defined with the keyword "var" inside a block. When we print to the console outside the block, we can still access the variable foo without any trouble. The reason that this works is to do with the nature of the "var" keyword. According to the Mozilla Developer Network (MDN), "[t]he scope of a variable declared with var is its current execution context, which is either the enclosing function or, for variables declared outside any function, global." In our case, variable foo is not wrapped up in a function so that means that it's going to be available to the global scope. It doesn't matter if it's nested fifty blocks deep, it's still going to be accessible anywhere in our JavaScript program.

We'll get to the "execution context of the enclosing function" stuff a bit later. Let's have a look at another example.

```javascript
{
    let foo = "bar";
}
console.log(foo); // Uncaught ReferenceError: foo is not defined
```
Obviously, the "let" keyword is changing the situation a bit. Our foo variable is no longer accessible outside the block. According to MDN, "let allows you to declare variables that are limited in scope to the block, statement, or expression on which it is used." So our foo variable is now "closed" inside the block scope and private to the block.

So if we take this example one step further, we can create a simple closure. Let's take a look.

```javascript
var getFoo;
{
    let foo = "bar";
    getFoo = function(){
        return foo;
    }
}
getFoo(); // "foo"
```

We know that the function inside the block will have access to foo becuase it's in the same block. Variable foo is private to the block because it was declared with the "let" keywork, but at the same time we've give access to foo from outside the block with a function getFoo. This is now a simple closure in JavaScript.

MDN defines a closure as "the combination of a function and the lexical environment within which that function was declared." So getFoo is a closure becuase it is a function that comes with a bag of goodies (lexical environment); the block that variable foo is in and anything else that might be contained there. A function in JavaScript has access to whichever scope that it is defined in. A closure is a way to use this to your advantage by strategically placing a function into a scope where the goal is to gain access outside of the scope. So in other words, declaring a function in a scope and making sure that you give access to that function outside of the scope.

If imagery helps, think of it this way. 

There could be a locked castle with no key:
```javascript
{
    let castle = {};
}
console.log(typeof(castle)); // undefined
```
If we needed to give access to the castle we will need to create a key:
```javascript
{
    let castle = {};
    function key() {
        return castle;
    }
}
console.log(typeof(key())); // undefined
```

There's still a problem here! Think of the first set of brackets as the usual water that surrounds a castle {}. It's like having the key to the castle on the lawn in front of the gate. You still need to get across the water to get the key!

Here's the solution, make the key available on the other side of the water:
```javascript
var key;
{
    let castle = {};
    key = function() {
        return castle;
    }
}
console.log(typeof(key())); // object
```

## Wait! Usually closures look much more complicated than that!
Although the examples above may be simplistic, they are still JavaScript closures. The point is to show how placing a function inside another scope is like sending in a spy to an enemy base. That spy may have tons of useful information for us but we need a communication route outside the enemy base in order to have access to that information. 

Usually the barrier to understanding closures is the syntactical complexity piled on top of them. Let's break down the syntax a bit.

### Form 1: A function that returns a function
Since we know that strategically declaring a function inside a specific scope gives the function access to that scope, having a way to call that function outside the scope gives us access to it's contents. Here's one way we can do it without using the "let" keyword inside a random block.

```javascript
function bar() {
    var foo = 5;
    return function() { 
        foo++;
        return foo; 
    }
}

var myBar = bar();
myBar(); // 6
myBar(); // 7
myBar(); // 8
console.log(foo); // ReferenceError: foo is not defined
```
The core principle of a closure still applies here. We've got a function declared inside a scope that we normally wouldn't have access to in order to gain access to that scope's contents. 

When we declare myBar to be the result of bar(), myBar becomes a function which has access to the scope that it was declared in and therefore has access to foo. Variable foo cannot be safely destroyed by the garbage collector in JavaScript because it's still potentially needed by myBar. What would happen if myBar() were called and the variable foo no longer existed? For this reason foo is kept alive. 

### Form 2: An IIFE (pronounced: iffy). A whaty???
The acronym IIFE stands for Immediately Invoked Function Expression. The examples below are equivalent. The only difference is that the second example requires less syntax.

```javascript
function foo(){
    return "bar"
}
foo(); // "bar"
```

```javascript
(function() {
    return "bar"
})(); // "bar"
```

The fact that the function declaration is wrapped in parenthesis, (function() { return "bar" }), makes it a function expression. The second set parenthesis, (), is what immediately invokes it.

So a closure using an IIFE, will look something like this.

```javascript
var bar = (function() {
    var foo = 5;
    return function() {
        foo++;
        return foo;
    }
})();

bar(); // 6
bar(); // 7
bar(); // 8
console.log(foo); // ReferenceError: foo is not defined
```

So all we've basically done here is improve on the example from Form 1. Declaring and invoking the function "bar" is now a one step process. Since the function expression is a nameless or "anonymous" function, we've saved one variable in the process as well. We don't need "myBar" this time to hold the result of the function; "bar" already does this.

In short, you will often see IIFEs being used to create closure becuase it's so economical.

### Form 3 The modular pattern
The modular pattern takes the example in Form 2 one step further. Take a look at the example below:

```javascript
var module = (function(){
    // Private variables, sometimes marked with an underscore
    var _foo = "foo";

    return {
        // These will be a public functions to get and set _foo
        getFoo: function(){ return _foo },
        setFoo: function(newVal) { _foo = newVal }
    }
})();

module.getFoo(); // "foo"
module.setFoo("bar");
module.getFoo(); // "bar"
console.log(module._foo); // undefined
```

Our IIFE is now returning an object with a set of functions, not just one function. The modular pattern returns an object. Anything that gets returned in the object with be public. Anything from the IIFE that is not returned in the object will be private. We are using our module to gain full control of how our private variable is retrieved and set.


## Conclusion
There are endless informative articles on JavaScript closures on the Internet. Hopefully, my way of explaining it may make the difference between understanding and continued frustration for someone else out there who's been struggling to grasp this concept. In any case, I hope you've got something out of this article. Thanks for reading and happy coding!
