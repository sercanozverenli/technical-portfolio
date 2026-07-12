# Python for Data Analysis: Monte Carlo Simulation and Random Walk

In this section, we will solve a probability problem using the **Monte Carlo Simulation** method, which is widely used in data science and statistics. We will see how to get accurate results by mimicking real-world uncertainties thousands of times in a computer environment.

### Project Scenario: The Empire State Building Game
Imagine you made a bet with a friend. You are standing on the steps of the Empire State Building and you will roll a dice:
- If the dice is **1 or 2**: You go 1 step down (you cannot go below step 0).
- If the dice is **3, 4, or 5**: You go 1 step up.
- If the dice is **6**: You roll the dice again and go up by the number you get.
- Extra Clumsiness Condition: At each step, there is a **0.1% (0.001)** chance that you might slip and fall down to the very bottom step (step 0).

You will roll the dice a total of **100 times**. The question is: **What is the probability that you will reach the 60th step or higher at the end of this game?**

Calculating this probability mathematically on paper is very difficult due to the clumsiness factor. However, we can easily find the answer by making the computer play this game **500 times in a row**.

---

## 1. Modeling the Data with numpy and matplotlib

To run this simulation, we will use `numpy` (Python's library for mathematics and data analysis) and `matplotlib` (the library for data visualization).

The code block below calculates all the steps for 500 different games (random walks). Then, it takes the transpose of the matrix to detect the final steps for analysis.

```python
import numpy as np
import matplotlib.pyplot as plt

# Set a fixed seed to generate the exact same random numbers every time we run the script
np.random.seed(123)

# List to store all 500 different simulations (random walks)
all_walks = []

for i in range(500):
    random_walk = [0]  # Every game starts at step 0
    for x in range(100):    # The dice will be rolled 100 times
        current_step = random_walk[-1]
        dice = np.random.randint(1, 7)  # Random integer between 1 and 6
        
        if dice <= 2:
            # If 1 or 2, go down 1 step but do not drop below 0
            current_step = max(0, current_step - 1)
        elif dice <= 5:
            # If 3, 4, or 5, go up 1 step
            current_step = current_step + 1
        else:
            # If 6, roll again and go up by that amount
            current_step = current_step + np.random.randint(1, 7)
        
        # Clumsiness factor: 0.1% chance to fall down and reset to 0
        if np.random.rand() <= 0.001:
            current_step = 0
            
        random_walk.append(current_step)
    all_walks.append(random_walk)

# Convert to a numpy array and take the transpose for easier data analysis
np_walks_matrix = np.transpose(np.array(all_walks))

# Select the very last row; this gives us the final step where each of the 500 games ended
final_steps = np_walks_matrix[-1, :]
```

---

## 2. Data Visualization: Why a Histogram Chart?

We have 500 ending steps from 500 different games. Reading these numbers from a plain list does not give us a clear view. We need to draw a chart to see how the data is distributed.

**Why a Histogram Instead of a Bar Chart?**
- **Bar Charts:** Used to compare separate and independent categories (e.g., Car models, Fruit types, City names).
- **Histogram Charts:** Used to see where continuous or numerical data thickens, which means the frequency distribution. We want to see where our final steps concentrate, so a histogram is the right choice.

```python
# Plot the histogram of final steps
plt.hist(final_steps, bins=10, edgecolor='black')
plt.title('Random Walk Simulation: Final Steps Distribution')
plt.xlabel('Final Step Number')
plt.ylabel('Frequency (How many times)')
plt.show()
```

---

## 3. Probability Calculation and Conclusion

Now that our simulation is complete, we can answer our main question: **What is our probability of reaching the 60th step or higher?**

When we run the query `final_steps >= 60` with Numpy, we get an array of `True` (1) and `False` (0) values. Taking the mean (`np.mean`) of this array directly gives us the probability we are looking for.

```python
# Calculate the probability of reaching step 60 or higher
probability = np.mean(final_steps >= 60)

print("Probability of reaching the 60th step: " + str(probability * 100) + "%")
# Approximate Output: 78.4%
```

### Summary Conclusion
As a result of this Monte Carlo simulation, we have proven with the help of a computer that we have a high chance of **around 78.4%** to reach the 60th step, despite the risk of falling down the stairs and the luck factor of the dice.
```
