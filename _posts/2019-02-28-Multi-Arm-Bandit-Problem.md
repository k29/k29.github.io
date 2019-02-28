---
layout: post
title:  "Multi Arm Bandit Problem"
date:   2019-02-28 21:32:39
comments: true
categories: RL
mathjax: true
---

A one arm bandit machine is a simple slot machine in which you insert a coin, pull a lever and get a reward. It’s equivalent to a single step Markov decision process. 

A multi arm bandit is a complicated slot machine in which there are multiple lever which a gambler can pull and each lever gives a different reward based on its probability distribution which is unknown to the gambler obviously.  The task is to identify which lever to pull in order to get a maximum reward after a given set of trials. 

These are called as bandit machines cause they are programmed in a way that gamblers end up losing all their money. (It has happened to me)

Thus you are repeatedly faced with a choice to select amongst k different options or actions. After each choice you receive a reward that is from a stationery probability distribution that depends on the action you selected. And the objective is to maximise the reward after finite number of steps say 1000. 

**Example:**

In the event of 2 arm bandit problem with Bernoulli distribution for rewards this is what it may look like for 8 steps: 
 
Arm | Reward 
--- | --- 
1 | 1
2 | 0
1| 1
2 | 0
1 | 0
2 | 0
1| 1
2 | 1

It is clear that arm 1 is better than arm 2 in this case. 

Now the data we operate on can be two types:
1.  **stationary** — There is always the best answer and it never changes.
2. **non-stationary** — There is always the best answer but it could change anytime.
In the real world, most of the examples encountered are non-stationary. 

In the **stationary** version, each of our actions has an *expected reward* given that that action is selected, called ***action value***.

$$q_\star(a) = \mathbb{E}[R_t \ \mid \ A_t = a]$$

Since we do not know the values of $$q_\star(a)$$, we calculate our approximation of the action value as well, called $$Q_t(a)$$ and we want this value to be as close to the original as possible.   $$Q_t(a)$$ is also commonly called the $$Q$$ Value for action $$a$$.

Say we start with any arbitrary action and it yields a action value, using which we update the $$Q$$ value.  Now for the next round, we can be greedy and chose the best Q value we already have, that is exploit the values we have, or we can be in exploratory mode and choose a random action, and again update the Q value for that action.  This is one of the main ideas in reinforcement learning, whether to chose to explore or exploit and in what proportion. This has wide application as a concept also, say in lab trials, when to try new drugs or use the best drug available so far. Intuitively speaking it is obviously possible to find better or worse values when we explore. It’s like going to the same cafe which you like or go to a new one. Maybe you have a better experience and find the new best for you, or maybe it’s just a waste of money. 

One of the main ideas in the exploration vs exploitation is that if we want $$Q$$ to be close to $$q$$, it isn’t sufficient to take greedy (exploitative) moves all the time. In fact, with exploring – reward is lower in the short run, during exploration, but higher in the long run because after you have discovered the better actions, you can exploit them many times.

Whether to explore or exploit is a very hard question, and depends in a very complex way on the precise values of estimates, uncertainties, and number of remaining steps. There are some methods to determine what is the best tradeoff, but they usually make very limiting assumptions, such as stationarity and prior knowledge that can be impossible to verify or are just not true in most applications.

One of the approach of solving this problem is using Action Value methods which are basically all methods that estimate values of actions and that use these estimates to pick the agent’s next action. The true* value of an action is it’s expected/mean reward. 

One natural way to estimate the value of a state is just taking the average reward obtained every time we reach that state:

$$ Q_t(a) = \frac{\text{sum of rewards when action `a` taken before time t}}{\text{number of times action `a` was taken}}$$

As the denominator goes to infinity, our estimate converges to the real value $q_\star(a)$. This is called the sample-average method.

The simples method for selecting actions is instead the greedy approach:

$$ A_t = argmax_a Q_t(a) $$

A better, but still simple approach is the $$\epsilon$$-greedy approach, in which the greedy option is selected with probability $$1-\epsilon$$ and a random action (among the remaining) is selected with probability $$\epsilon$$. Thus we explore with $$\epsilon$$ probability and exploit rest of the time. 


**Incremental Implementation**

How can these averages be computed in a computationally efficient manner?

Basically just store the average and update it at each timestep.

Given $$Q_n$$ and the $$n$$th reward $$R_n$$ (note that the $$n$$th reward forms the $$Q_{n+1}$$ estimate as $$Q_1$$ is based off of a prior – usually just set to zero), we get:

 $$Q_{n+1} = \frac{1}{n} \sum_i^n R_i = \frac{1}{n} \Bigg(R_n + (n-1) \frac{1}{(n-1)}\sum_i^{n-1} R_i\Bigg) = \frac{1}{n} \Big(R_n + (n-1) Q_{n}\Big) = Q_n + \frac{1}{n} \Big[R_n - Q_{n}\Big]$$

The update formula turns out to take the form:

$$NewEstimate \leftarrow OldEstimate + StepSize [Target - OldEstimate]$$

This is a form that comes up very often in RL. Note that $[Target - OldEstimate]$ can be considered a measure of *error* of the estimate.

Note that the step-size parameter changes from timestep to timestep, depending on the number of times that one has chosen that action. Usually *step-size* is denoted by $$\alpha_n$$.

Code:
```
class Bandit:
    def __init__(self, k, steps, e): # k: number of arms
        self.k = k
        self.steps = steps
        self.e = e
        self.average_reward = 0
        self.cum_avg_reward = np.zeros(steps)    
        
    def generate_problem(self):
        self.q_values = np.random.normal(0, 1, size=self.k) # Generate the action values q*(a) for a given problem, with mean as 0, and variance as 1
        
    def generate_reward(self, action):
        return np.random.normal(self.q_values[action], 1) # For a given action A_t the actual reward R_t is a normal distribution with mean q*(A_t) and variance 1
    
    def e_greedy_solution(self, problem_count):
        self.Q = {i: 0 for i in range(self.k)} # Store the Q values for the action 
        self.N = {i: 0 for i in range(self.k)} # Store the number of times that action happened
        
        for i in range(self.steps):
            explore = np.random.random() < self.e
            if (explore):
                action = np.random.randint(0, self.k) # Choose a random action 
            else:
                action = max(self.Q, key=self.Q.get) # Chose the action with maximum reward

            reward = self.generate_reward(action) # Get the reward for the current action
            self.average_reward += (reward - self.average_reward)/(i+1)
            self.cum_avg_reward[i] += (self.average_reward - self.cum_avg_reward[i])/(problem_count+1)
            self.N[action] += 1 # update the count number for that action 
            self.Q[action] += (1 / self.N[action]) * (reward - self.Q[action]) # Update the Q for that particular action        


e_values = [0.0, 0.01, 0.1, 0.2, 0.5, 1]

for e in e_values:
    b = Bandit(10, 3000, e)
    for j in range(3000):
        b.generate_problem()
        b.e_greedy_solution(j)
    plot(b.cum_avg_reward, label=e)
    legend(loc='best')
```

The average reward follows the below curves for different values of e as mentioned in the legend:
![](/assets/mabp1.png)

**Implementation**: Multi Arm Bandit Problem, [Github](https://github.com/k29/Multi-Arm-Bandit-Problem.git)

