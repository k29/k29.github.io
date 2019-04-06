---
layout: post
title: Counterfactual Regret Minimization
date: 2019-03-18 13:50:39
categories: Learning
---

Important game theoretic algorithm of regret matching was introduced by Hart and Mas-Colell in 2000. Players reach equlibrim play by tracking regrets of past play and making future plays proportional to positive regrets. This is a pretty simple and intuitive technique as demonstrated using a rock-paper-scissor game below. 

Rock paper scissors is a pretty simple game known to most people. Rock beats scissors, scissors beats paper, paper beats rock. The payoff table for RPS (rock, paper, scissor) is as follows:

|   | R    | P    | S    |
|---|------|------|------|
| R | 0,0  | -1,1 | 1,-1 |
| P | 1,-1 | 0,0  | -1,1 |
| S | -1,1 | 1,-1 | 0,0  |

Each entry takes the form of (u,v) and by convention, the row player is player 1 and the column player is player 2.

Now some game theory concepts:

**Zero sum game**: This is a situaltion when a person's gain is another person's loss. In essence the game is zero sum, if each utility vector sums to 0. One of the most famous examples of zero sum game are that of matching pennies. 

**Strategies**: There are different types of strategies that can be adopted by players which can result in different outcomes. For instance, the player may adopt a single strategy every time as it provides him/her maximum outcome or he/she can adopt multiple strategies. Apart from this, a player may also adopt a strategy that provides him/her minimum loss. Therefore on the basis of outcome, the strategies of the game theory are classified as pure and mixed strategies, dominant and dominated strategies, minimax strategy, and maximin strategy.
A pure strategy provides a complete definition of how a player will play a game. In particular, it determines the move a player will make for any situation they could face. A player's strategy set is the set of pure strategies available to that player. A mixed strategy is an assignment of a probability to each pure strategy. This allows for a player to randomly select a pure strategy. Since probabilities are continuous, there are infinitely many mixed strategies available to a player. Of course, one can regard a pure strategy as a degenerate case of a mixed strategy, in which that particular pure strategy is selected with probability 1 and every other strategy with probability 0.

Rregret Matching and Minimization:

Suppose we are playing RPS for money. Each player places a dollar on the table and you take home both if you win. Say we play rock and our opponent plays paper and wins causing us to lose a dollar. In this case we regret not having played paper and drawing but we regret not having played scissors even more because our relative gain would have been even more in retrospect. Thus regret is defined as not having chosen an action as the difference between the utility of that action and the action
and the action that we actially chose. For this example, we regret not having played paper u(paper, paper)−u(rock, paper) = 0−(−1) = 1, and we regret not having played scissors u(scissors, paper) − u(rock, paper) = +1 − (−1) = 2. 

To let this decision inform the future play, one might prefer to chose the action that one regretted the most not having chosen in the past but you don't want to be entirely predictable and thus allow opponents to exploit you. One way of implementing this through regret matching where the action is basically selection through a distribution which is proportional to the positive regrets. These regrests convey relative losses one has experienced for not having selected the action in the
past. In the above stated example we don't regret chosing rock but we definitely regret 1 and 2 for having chosen paper and scissors repectively. Thus our next action will be chosen proportionally to postive regrets i.e. we chose rock, paper and scissor with the probablity of 0, 1/3 and 2/3 respectively. Note that in successive plays these will be taken as normalized positive regrets, which is regrets divided by their sum. For instance in the next chance we chose scissors whcih has a
probablity of 2/3 while our opponent choses rock. For this game we have a regret of 1, 2, 0 for r, p and s. Thus our mixed strategy probablity distribution would be 1/6, 3/6 and 2/6.

Ideally we would like to minize oure expected regrets over time. However this would not be enough, since this strategy can be exploited. The opponent can exploit our regrets and by the time we learn the values the damage would have already been done. Regret matching can be used to minimize expected regret through self play algorithm as described below

* Init all cumulative regrets to 0
* For some number of iterations 
    * Compute strategy using regret matching. If there is a tie then use a random distribution. 
    * Select action based on the strategy
    * Play the game and compute regrets and add to cumulative regets. 
* Return the average atrategy profile.

Over time this process will converge as shown the code below, which is a worked out example for RPS game in python.



```python
ROCK, PAPER, SCISSORS = 0,1,2
NUM_ACTIONS = 3
regretSum = np.zeros(NUM_ACTIONS)
strategySum = np.zeros(NUM_ACTIONS)
oppStrategy = np.array([0.4, 0.3, 0.3])
def value(p1, p2):
    if p1 == p2:
        return 0
    elif p1 == ROCK and p2 == SCISSORS:
        return 1
    elif p1 == SCISSORS and p2 == PAPER:
        return 1
    elif p1 == PAPER and p2 == ROCK:
        return 1
    else:
        return -1


# accumulates in strategy sum 
def getStrategy():
    global regretSum, strategySum
    strategy = np.maximum(regretSum, 0)
    normalisingSum = np.sum(strategy)
    if normalisingSum > 0:
        strategy /= normalisingSum
    else:
        strategy = np.ones(NUM_ACTIONS)/NUM_ACTIONS
    strategySum += strategy
    return strategy

# use strategy sum 
def getAverageStrategy():
    global strategySum
    normalisingSum = np.sum(strategySum)
    if normalisingSum > 0:
        avgStrategy = strategySum/normalisingSum
    else:
        avgStrategy = np.ones(NUM_ACTIONS)/NUM_ACTIONS
    return avgStrategy

def getAction(strategy):
    rr = random.random()
    return np.searchsorted(np.cumsum(strategy), rr)

def train(iterations):
    global regretSum
    actionUtility = np.zeros(NUM_ACTIONS)
    for i in range(iterations):
        strategy = getStrategy()
        myAction = getAction(strategy)
        otherAction = getAction(oppStrategy)
        
        actionUtility[otherAction] = 0
        actionUtility[(otherAction + 1) % NUM_ACTIONS] = 1
        actionUtility[(otherAction - 1) % NUM_ACTIONS] = -1
        
        regretSum += actionUtility - actionUtility[myAction]

        
train(100000)

vvv = []
for j in range(100):
    vv = 0
    for i in range(1000):    
#       strategy = getStrategy()
#       this is probably what we will convege to 
#       strategy = np.array([0, 1, 0]) 
        strategy = getAverageStrategy()
        myAction = getAction(strategy)
        otherAction = getAction(oppStrategy)
        vv += value(myAction, otherAction)
    vvv.append(vv)
# plt.hist(vvv, bins=10)
plot(sorted(vvv)), np.mean(vvv)

```



**Implementation in ipython notebook:**
CFR [Github](https://github.com/k29/cfr)

**Credit:**
An Introduction to Counterfactual Regret Minimization by Todd W. Neller∗ Marc Lanctot

