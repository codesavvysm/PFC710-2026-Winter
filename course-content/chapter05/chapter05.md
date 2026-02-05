# Chapter Five: Functions

---

## Introduction

In this chapter, you will learn how to design and implement your own functions. Using the process of stepwise refinement, you will be able to break up complex tasks into sets of cooperating functions.

---

## Chapter Goals

In this chapter you will learn:

- To implement functions
- To become familiar with the concept of parameter passing
- To develop strategies for decomposing complex tasks into simpler ones
- To be able to determine the scope of a variable

---

[← Back to Course Index](../table-of-contents.md)

## 5.1 Functions as Black Boxes

A **function** is a sequence of instructions with a name. For example, the `round` function, which was introduced in Chapter 2, contains instructions to round a floating-point value to a specified number of decimal places.



## Calling Functions

You  call  a function in order to execute its instructions

price = round(6.8275, 2) # Sets result to 6.83

By using the expression round(6.8275, 2), your program  calls  the round function, asking it to round 6.8275 to two decimal digits



## Calling Functions (2)

The round function  returns  its result back to where the function was called and your program resumes execution

![](media/image2.png "Picture 5")



## Function Arguments

When another function calls the round function, it provides *inputs*, such as the values  6.8275  and  2  in the call  round(6.8275, 2)  

These values are called the arguments of the function call

Note that they are not necessarily inputs provided by a human user 

They are the values for which we want the function to compute a result



## Function Arguments

Functions can receive multiple arguments or it is also possible to have functions with no arguments



## Function Return Values

The *output* that the round function computes is called the  return value

Functions return only one value

The return value of a function is returned to the point in your program where the function was called

price = round(6.8275, 2)

When the round function returns its result, the return value is stored in the variable `price`)



## Function Return Values (2)

Do not confuse returning a value with producing program output which is produced when using a  print()  statement



## Black Box Analogy

A thermostat is a ' black box'

Set a desired temperature

Turns on heater/AC as required

You don't have to know how it really works!

How does it know the current temp?

What signals/commands does it send to the heater or A/C?

Use functions like ' black boxes'

Pass the function what it needs to do its job

Receive the answer



## The  round  Function as a Black Box

You pass the round function its necessary arguments (6.8275 & 2) and it produces its result (6.83)



<img src="media/image3.png" alt="Picture 5" width="400">

## The  round  Function as a Black Box

You may wonder how the round function performs its job 

As a user of the function, you don't need to know how the function is implemented

You just need to know the specification of the function: 

If you provide arguments x and n, the function returns x rounded to n decimal digits



## Designing Your Own Functions

When you design your own functions, you will want to make them appear as black boxes to other programmers

Even if you are the only person working on a program, making each function into a black box pays off: there are fewer details that you need to keep in mind



## 5.2 Implementing and Testing Functions

A function to calculate the volume of a cube

What does it need to do its job?

What does it answer with?

When writing ('defining') this function

Pick a name for the function ( cubeVolume )

Declare a variable for each incoming argument

  ( sideLength ) (called parameter variables)

Put all this information together along with the  def  keyword to form the first line of the function's definition:



def cubeVolume(sideLength):

This line is called the  header  of the function

## Testing a Function

If you run a program containing just the function definition, then nothing happens

After all, nobody is calling the function

In order to test the function, your program should contain

The definition of the function

Statements that call the function and print the result



## Calling/Testing the Cube Function

def cubeVolume(sideLength):
    volume = sideLength ** 3
    return volume

result1 = cubeVolume(2)
result2 = cubeVolume(10)
print("A cube with side length 2 has volume", result1)
print("A cube with side length 10 has volume", result2)

Implementing the function (function definition)

Calling/testing the function



## Syntax: Function Definition

<img src="media/image4.jpeg" width="800">



## Programming Tip: Function Comments

Whenever you write a function, you should  comment  its behavior 

Remember, comments are for human readers, not compilers

## Computes the volume of a cube.

```python
## Computes the volume of a cube.
#  @param sideLength the length of a side of the cube
#  @return the volume of the cube
#
def cubeVolume(sideLength):
    volume = sideLength ** 3
    return volume
```

Function comments explain the purpose of the function, the meaning of the parameter variables and the return value, as well as any special requirements



## Cubes.py with Documentation

```python
##
#  This program computes the volumes of two cubes.
#

def main():
    result1 = cubeVolume(2)
    result2 = cubeVolume(10)
    print("A cube with side length 2 has volume", result1)
    print("A cube with side length 10 has volume", result2)

## Computes the volume of a cube.
#  @param sideLength the length of a side of the cube
#  @return the volume of the cube
#
def cubeVolume(sideLength):
    volume = sideLength ** 3
    return volume

# Start the program.
main()
```

![](media/image5.png "image5")



## The  main  Function

