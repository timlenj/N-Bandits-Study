# Analyzing Evaluative Feedback concepts in the Sutton RL Text

In the Sutton and Barto RL book, the authors outline several algorithms to tackle the N Armed Bandit Problem. You can think of the N Armed Bandit as an MDP or MRP with a single state and N actions. If MDP, MRP or Markov sound like alien to you; you can imagine the "N Armed Bandit" as a slot machine with N arms. Each arm you pull generates a reward. You do not know the reward for pulling each arm until you pull it.

I assumed that the rewards are stationary, meaning the reward each arm gives you is deterministic (if you pull the first arm multiple times, you will always get the same reward). I did this because laziness mostly :). Although I did set up the algorithms to handle stochastic action values.

Below are several of the algorithms outlined in the book.

## Epsilon Greedy
** Components **
* **Q**: Action-Value Vector. This vector holds the **estimate** of the reward for action a. It is important to remember that these are estimates. As such, each element in the vector is defined as a function. In my algorithms I use a simple average of the rewards for the action. Also important to note, in the case where rewards are stationary, this does not matter as the estimate is the true value.
* **Epsilon**: This is the probability that you "explore". 1 - epsilon is the probability that you "exploit" by greedily selecting the max Action Value Estimate from Q.
* **Exploration Method**: This isn't explicity defined in the book, however once your program decides to explore (by selecting a random value less than or equal to epsilon), you have to define how it will explore. The text describes two methods, first is to create a univariate distribution from Q and select from there. That is, each element in Q has (1 / len(Q)) probability of being selected. The second is using a soft max method. This just means that we increse the probability of the higher valued Action Value Estimates in Q and decrease the probabilities of the lower valued estimated. In the book they use a Boltzmann distribution, which is a parameterized distribution which can then be converted to uniform, a simple weighted average to a "max value favored" weighted average based on the parameter values you select. It is called the Boltzmann distribution and is pretty cool and useful, I recommend reading up on it. I did not know of this distribution before reading the book.
* **Action Value Estimate Method**: As mentioned above, Q(a) is an estimate and we need a function to define the estimate. The book uses the average reward for each time we selected the action, but one can get more creative.

## Reinforcement Comparison
** Components **
Okay, before we even get into these next two algorithms, you have to understand a concept called "Incremental Implementation." A simple (probably oversimplified) explanation is that it is just a method to update existing values. You have a value that is an estimate, you draw a new value to update that estimate (this new value is called the target), then you update that estimate using a portion of the delta between the new and old value.

Updated Value = Old Value + parameter(New Value - Old Value)

(New Value - Old Value) is the error in the estimation. You are just adding a little bit of the error to the old value to nudge it closer to the "true" value (which we assume is the new value we just drew). The text outlines various ways to understand convergence of Incremental Implementation methods. It is important but I won't go into it here, because this repo is meant for beginners. Just know that if the "true value" never converges and the parameter is constant, our Updated Value (really just the guess for the true value) will never converge. We can force convergence by having the parameter itself be a convergent sequence. A simple example of a convergent sequence is 1/n as n->infinity.

Back to the components.
* **Selection Probabilities**: The probability of selecting an action. This is updated as we go along to favor large values. In the book, we use a version of the Boltzmann distribution where the parameter = 1.
* **Reference Reward**: This is the number we use to judge the reward we receive. Think of it as a baseline we use to determine if a reward is good or bad.
* **Parameters**: Alpha and beta, the learning rate/step rate/whatever you want to call it. This is what the number that multiply our "error" with before we add it with the old estimate. Large numbers means we learn faster, but our estimates can oscillate around the true value, small numbers means we can get closer to the true value, but learn slower. This is why it is often convenient to use a convergent function as the parameter. 

We run through the algorithm, using Incremental Implementation to update the probabilities of selecting each action and the reference reward.

## Pursuit
This read to me as a mix between Reinforcement Comparison and Epsilon Greedy. Like RC, we use Incremental Implementation to calculate the probabilities of selecting each state (although the example in the book has us calculating the probabilities in a more direct manner than the book's example for RC). Like Epsilon Greedy, we must keep track of our Action Value estimates in our Q vector.
** Components **
* **Selection Probabilities**
* **Q:** Action Value Estimate vector
* **Action Value Estimate Method**
* **Parameters**: Beta as the learning parameter

This algorithm is simple. You start with the probability distribution of selecting an action being uniform, meaning each action has the same chance of being selected. You select an action, you then update Q with that action's value (using the Action Value Estimate function). The algorithm then selects the max estimate in Q, increments the probability of it being selected by Beta(1 - current probability), and decrements the probability of selecting the other actions (uniformly).


## Testing
Below are some results from tests I ran for myself. You can view the results in the notebook. I assumed stationary rewards. Rewards were generated using numpy normal distribution generator with mean at 1 and standard deviation = 3 (so not normal at all). I wanted increased variability to challenge the algorithms. In the future, I will combine these methods to create my own algorithms.

I ran through 10 epsiodes with a 1000 step horizon and averaged the results. In the graphs F(y) is the average of the selected values as of x=t divided by the max value in the rewards vector. I did this instead of displaying the actual reward at time x=t. When I tried the latter, the graphs were extremely jagged (due to exploration). 

### Epsilon Greedy (temperature = .5, epsilon = .1)
* Uniform selection tends to outperform Softmax w/ Boltzmann when using non-optimistic initialization 
* Softmax w/ Boltzmann tends to outperform uniform when using optimistic initialization of the action value vector
	* I believe this is due to Boltzmann at the current temperature discouraging exploration compared to uniform distribution

### Reinforcement Comparison
* My results conclude that reinforcement comparison using Boltzmann requires more data and creative parameterization to compete with Epsilon Greedy in this problem given the size of the action vector

### Pursuit
* Pursuit seems to outperform all algorithms in the non-optimistic case, but underperofrms Epsilon Greedy methods in the optimistic case

