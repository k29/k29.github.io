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

There can be two types of armed bandit problems:
* **stationary** — There is always the best answer and it never changes.
* **non-stationary** — There is always the best answer but it could change anytime.
In the real world, most of the examples encountered are non-stationary. 

In the **stationary** version, each of our actions has an *expected reward* given that that action is selected, called ***action value***.

$$q_\star(a) = \mathbb{E}[R_t \ \mid \ A_t = a]$$

We want $Q_t(a)$, our approximation of the action value, to be close to $q_\star(a)$, the **true value**.  $Q_t(a)$ is also commonly called the **$Q$ Value** for action $a$.

One of the main ideas in the *exploration vs exploitation* is that if we want $Q$ to be close to $q$, it isn’t sufficient to take greedy (exploitative) moves all the time. In fact, with exploring – **reward is lower in the short run, during exploration, but higher in the long run because after you have discovered the better actions, you can exploit them *many times***.

Whether to explore or exploit is a very hard question, and depends in a very complex way on the precise values of estimates, uncertainties, and number of remaining steps. There are some methods to determine what is the best tradeoff, *but they usually make very limiting assumptions*, such as stationarity and prior knowledge that can be impossible to verify or are just not true in most applications.
