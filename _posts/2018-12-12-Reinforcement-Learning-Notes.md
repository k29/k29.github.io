In the book, "Feynman's lost lecture: The motion of planets around the sun", there is a passage in the book, which goes as follows:

> Feynman was a truly great teacher. He prided himself on being able to devise ways to explain even the most profound ideas to beginning students. Once, I said to him, "Dick, explain to me, so that I can understand it, why spin one-half particles obey Fermi-Dirac statistics." Sizing up his audience perfectly, Feynman said, "I'll prepare a freshman lecture on it." But he came back a few days later to say, "I couldn't do it. I couldn't reduce it to the freshman level. That means we don't really understand it."

In the same fashion these notes are basically an attempt to understand reinforcement learning by reducing it to a freshman level. I am referring to the amazing book, "Reinforcement Learning: An Introduction, by Barton and Sutton" so far. No plagiarism is intended, all sources I have referred to and shall refer to are given credit here. 
The notes are iterative in nature, like PG metions in one of his essays, "Write a bad version 1 as fast as you can; rewrite it over and over; cut out everything unnecessary."

This is a 30 day mini challenge, and shall continue to add a post every day, and hopefully, shall get to a decently good implementation by Day 30. 

All codes shall be linked to my github repo (coming soon)

# Day 1

Reinforcement Learning (RL) is learning what to do, i.e. map situations to actions and keep doing this again and again, till you can maximise the reward. The agent or the learner does not know what to do in a given situation, but instead learns by doing. It must discover which action leads to the most reward by trying them. 
The learning agent, must be able to 
1. Sense the state of the environment. 
2. Must be able to take actions that affect the state.
3. Must have a goal relating to the state of the environment.

RL is often considered as a different paradigm of ML, in comparison with supervised and unsupervised learning.

One of the challenges, which is especially reflected while performing RL, and has been studied extensively by mathematicians till now (yet to be solved), is the tradeoff between exploration and exploitation. To be more effective, i.e. to find more reward the agent mist prefer actions which it has performed in the past and found to be effective in producing reward, but to discover such actions it has to go and do things it hasn't done before. So there is a contstant fight between, this was good, let's do it again vs maybe I will find something better in the future. 

### Four components of RL 

**Policy**

Policy is the way a learning agent behaves at any given time. This is mapping, from 'state of the environment' to 'action to be taken'. This can be a simple function, a lookup table or even an extensive computation.

**Reward Signal** 

This is the goal of the problem. On each step, the environment send the agent a reward, and the agent needs to maximise the reward over the long run. This is also the main reason why policy can be changed. If the reward after executing some action from the policy is low, then some other action will be selected in the future for the same environmental conditions. This is basically like the experiences of pleasure and pain, if by touching a hot stove, you get a low reward, you are less likely to follow the policy which asks you to touch the hot stove when you are near it. These reward signals are a function of the state and the action, reward =  f(state, action).

**Value function**

Value function specifies what is good in the long run. The value of a state is the total amount of reward an agent can expect to accumulate over the future, starting from that state. The value function is telling how rewarding the current state is by taking into account the following states and rewards available in those cases. Most of the action choices are made based on the value and not on the basis of rewards as rewards are short term. Although, it is much harder to estimate the value of a state.

**Model of the enviroment** 

The model mimics the environment thus allowing inferences to be made. This is done for planning so that future possible siuations can be considered even before they are actually experienced. Based on whether model is employed or not, a mehtod can be model-based learning or model-free learning. 

