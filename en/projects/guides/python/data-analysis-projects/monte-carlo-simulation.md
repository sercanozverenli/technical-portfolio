# Data Analysis with Python: Monte Carlo Simulation and Random Walk

In this section, we will solve a probability problem using the **Monte Carlo Simulation** method, which is frequently used in data science and statistics. We will see how to reach definitive results by imitating (simulating) real-life uncertainties thousands of times in a computer environment.

### Project Scenario: Empire State Building Game
Imagine you made a bet with a friend. You are standing on the steps of the Empire State building and you will roll a die:
- If the die rolls **1 or 2**: You will go down 1 step (no going below 0).
- If the die rolls **3, 4, or 5**: You will go up 1 step.
- If the die rolls **6**: You will roll the die one more time and go up as many steps as the rolled number.
- Extra Clumsiness Situation: At each step, there is a **0.1% (0.001)** chance that you might slip on the stairs and fall down to the bottom step (0).

You will roll the die **100 times** in total. The question is: **What is the probability of you reaching the 60th step or higher at the end of this game?**

Calculating this probability mathematically on paper is very difficult because of the clumsiness factor. However, we can easily find the result by having the computer play this game **500 times in a row**.

---

## 1. Data Modeling with numpy and matplotlib

To do this simulation, we will use Python's math and data analysis library `numpy` and the graph plotting library `matplotlib`.

In the code block below, we calculate all the steps of 500 different games (random walks), then to analyze the data, we take the transpose (reverse) of the matrix to determine the final steps reached.

```python
import numpy as np
import matplotlib.pyplot as plt

# We choose a fixed starting (seed) point to generate the same random numbers in every run
np.random.seed(123)

# List of 500 different simulations (random walks)
all_walks = []

for i in range(500):
    random_walk = [0]  # Every game starts from the 0th step
    for x in range(100):    # The die will be rolled 100 times
        current_step = random_walk[-1]
        dice = np.random.randint(1, 7)  # Random integer between 1 and 6
        
        if dice <= 2:
            # If 1 or 2 comes up, go down but don't fall below 0
            current_step = max(0, current_step - 1)
        elif dice <= 5:
            # If 3, 4, or 5 comes up, go up 1
            current_step = current_step + 1
        else:
            # If 6 comes up, roll the die again and go up by the rolled number
            current_step = current_step + np.random.randint(1, 7)
        
        # Clumsiness factor: 0.1% chance of falling to the ground and returning to 0
        if np.random.rand() <= 0.001:
            current_step = 0
            
        random_walk.append(current_step)
    all_walks.append(random_walk)

# To analyze the data more easily, we convert it to a numpy array and take its transpose (reverse)
np_walk_matrix = np.transpose(np.array(all_walks))

# We select the last row of the matrix; this gives us the ending step of each of the 500 games
final_steps = np_walk_matrix[-1, :]
```

---

## 2. Data Visualization: Why a Histogram Chart?

We have 500 final steps reached at the end of 500 games. Reading these numbers as a flat list does not mean anything to us. We must draw a chart to see how the data is distributed.

**Why a Histogram instead of a Bar Chart?**
- **Bar Charts:** Used to compare independent categories (e.g., Cars, Fruits, Cities).
- **Histogram Charts:** Used to see the frequency distribution, meaning in which ranges continuous or numerical data is concentrated. Since we want to see at which numbers the final steps are concentrated, we use a histogram.

```python
# Let's draw the histogram chart of the final steps
plt.hist(final_steps, bins=10, edgecolor='black')
plt.title('Random Walk Simulation: Distribution of Final Steps')
plt.xlabel('Final Step Number Reached')
plt.ylabel('Frequency (How Many Times It Occurred)')
plt.show()
```

---

## 3. Probability Calculation and Result

Now that our simulation is complete, we can calculate the answer to our main question: **What is our probability of reaching the 60th step or higher?**

When we query `final_steps >= 60` with Numpy, we get an array consisting of `True` (1) values for those that meet the condition and `False` (0) values for those that do not. When we take the mean (`np.mean`) of this array, we directly reach the probability value we are looking for.

```python
# Calculating the probability of reaching the 60th step or higher
probability = np.mean(final_steps >= 60)

print("Your probability of reaching the 60th step: %" + str(probability * 100))
# Approximate Output: %78.4
```

### Summary Conclusion
As a result of this Monte Carlo simulation we performed, we have proven with the help of a computer that despite our risk of falling down the stairs and the luck factor of the die, our chance of reaching the 60th step in this game is a high rate of **approximately 78.4%**.
