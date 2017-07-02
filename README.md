# JavaScript-Closures
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
In JavaScript, we don't have this option. We have to implement private variables in a different way. Closures are just one option of getting this done.

## What is a closure?
I'll save my attempt to define this for you for later. I want to build up to a definition one example at a time.

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
Although my examples above may be simplistic, they are still JavaScript closures. Usually the barrier to understanding closures is the syntactical complexity piled on top of them. Let's break down the syntax a bit.

### Form 1:

