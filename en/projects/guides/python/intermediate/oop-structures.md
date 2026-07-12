# Object-Oriented Programming (OOP) and Basic Data Structures

In this section, we will examine the Object-Oriented Programming (OOP) principles we use to embody abstract concepts in the software world and data structures, which are the cornerstones of computer science.

Topics we will cover:
- Encapsulation and hidden (private) variables.
- Inheritance and expanding by overriding existing methods (Method Overriding).
- Designing LIFO (Stack) and FIFO (Queue) data structures with Python lists.
- Magic Methods and object state management.

---

## 1. Basic OOP, Encapsulation, and Stack Structure

In computer science, a Stack is a structure where data is stacked on top of each other like clothes. This structure works on the **LIFO (Last In, First Out)** principle. So, the last element you add to the stack is the first element you will take from the stack. You can think of this like books in a box; you cannot reach the ones at the bottom without taking the book on top.

In Python, when creating a class, we use a double underscore (`__`) to hide our data from the outside world and prevent it from being changed directly. This is called **Encapsulation**. In the example below, the `__stk` list is kept hidden and closed to random external interventions.

```python
class Stack:
    """
    A simple stack (LIFO: Last In, First Out) implementation.
    This class provides basic add (push) and remove (pop) operations.
    """

    def __init__(self):
        self.__stk = []  # Hidden list to hold elements

    def push(self, val):
        """
        Adds a new element to the top of the stack.
        """
        self.__stk.append(val)  # We add the element to the end of the list

    def pop(self):
        """
        Removes and returns the element at the top of the stack.
        """
        val = self.__stk[-1]    # Get the last element
        del self.__stk[-1]      # Delete the last element from the list
        return val
```

---

## 2. Class Inheritance and Method Overriding

Sometimes we want to add new capabilities to a base class we wrote while preserving its properties. Instead of writing code from scratch, we proceed by inheriting from the existing class. This is called **Inheritance**.

If we want to change or expand the behavior of a function in the class we inherited from, we redefine that function with the same name in the new class. This is called **Method Overriding**.

The `CountingStack` class below inherits from the basic `Stack` class above. Its purpose is to count how many times additions and removals are made in the background while performing stack operations exactly the same.

```python
class CountingStack(Stack):
    """
    Advanced stack class that counts push and pop operations.
    Suitable for tracking stack usage.
    """
    def __init__(self):
        Stack.__init__(self)     # We call the constructor of the base class
        self.__push_counter = 0  # Counter for the push operation
        self.__pop_counter = 0   # Counter for the pop operation

    def get_push_counter(self):
        """
        Returns the total number of push operations performed.
        """
        return self.__push_counter

    def get_pop_counter(self):
        """
        Returns the total number of pop operations performed.
        """
        return self.__pop_counter

    def total_operations(self):
        """
        Returns the total number of operations performed (push + pop).
        """
        return self.__push_counter + self.__pop_counter

    def push(self, val):
        """
        Adds an element to the stack and increases the push counter by one.
        """
        self.__push_counter += 1
        return Stack.push(self, val)  # We call the push method in the base class

    def pop(self):
        """
        Removes an element from the stack and increases the pop counter by one.
        """
        self.__pop_counter += 1
        return Stack.pop(self)  # We call the pop method in the base class


# Usage Example
if __name__ == "__main__":
    my_stack = CountingStack()
    for i in range(100):
        my_stack.push(i)
        my_stack.pop()

    print("Push count:", my_stack.get_push_counter())        # Output: 100
    print("Pop count:", my_stack.get_pop_counter())          # Output: 100
    print("Total operations:", my_stack.total_operations())  # Output: 200
```

---

## 3. Queue Structure and FIFO Principle

A Queue works with the exact opposite logic of a stack. Just like a real-life bakery or bank queue, it is based on the **FIFO (First In, First Out)** principle. The first person to enter the queue completes their transaction first and leaves.

To simulate this in Python lists, we add the newly arriving element to the very beginning of the list (0th index) with `insert(0, element)`. When removing an element, we take the element at the end of the list with `pop()`. Thus, since the first added element will stay on the far right, it will be the first to come out.

```python
class Queue:
    """
    A simple queue implementation (FIFO - First In, First Out).
    """
    def __init__(self):
        self._queue = []  # The list where we will store the elements
    
    def put(self, elem):
        """Adds a new element to the queue."""
        self._queue.insert(0, elem)  # Place the new element at the very beginning of the list
    
    def get(self):
        """Removes and returns the next element in the queue."""
        return self._queue.pop()     # Remove the element at the end of the list (the oldest)
    
    def isempty(self):
        """Returns True if the queue is empty, False if it is full."""
        return not self._queue       # Returns True if the list is empty
        

# Usage Example
my_queue = Queue()
my_queue.put(1)        # Add a number
my_queue.put("dog")    # Add a string
my_queue.put(False)    # Add a boolean value

for i in range(4):     # Let's try to pull an element 4 times
    if not my_queue.isempty():
        print(my_queue.get())  # Print the element in the queue
    else:
        print("Queue is empty")  # Warn if there are no elements left in the queue
```

*Note: When working with large data, the `insert(0)` operation is slow in standard Python lists because it shifts all elements to the right. In performance-critical situations, the `collections.deque` structure is preferred instead.*

---

## 4. Magic Methods and State Management

In Python, methods inside classes that start and end with double underscores, like `__str__` and `__init__`, are called **Magic Methods**. These methods determine how objects will respond to the language's own built-in functions.

For example, when we want to print an object we created directly to the screen as `print(object)`, Python searches for the `__str__` method in the background. If we define this method inside the class, we can determine ourselves in what format the object will appear on the screen.

The `Timer` class below models a timer by holding hour, minute, and second values. It manages boundary overflow situations (like becoming 00:00:00 after 23:59:59) that might occur if time flows forward or backward.

```python
def two_digits(val):
    """
    Converts a number to a two-digit text.
    If the number is a single digit, it adds a zero to the beginning.
    Example: 5 -> "05", 12 -> "12"
    """
    s = str(val)
    if len(s) == 1:
        s = '0' + s
    return s


class Timer:
    """
    A timer class that holds time in hours, minutes, and seconds.
    """

    def __init__(self, hours=0, minutes=0, seconds=0):
        """
        Creates a new Timer object.
        Hour   : 0–23
        Minute : 0–59
        Second : 0–59
        """
        self.__hours = hours       # Hidden hour value
        self.__minutes = minutes   # Hidden minute value
        self.__seconds = seconds   # Hidden second value

    def __str__(self):
        """
        Returns the time as a text in hh:mm:ss format.
        This output is triggered when the print() function is called.
        """
        return (
            two_digits(self.__hours) + ":" +
            two_digits(self.__minutes) + ":" +
            two_digits(self.__seconds)
        )

    def next_second(self):
        """
        Moves the timer one second forward.
        """
        self.__seconds += 1                    # Increase the second

        if self.__seconds > 59:                # If the second limit is exceeded
            self.__seconds = 0
            self.__minutes += 1                # Increase the minute

            if self.__minutes > 59:            # If the minute limit is exceeded
                self.__minutes = 0
                self.__hours += 1              # Increase the hour

                if self.__hours > 23:          # If the hour limit is exceeded (If the day is over)
                    self.__hours = 0

    def prev_second(self):
        """
        Moves the timer one second backward.
        """
        self.__seconds -= 1                    # Decrease the second

        if self.__seconds < 0:                 # If the second falls below zero
            self.__seconds = 59
            self.__minutes -= 1                # Decrease the minute

            if self.__minutes < 0:             # If the minute falls below zero
                self.__minutes = 59
                self.__hours -= 1              # Decrease the hour

                if self.__hours < 0:           # If the hour falls below zero
                    self.__hours = 23


# Test Codes
timer = Timer(23, 59, 59)
print(timer)          # Output: 23:59:59

timer.next_second()
print(timer)          # Output: 00:00:00 (Transition to a new day check successful)

timer.prev_second()
print(timer)          # Output: 23:59:59 (Backward limit check successful)
```
