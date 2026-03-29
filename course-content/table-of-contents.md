# Course Content Index

Course materials for PFC710 - Winter 2026

## Chapter 1
- [Chapter One: Introduction](chapter01/chapter01.md)
  - Hardware and Software (Computer Basics)
  - Algorithms and Algorithm Design
  - Problem Solving with Pseudocode
  - The Python Language
  - Programming Environments (IDEs like PyCharm)
  - Your First Python Program
  - Error Handling and Debugging (Syntax and Logic Errors)

## Chapter 2
- [Chapter Two: Programming with Numbers and Strings](chapter02/chapter02.md)
  - Variables and Constants
  - Arithmetic Operations and Expressions
  - Built-in Functions and Math Module
  - Strings and String Manipulation
  - Input/Output Operations and String Formatting
  - External Libraries and Package Management (pip, requests)

## Chapter 3
- [Chapter Three: Decisions and Relational Operators](chapter03/chapter03.md)
  - The `if` Statement
  - Relational Operators
  - Nested Branches
  - Multiple Alternatives (`elif` statements)
  - Boolean Variables and Operators (`and`, `or`, `not`)
  - Analyzing Strings (substring testing, string methods)
  - Input Validation

## Chapter 4
- [Chapter Four: Loops](chapter04/chapter04.md)
  - The `while` Loop
  - Problem Solving: Hand-Tracing
  - Application: Processing Sentinel Values
  - Common Loop Algorithms
  - The `for` Loop
  - Nested Loops
  - Processing Strings

## Chapter 5
- [Chapter Five: Functions](chapter05/chapter05.md)
  - Functions as Black Boxes
  - Implementing and Testing Functions
  - Parameter Passing
  - Return Values
  - Functions Without Return Values
  - Variable Scope
  - Exception Handling

## Chapter 6
- [Chapter Six: Lists](chapter06/chapter06.md)
  - Basic Properties of Lists
  - List Operations
  - Common List Algorithms
  - Using Lists with Functions

## Chapter 7
- [Chapter Seven: Files and Exceptions](chapter07/chapter07.md)
  - Reading and Writing Text Files
  - Text Input and Output
  - Command-Line Arguments
  - Exception Handling
  - Processing Text Files (Worked Example)
  - Handling Input Errors

## Chapter 8
- [Chapter Eight: Sets and Dictionaries](chapter08/chapter08.md)
  - Sets (creating, membership, adding/removing, set operations)
  - Dictionaries (key–value pairs, table lookups)
  - Complex Structures (combining containers)
  - Modules

## Chapter 9
- [Chapter Nine: Objects and Classes](chapter09/chapter09.md)
  - Object-oriented programming: classes, objects, encapsulation, public interface
  - Implementing a simple class; instance variables, methods, and `self`
  - Public interface, data representation, steps to implement a class
  - Constructors (`__init__`), default arguments
  - Implementing methods; class variables and constants
  - Object references, aliases, identity (`is` / `is not`), `None`

## AWS Topics

Supplementary readings for using Python with AWS (after core Python chapters). Paths are under `course-content/aws-topics/`.

- [AWS Lambda (Python)](aws-topics/lambda.md)
  - What Lambda is; handler, runtime, creating a function in the console
  - Environment variables; scheduling with EventBridge
  - Packaging dependencies; IAM and execution role
  - S3 from Lambda; logging and CloudWatch; exception handling; local testing

- [Event-driven programming on AWS](aws-topics/event-driven-aws.md)
  - Event-driven style; **producers** and **consumers**
  - S3 event notifications → **SQS** → **Lambda** as a worked example
  - SQS basics; Lambda SQS trigger and `event` shape (nested JSON)
  - Why **`GetObject`** is usually needed; per-record error handling; IAM overview

- [Amazon DynamoDB (Python / boto3)](aws-topics/dynamodb.md)
  - Tables, items, partition key and optional sort key; attribute types
  - Creating a table in the console; table name from configuration
  - **`put_item`** and **`get_item`** with **`boto3.resource`**; IAM; error handling

---

## Course Structure

This repository contains course materials organized by chapter, plus **AWS Topics** reference pages for Python on AWS. Each chapter's content includes:
- Lecture notes and slides
- Code examples
- Exercises and assignments
- Additional resources

## Getting Started

1. Navigate to the chapter you're currently studying
2. Read through the course materials
3. Follow along with code examples
4. Complete the exercises

---


