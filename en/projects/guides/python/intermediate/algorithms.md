# Algorithm Logic, Data Validation, and String Processing in Python

In this section, we will cover the following fundamental topics that we will need most when solving problems with Python:
- Methods of manipulating texts (strings) without using built-in functions.
- Solutions to classic interview problems such as palindrome, anagram, and searching for a word within a text.
- "Defensive Programming" techniques that prevent the program from crashing.
- The use of two-dimensional lists (matrices) and `set` for uniqueness checking.

---

## 1. String Processing and Background Algorithms

Normally, we use the built-in `str.split()` method in Python to separate a text into its words. But how do these things work in the background? The best way to grasp the logic of a method is to write it from scratch. 

The `mysplit` function below finds the words in the text without using any built-in methods. It successfully handles all "edge cases" such as having multiple spaces at the beginning, at the end, or between words.

```python
def mysplit(strng):
    """
    Splits the text by spaces without using the built-in split() method.
    """
    mylist = []  # The list where we will collect the words
    word = ""    # The temporary variable where we will combine characters to make a word

    # If the text is empty or consists only of spaces, return an empty list directly
    if not strng or strng.isspace():
        return mylist

    # Let's examine each character in the text one by one
    for i in strng:
        if i != " ":
            # Create the word by adding non-space characters side by side
            word += i
        else:
            # If we encounter a space and we have accumulated a word, add it to the list
            if word != "":
                mylist.append(word)
                word = ""  # Clear the memory for the new word

    # Don't forget to add the last remaining word inside to the list when the loop ends
    if word != "":
        mylist.append(word)

    return mylist

# Let's Test
print(mysplit("To be or not to be, this is a question"))
print(mysplit("To be or not to be,this is a question"))
print(mysplit("   "))   # Output: []
print(mysplit(" abc "))  # Output: ['abc']
print(mysplit(""))      # Output: []
```

### What We Need to Know:
*   **Performance:** Because this function scans all characters in the text in a single pass, it works with linear, i.e., O(n) time complexity.
*   **Built-in Function Advantage:** In real projects, of course, we should use Python's own `split()` method. Because it is written in C language in the background and runs much faster. It also supports different separators like commas or dots.

---

## 2. Character and Text-Based Problems

Let's gather the 4 popular text problems that we encounter most in software interviews and algorithm competitions under a single roof. 

### 1. Palindrome (Reading Backwards)
It is the case where a word or sentence reads the same forwards and backwards. We need to check it by ignoring uppercase/lowercase letters and spaces.

### 2. Anagram (Letter Swapping)
It checks whether two different words consist of exactly the same letters. For example, the words "listen" and "silent" are anagrams of each other.

### 3. Digit of Life
It is an algorithm of continuously adding all the digits in a birth date among themselves until a single digit remains.

### 4. Find a Word
It checks whether the letters of the first word exist in the second text in order (even if there are other letters in between).

Here are the functions containing these 4 games:

```python
def palindrome():
    """Checks whether the text received from the user is a palindrome."""
    pl = input("Please enter a text: ").lower().replace(" ", "")
    r_pl = pl[::-1]  # We are reversing the text

    if pl == "" or pl != r_pl:
        print("This is not a palindrome.")
    else:
        print("Great! This is a palindrome.")


def anagram():
    """Verifies whether two texts are anagrams of each other."""
    pl1 = input("Enter the first text: ").lower().replace(" ", "")
    pl2 = input("Enter the second text: ").lower().replace(" ", "")

    # If both are the same when we sort the letters, it is an anagram
    if sorted(pl1) != sorted(pl2) or pl1 == "":
        print("Not an anagram.")
    else:
        print("Yes, these are anagrams!")


def digit_of_life():
    """Reduces the birth date to a single-digit number."""
    bd = input("Enter your birth date in DDMMYYYY format (e.g., 15031990): ").strip()

    # Run the loop until the number remains a single digit
    while len(bd) > 1:
        total = 0
        for i in bd:
            total += int(i)
        bd = str(total)  # Convert the new total to a string and put it back into the loop

    print("Your Digit of Life:", bd)


def find_word():
    """Checks if the letters of the first word are present in the second text in order."""
    word = input("The word you are looking for: ").upper()
    strn = input("The text to search inside: ").upper()

    found = True
    start = 0

    for i in word:
        # We search for the letter after the last index we left off
        pos = strn.find(i, start)
        if pos == -1:
            found = False
            break
        else:
            start = pos + 1  # Shift the index to search for the next letter

    if found:
        print("The word is hidden inside the text!")
    else:
        print("Unfortunately, the word could not be found.")
```

