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
In the example above, variable foo is defined with the keyword "var" inside a block. When we print to the console, we can still access the variable foo without any trouble. The "var" keyword
