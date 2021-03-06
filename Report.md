# Reinforcement Learning

## A Taxonomy of RL Algorithms
Reinforcement learning basics and value-based RL algorithms can be found here: https://github.com/AnKra/udacity-deep-reinforcement-learning-navigation-project/blob/master/Report.md

## Policy-Based RL Methods
### Why Policy-Based Methods?
#### Simplicity
Value-based methods maintains an estimate of the optimal action-value function. The optimal policy is derived from this estimate. In contrast, policy-based methods learn the optimal policy directly.

#### Stochastic Policies
In contrast to value-based methods, policy-based methods can learn stochastic policies. In this case, the network outputs are treated as the probability that the agent the corresponding action.

#### Continuous Action-Spaces
Value-based methods require discrete action spaces. In contrast, policy-based methods can be used to solve environments with continuous action spaces.

### Policy Gradient Methods
Policy gradient methods estimate the weights of an optimal policy through gradient ascent.

The so-called REINFORCE algorithm proceeds as follows:
1. Initialize a random policy.
2. Use the policy to collect trajectories (i.e. a sequence of state, actions and rewards).
3. Calculate the total reward of the trajectory. Estimate the gradient of the expected reward.
4. Update the weights of the policy using gradient ascent with a learning rate.
5. Go to step 2

### Proximal Policy Optimization (PPO)
PPO improves the following aspects of the REINFORCE algorithm:

#### Noise
A single collected trajectory may not be representative of the policy. As a consequence, in the gradient estimate in the REINFORCE algorithm is very noisy.

PPO estimates the policy gradient by averaging across a batch of trajectories. This approach reduces noise and increases efficiency.

Another advantage of using batches: Using batches allows for rewards normalization which avoids too large policy updates.

#### Credit Assignment
Some actions in a trajectory may be good while others are bad. The REINFORCE algorithm reinforces actions depending on only the final total output.

PPO assigns credit to an action based on only the future reward. The past reward does not contribute to the policy gradient.

#### Efficiency
A trajectory must be representative for the current policy. Therefore the REINFORCE algorithm uses every trajectory only once.

Importance Sampling allows to reuse trajectories. It is based on the idea that every trajectory has a certain probability to be sampled under the current policy. Under a new policy this probability may be different.

To reused a trajectory its probability needs to be multiplied by a an extra re-weighting factor. This factor corresponds to how more or less representative the trajectory is under the new policy.

#### Clipped Surrogate Function
The idea of PPO is to find a surrogate function which approximates the current policy gradient. In other words, the surrogate function approximates the reward around the current policy. Gradient ascent can be performed by maximizing the surrogate function. (Be aware that this objective is opposed to "classic" deep learning where you aim to find network weights which minimize the error!)

The surrogate function approximates the reward around the current policy well - but further away it might differ significantly from the actual reward.

<figure class="image">
  <img src="images/policy-reward-cliff.png" width="80%" alt="" title="The Policy/Reward Cliff" />
  <figcaption>The Policy/Reward Cliff. Source: Udacity Deep Reinforcement Learning Nanodegree</figcaption>
</figure>

To prevent from falling off a cliff and getting stuck in a flat bottom the top part of the surrogate function is flattened.

<figure class="image">
  <img src="images/clipped-surrogate.png" width="80%" alt="" title="Clipped Surrogate Function" />
  <figcaption>Clipped Surrogate Function. Source: Udacity Deep Reinforcement Learning Nanodegree</figcaption>
</figure>

PPO proceeds as follows:
1. Initialize a random policy.
2. Collect trajectories.
3. Compute the gradient of the clipped surrogate function.
4. Update the policy gradient using gradient ascent.
5. Go to step 3. Loop for a few times.
6. Update the policy and go to step 2.

## Actor-Critic Methods
Actor-Critic methods aim to combine the good aspects of value-based and policy-based methods.

<figure class="image">
  <img src="images/actor-critic-methods.png" width="80%" alt="" title="Actor-Critic Methods" />
  <figcaption>Actor-Critic Methods. Source: Udacity Deep Reinforcement Learning Nanodegree</figcaption>
</figure>

The idea is to use value-based techniques to reduce the variance of policy-based methods. The actor decides which action to take. It uses function approximation to learn the optimal policy. The critic evaluates the action. It uses function approximation to learn the estimated value function (in simple implementations the action-value function / Q-value, but typically the state-value function). The critics output is the advantage function. The advantage function tells how good an action is at the current state compared to the other actions. It can be used as a baseline to train the actor. The baseline is subtracted from the policy gradient which reduces variance. One possibility to approximate the advantage function is by TD error.

<figure class="image">
  <img src="images/actor-critic-architecture.png" width="30%" alt="" title="The actor-critic architecture. " />
  <figcaption>The actor-critic architecture. Source: http://incompleteideas.net/book/first/ebook/node66.html</figcaption>
</figure>

### Advantage Actor Critic (A2C) v.s. Asynchronous Advantage Actor Critic (A3C)
A3C aims to reduce bias by the following improvements:
1. Instead of using a TD estimate, A2C uses an estimate referred to as n-step bootstrapping to train the critic. TD can be seen as a one-step bootstrapping, Monte Carlo as an infinite bootstrapping. N-step bootstrapping is a generalization of the TD and Monte-Carlo estimates.
2. A3C implements parallel training. Multiple workers update a global value function. Parallel training decorrelates experiences and eliminates the need for a replay buffer.

In A3C the agents update the global network asynchronously. As a consequence, the weights used by the agent may be different. A2C is the asynchronous implementation of A3C. It waits for all agents and updates the network at once. As a consequence, all agents use the same weights. It is simpler to implement.

### Deep Deterministic Policy Gradient (DDPG)
DDPG combines an actor-critic architecture with insights from DQN. However, while DQN can only be used for environments with discrete action spaces, DDPG can only be used in environments with continuous action spaces.

The actor specifies a deterministic policy (instead of a stochastic policy). It maps states to a specific action. The critic learns to evaluate the actor. It is learned using the Bellman equation as in DQN.

In DDPG the critic is used to approximate the maximizer over the Q-values of the next state, instead of a learned baseline. This makes it similar to a DQN.

Two characteristics of DDPG are:
1. Replay buffer: DDPG does not support parallelization, thus it requires a replay buffer.
2. Soft updates: Instead of copying the weights of the regular actor and critic networks into the corresponding target networks, the regular network weights are slowly blended with the target network weights.

## Summary
<figure class="image">
  <img src="images/policy-gradient-summary.png" width="60%" alt="" title="Summary of Policy Gradient Algorithms" />
  <figcaption>Summary of Policy Gradient Algorithms. Source: https://hadovanhasselt.files.wordpress.com/2016/01/pg1.pdf</figcaption>
</figure>

# Project 2 - Continuous Control

## Learning Algorithm
The learning algorithm is based on the [DDPG Paper](https://arxiv.org/pdf/1509.02971.pdf). I started with 1 agent (using version 1 of the environment). After having gained some experience on how to tune the parameters, I switched to 20 agents (using version 2 of the environment).

In every step the Agent class takes 20 states, actions, rewards, next states and dones as input (one per agent) and outputs 20 actions. It represents 20 agents.

Each agent inserts its experience into a replay buffer. The buffer is shared by all agents. The agents sample batches of 256 experiences from the buffer. This relatively large batch size results in a smoother learning curve.

The actor and critic networks are updated 10 times in a row after every 20 timesteps to make the agent stable. When training the critic network I use gradient clipping. The update is performed by soft update with tau set to 0.001.

During training, noise is added to every action. I reduced the noise by setting Ornstein-Uhlenbeck noise parameters sigma and theta. I decreased sigma to 0.05 to reduce the influence of the random number on the result. Larger values prevent the agent from learning. I increased theta slightly to 0.25 to reduce the decrease the reversion speed of the noise function.

A well-chosen learning rate as great influence on the learning progress of the agents. After some tuning I decided to set the learning rate for both actor and critic to 0.0006.

## Model Architecture
The primary actor network and the target actor network have identical architectures. The same is true for the primary critic network and the target critic network.

### Actor
```
Actor(
  (fc1): Linear(in_features=33, out_features=128, bias=True)
  (fc2): Linear(in_features=128, out_features=256, bias=True)
  (fc3): Linear(in_features=256, out_features=4, bias=True)
  (bn1): BatchNorm1d(128, eps=1e-05, momentum=0.1, affine=True, track_running_stats=True)
  (bn2): BatchNorm1d(256, eps=1e-05, momentum=0.1, affine=True, track_running_stats=True)
)
```
```
def forward(self, state):
    x = self.bn1(F.relu(self.fc1(state)))
    x = self.bn2(F.relu(self.fc2(x)))
    x = F.tanh(self.fc3(x))
    return x
```

### Critic
```
Critic(
  (fcs1): Linear(in_features=33, out_features=512, bias=True)
  (fc2): Linear(in_features=516, out_features=256, bias=True)
  (fc3): Linear(in_features=256, out_features=1, bias=True)
)
```
```
def forward(self, state, action):
    xs = F.relu(self.fcs1(state))
    x = torch.cat((xs, action), dim=1)
    x = F.relu(self.fc2(x))
    x = self.fc3(x)
    return x
```

## Result
The agent achieves an average score of 13 in less than 300 episodes.
The agents achieve an average score of 30 (over 100 consecutive episodes, and over all agents) in less than 70 episodes.

<img src="images/result.png" width="80%" alt="" title="MC Control" />

## Ideas for Improvement
* find better hyperparameters in an automated way
* study [Benchmarking Deep Reinforcement Learning for Continuous Control](https://arxiv.org/pdf/1604.06778.pdf) and implement Trust Region Policy Optimization (TRPO) and Truncated Natural Policy Gradient (TNPG)
* implement Distributed Distributional Deterministic Policy Gradients (D4PG) and compare with DDPG