---

## 3. Exception Handling and Defensive Programming

Users do not always enter the data our program expects. They might enter a letter where we want an integer (`int`), or they might go outside the range we allow. A good developer writes code by anticipating that the user might make a mistake.

The `read_int` function below is a safe pattern that runs until the user enters a valid number in the correct range and prevents the program from crashing in case of an error.

```python
def read_int(prompt, min, max):
    """
    Safely takes an integer from the user within a specific range.
    Prevents the program from crashing on faulty inputs.
    """
    ok = False  # The flag that will control the loop
    
    while not ok:
        try:
            # Take input from the user and try to convert it to a number
            value = int(input(prompt))
            ok = True  # If the conversion is successful, we can break the loop for now
        except ValueError:
            # If the user enters a letter, the program does not crash, it falls here
            print("Error: You entered an invalid number.")
        
        if ok:
            # Check if the number is in the correct range
            ok = value >= min and value <= max
        
        if not ok:
            # If the number is out of bounds or the conversion fails, go back to the beginning
            print(f"Error: The value you entered is not in the permitted range ({min}..{max})")
    
    return value

# Usage Example
v = read_int("Please enter a number between -10 and 10: ", -10, 10)
print("The safe number you selected:", v)
```

---

## 4. Two-Dimensional Lists (Matrices) and Set Theory — Sudoku Validator

Matrices are two-dimensional data structures that contain lists within lists. The rules of a Sudoku game are a perfect fit for understanding matrix operations.

For a Sudoku board to be considered valid:
1.  The digits 1-9 must be unique in each of the 9 rows.
2.  The digits 1-9 must be unique in each of the 9 columns.
3.  The digits 1-9 must be unique in each of the nine 3x3 sub-squares.

Here we will utilize the `set` structure in Python. Since sets keep the elements inside them unique, comparing the numbers we have with `set("123456789")` will make our job much easier.

```python
def sudoku_validator():
    """Verifies the 9x9 Sudoku board received from the user."""
    
    def checkset(digs):
        """Checks if the given series contains all the digits from 1-9 completely."""
        return set(digs) == set("123456789")

    rows = []

    # Stage 1: Let's safely get all 9 rows from the user
    for i in range(9):
        rowi = input(f"Enter a 9-digit number for Row {i+1}: ")
        # Force until the input is 9 digits and consists entirely of numbers
        while len(rowi) != 9 or not rowi.isdigit():
            print("Invalid input! Please type exactly 9 digits.")
            rowi = input(f"Enter AGAIN for Row {i+1}: ")
        rows.append(rowi)

    # Stage 2: Check the Rows
    for row in rows:
        if not checkset(row):
            print("Result: INVALID (Row error)")
            return

    # Stage 3: Check the Columns (We extract columns using List Comprehension)
    for i in range(9):
        col = [row[i] for row in rows]
        if not checkset(col):
            print("Result: INVALID (Column error)")
            return

    # Stage 4: Check the 3x3 Sub-Squares
    for r in range(0, 9, 3):
        for c in range(0, 9, 3):
            sqr = []
            for i in range(3):
                # We collect the 3x3 pieces with matrix slicing
                sqr.extend(rows[r+i][c:c+3])
            if not checkset(sqr):
                print("Result: INVALID (3x3 Square error)")
                return

    # If it passed all tests
    print("Result: CONGRATULATIONS! The Sudoku is perfectly valid.")
```

---

## 5. Main Menu That Runs All Programs

To test all these algorithms we wrote above through a single main file, we add the following control mechanism and main menu to the bottom of our project:

```python
def main():
    """The main menu that allows the user to choose the desired program."""
    print("\n--- CHOOSE THE PROGRAM YOU WANT TO RUN ---")
    print("1. Palindrome Checker (Reading Backwards)")
    print("2. Anagram Checker (Letter Matching)")
    print("3. Digit of Life Calculator")
    print("4. Find a Word Game (Word Puzzle)")
    print("5. Sudoku Validator")

    choice = input("Your choice (1-5): ").strip()

    if choice == "1":
        palindrome()
    elif choice == "2":
        anagram()
    elif choice == "3":
        digit_of_life()
    elif choice == "4":
        find_word()
    elif choice == "5":
        sudoku_validator()
    else:
        print("You made an invalid choice.")

if __name__ == "__main__":
    main()
```
