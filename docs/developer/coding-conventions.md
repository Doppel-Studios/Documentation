---
layout: default
title: Coding Conventions
nav_order: 1
parent: Developer
permalink: /docs/developer/coding-conventions
---

# C# Coding Conventions

---

## Naming

- Use PascalCase for method and class names
- Use camelCasing for variables
- Do prefix private variables with '_'
- Do prefix interface names with 'I'

```c#
// PascalCase class name
class ClassName
{
    // camelCase variable name
    public float publicVar;

    // '_' prefixed private variable names
    float _privateVar;
    [SerializeField] Button _serializedPrivateVar;

    // PascalCase method name
    public void MethodName(string[] parameterName)
    {

    }
}
```

---

## Layout

- Braces always used and always on a new line, even for single line if statements
- Else always on a separate line
- Seperate property and method definitions with a blank line
- Include a blank line between method definitions

```c#
class ClassName
{ // Braces always on new line
    string stringVar;
    int intVar;

    // New line between properties and methods
    public void FirstMethod()
    {
        if (true) // If always has brace
        {
            Console.WriteLine("true");
        }
        else // Else on a separate line
        {
            Console.WriteLine("false");
        }
    }

    // New line between methods
    public bool SecondMethod()
    {
        return false;
    }
}
```

---

## Variable Scoping

- Member variables are private by default. Avoid public variables if possible. 
- If a variable needs to be exposed to the Unity inspector use [SerializeField]
- If a variable needs to be accessed elsewhere use properties with the variables name in PascalCase.
- For Structs, Nested Classes, readonlys, and consts private variables are overkill and publics should be used

```c#
class ClassName : MonoBehaviour
{
    // Readonly and const are public
    public readonly int READONLY = 0;
    public const int CONST = 1;

    // All regular variables are private with SerializeField
    [SerializeField] int _getter;
    [SerializeField] ExampleStruct _setter;
    [SerializeField] ExampleNestedClass _getterAndSetter

    // Public properties expose private variables
    public ExampleStruct Getter => _getter;
    
    public int Setter
    {
        set => _setter = value;
    }

    public int GetterAndSetter
    {
        get => _getterAndSetter;
        set => _getterAndSetter = value;
    }

    [System.Serializable]
    public class ExampleNestedClass
    {
        // Nested Class variables are public
        public int var;
    }
}

[System.Serializable]
public struct ExampleStruct
{
    // Struct variables are public
    public string var;
}
```

---

## Spacing

- Parentheses and brackets should tightly wrap content
- Provide a space after flow control keywords (for, if, while, etc...)
- Provide a single space before and after operators
```c#
if (x != 0)
{
    MethodName(var, x, 5);
    x += x + (x * x);
    x = someList[index];

    while (x != y)
    {
        x++;
    }
}
```