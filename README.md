# Analyzing Evaluative Feedback concepts in the Sutton RL Text

## Epsilon Greedy (temperature = .5, epsilon = .1)
* Uniform selection tends to outperform Softmax w/ Boltzmann when using non-optimistic initialization 
* Softmax w/ Boltzmann tends to outperform uniform when using optimistic initialization of the action value vector
	* I believe this is due to Boltzmann at the current temperature discouraging exploration compared to uniform distribution

## Reinforcement Comparison
* My results conclude that reinforcement comparison using Boltzmann requires more data and creative parameterization to compete with Epsilon Greedy in this problem given the size of the action vector

## Pursuit
* Pursuit seems to outperform all algorithms in the non-optimistic case, but underperofrms Epsilon Greedy methods in the optimistic case

