# JavaScript-Closures
Closures in JavaScript can be a difficult concept to grasp for those either new to the language or new to programming. As part of my own learning process I want to explain closures here to anyone who has struggled to find a simple and concise set of examples.

## What problem do closures solve?
Closures are one way to solve the problem of having private variables in Javascript.

In a language like C#, the "private" keyword can be used to protect class members from being directly accessed from outside the class. 

Example below taken from https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/private
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

