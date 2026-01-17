# Chapter 2 Exercises

[← Back to Chapter Two](chapter02.md)

---

## Exercise 1: User Input and f-Strings

Write a Python program that:

1. Asks the user to enter their **name**
2. Asks the user to enter their **age**
3. Uses an **f-string** to display the following message:

   ```
   Hi <name>. You are <age> years old.
   ```

**Example Output:**
```
Enter your name: Alice
Enter your age: 20
Hi Alice. You are 20 years old.
```

---

## Exercise 2: Using the requests Library

Write a Python program that uses the **requests** library to retrieve information about a GitHub user.

Your program should:

1. Ask the user to enter their **GitHub username**
2. Use the GitHub API endpoint: `https://api.github.com/users/<username>`
3. Make a GET request using the `requests` library
4. Print the JSON response

**Example Output:**
```
Enter GitHub username: octocat
{'login': 'octocat', 'id': 1, 'node_id': 'MDQ6VXNlcjE=', 'avatar_url': 'https://github.com/images/error/octocat_happy.gif', 'gravatar_id': '', 'url': 'https://api.github.com/users/octocat', ...}
```

**Hint:** You'll need to:
- Import the `requests` library
- Use `requests.get()` to make the API call
- Use `response.json()` to parse and print the JSON response