When defining and using functions in Python, it is good programming practice to place all statements into functions, and to specify one function as the starting point

Any legal name can be used for the starting point, but we chose 'main' since it is the required function name used by other common languages

Of course, we must have one statement in the program that calls the main function



## Syntax: The  main  Function 

![](media/image7.png "Content Placeholder 5")



## Using Functions: Order (1)

It is important that you define any function before you call it 

For example, the following will produce a compile-time error:

```python
print(cubeVolume(10))

def cubeVolume(sideLength):
    volume = sideLength ** 3
    return volume
```

The compiler does not know that the  cubeVolume  function will be defined later in the program



## Using Functions: Order (2)

However, a function can be called from within another function before the former has been defined 

The following is perfectly legal:

```python
def main():
    result = cubeVolume(2)
    print("A cube with side length 2 has volume", result)

def cubeVolume(sideLength):
    volume = sideLength ** 3
    return volume

main()
```


## 5.3 Parameter Passing

When you **call** a function, you pass **arguments**. The function receives those values in its **parameters**. Parameter passing is how the values get from the caller into the function.

### Arguments and parameters

- **Argument** — The value you pass in the function call (e.g. `cubeVolume(2)` or `cubeVolume(side)`). It can be a literal like `2` or the value of a variable.
- **Parameter** — The variable declared in the function definition (e.g. `sideLength` in `def cubeVolume(sideLength):`). It is initialized with the argument value when the function is called and used like any other variable inside the function.

So in `result1 = cubeVolume(2)`, the argument is `2`. Inside `cubeVolume`, the parameter `sideLength` is set to `2` for that call.

### How it works (pass by value)

Python passes the **value** of the argument into the parameter. For numbers and other simple types, the function gets a copy of the value. So when the function runs, the parameter variable holds that value and can be used in expressions—but changing the parameter variable does not change the variable in the caller.

**Example:** Steps when `result1 = cubeVolume(2)` is executed:

1. The argument `2` is evaluated.
2. `cubeVolume` is called and its parameter `sideLength` is set to `2`.
3. The function body runs (e.g. `volume = sideLength ** 3` → 8).
4. The return value is sent back and assigned to `result1`.

```python
result1 = cubeVolume(2)

def cubeVolume(sideLength):
    volume = sideLength ** 3
    return volume
```

![Parameter passing diagram](media/image8.png "Parameter passing")

### ⚠️ Common Error: Modifying parameter variables

Because the parameter holds a copy of the value (for numbers), the caller’s variables are not changed when you assign to the parameter. But **assigning to a parameter inside the function is confusing**—readers expect parameters to be “inputs” that are not reassigned. Avoid modifying parameter variables; use a local variable instead.

**Confusing (modifies parameter):**

```python
def totalCents(dollars, cents):
    cents = dollars * 100 + cents   # Modifies parameter variable
    return cents
```

**Clear (use a separate variable):**

```python
def totalCents(dollars, cents):
    result = dollars * 100 + cents
    return result
```

## 5.4 Return Values

Functions can (optionally) return one value

Add a  return statement  that returns a value

A  return statement  does two things:

- Immediately terminates the function
- Passes the return value back to the calling function


```python
def cubeVolume(sideLength):
    volume = sideLength ** 3
    return volume
```

The return value may be a value, a variable or a calculation

## Multiple  return  Statements

![](media/image13.png "Picture 8")

A function can use multiple  return  statements

But every branch must have a  return  statement


```python
def cubeVolume(sideLength):
    if sideLength < 0:
        return 0
    return sideLength ** 3
```

## Multiple  return  Statements (2)

Alternative to multiple returns (e.g., one for each branch):

You can avoid multiple returns by storing the function result in a variable that you return in the last statement of the function 

For example:

```python
def cubeVolume(sideLength):
    if sideLength >= 0:
        volume = sideLength ** 3
    else:
        volume = 0
    return volume
```

## Make Sure A Return Catches All Cases

Missing  return  statement

Make sure all conditions are handled

In this case,  sideLength  could be equal to 0

No  return  statement for this condition

The compiler will  not  complain if any branch has no  return  statement

It may result in a run-time error because Python returns the special value  None  when you forget to return a value

```python
def cubeVolume(sideLength):
    if sideLength >= 0:
        return sideLength ** 3
    # Error — no return value if sideLength < 0
```

A correct implementation:

```python
def cubeVolume(sideLength):
    if sideLength >= 0:
        return sideLength ** 3
    else:
        return 0
```

## Pyramids.py

Open the file pyramids.py

Look at how the main function is set up to make the calls to  pyramidVolume  and print the expected results

```python
##
#  This program defines a function for calculating a pyramid's volume and
#  provides a unit test for the function.
#

def main():
    print("Volume:", pyramidVolume(9, 10))
    print("Expected: 300")
    print("Volume:", pyramidVolume(0, 10))
    print("Expected: 0")

## Computes the volume of a pyramid whose base is a square.
#  @param height a float indicating the height of the pyramid
#  @param baseLength a float indicating the length of one side of
#  the pyramid's base
#  @return the volume of the pyramid as a float
#
def pyramidVolume(height, baseLength):
    baseArea = baseLength * baseLength
    return height * baseArea / 3

# Start the program.
main()
```

## 5.5 Functions Without Return Values

Functions are not required to return a value

No  return  statement is required

The function can generate output even when it doesn't have a return value

```python
def boxString(contents):
    n = len(contents)
    print("-" * (n + 2))
    print("!" + contents + "!")
    print("-" * (n + 2))
```
![](media/image14.png "Picture 7")

```python
boxString("Hello")
```

## Using  return  Without a Value

You can use the return statement without a value

The function will terminate immediately!

```python
def boxString(contents):
    n = len(contents)
    if n == 0:
        return  # Return immediately
    print("-" * (n + 2))
    print("!" + contents + "!")
    print("-" * (n + 2))
```

## 5.6 Variable Scope

The **scope** of a variable is the part of the program in which that variable is visible and can be used.

### Where variables can be declared

- **Inside a function** — These are **local variables** (including parameter variables). They are only available inside that function and are not visible to other functions.
- **Outside all functions** — These are **global variables**. They are in *global scope* and can be used (and changed) by code in any function that declares `global name` for that name.

### Local variables

Variables declared inside one function are not visible to other functions. In the following example, `sideLength` is local to `main`. Using it inside `cubeVolume` would be an error because `cubeVolume` has no access to `main`'s locals—and here `cubeVolume()` takes no parameters, so `sideLength` is undefined there.

```python
def main():
    sideLength = 10
    result = cubeVolume()   # cubeVolume has no parameters
    print(result)

def cubeVolume():
    return sideLength * sideLength * sideLength   # ERROR: sideLength not defined here
```

### Reusing names in different functions

You can use the same variable name in different functions. Each function has its own local variables, so `result` in one function and `result` in another are two different variables. Their scopes do not overlap.

### Global variables

Global variables are defined at the top level of the file (outside any function). A global variable is visible to all functions defined after it in the file. Any function that wants to **assign** to a global variable must include a `global` declaration for that name; otherwise the name is treated as a new local variable.

**Example:**

```python
balance = 10000    # Global variable

def withdraw(amount):
    global balance
    if balance >= amount:
        balance = balance - amount
```

If you omit `global balance`, then `balance` inside `withdraw` would be treated as a local variable, and the assignment would not change the global `balance`.

### Programming tip: Prefer parameters and return values

There are a few cases where globals are reasonable (e.g. constants like `pi` in the `math` module), but they are rare. Programs that rely on global variables are harder to maintain and extend, because you can no longer treat each function as a *black box* that gets inputs via parameters and sends back a result via `return`. Prefer passing data in with parameters and returning results; use globals only when necessary.

## 5.7 Exception Handling (brief overview)

Code that talks to the outside world—for example an **API call**—can fail (network error, bad response, timeout). If you don’t handle that, the program can crash. Use **try/except** so that when an error occurs, your code catches it and does something sensible (e.g. log the error, print a message, or return a default) instead of stopping.

Put the risky code in a `try` block. If an exception happens there, execution jumps to the matching `except` block. You can catch a specific exception type (e.g. `requests.RequestException` for many HTTP errors) or use a broad `except Exception` to catch any error.

**Example: handling errors around an API call**

```python
import requests

try:
    response = requests.get("https://newsapi.org/v2/everything", params={"q": "tech", "pageSize": 10}, timeout=5)
    response.raise_for_status()   # raise an error if HTTP status is 4xx or 5xx
    data = response.json()
    # ... use data ...
except requests.RequestException as e:
    print("API request failed:", e)
```

If the request fails (connection error, timeout, or bad status), the exception is caught and the message is printed; your program can continue or exit cleanly instead of crashing. You’ll see more on exceptions in a later section.

---

## Key Takeaways

1. **Functions** are named sequences of instructions; they receive **arguments** and can **return** a value.
2. **Parameter passing** supplies the values for a function’s parameters; avoid modifying parameter variables.
3. **Return values** are sent back with the `return` statement; every code path that should produce a value must return.
4. **Functions without return values** can still use `return` to exit early, or omit it to fall off the end.
5. **Stepwise refinement** breaks complex tasks into smaller functions that are easier to implement and test.
6. **Variable scope** is the part of the program where a variable is visible; local and parameter variables are confined to their function.
7. **Exception handling** with `try`/`except` lets you catch errors (e.g. from API calls) so your program doesn’t crash.

---

*End of Chapter Five*

[← Back to Course Index](../table-of-contents.md)
