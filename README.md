[//]: # (Image References)

# Udacity Deep Reinforcement Learning Nano Degree: Project 2 - Continuous Control

## Introduction

I trained a double-jointed arm to touch a target location for as many time steps as possible.

<img src="images/my-trained-agent.gif" width="60%" align="top-left" alt="" title="my trained agent" />

*above: my trained agents*

The task is episodic. The agent gets a reward of +0.1 for each step in which its arm touches the goal location. 

The state space has 33 dimensions corresponding to position, rotation, velocity, and angular velocities of the arm. Given this information, the agent has to learn how much torque to apply to its two joints. Every entry in the action vector should be a number between -1 and 1.

The Unity environment contains 20 identical agents which can be trained in parallel.

The goal is to train the agents to get an average score over all 20 agents of +30 over 100 consecutive episodes.

## Getting Started

### Set up your python environment

1. Create and activate a new environment with Python 3.6.

	- __Linux__ or __Mac__:
	```bash
	conda create --name drl-control python=3.6
	source activate drl-control
	```
	- __Windows__:
	```bash
	conda create --name drl-control python=3.6
	activate drl-control
	```

2. Clone this repository and install the dependencies.
```bash
git clone https://github.com/AnKra/udacity-deep-reinforcement-learning-continuous-control-project.git
cd udacity-deep-reinforcement-learning-continuous-control-project/python
pip install .
cd ..
```

3. Create an [IPython kernel](http://ipython.readthedocs.io/en/stable/install/kernel_install.html) for the `drl-control` environment.  
```bash
python -m ipykernel install --user --name drl-control --display-name "drl-control"
```

### Set up the reacher environment

1. Choose the environment matching your operating system from one of the links below.
    - Linux: [click here](https://s3-us-west-1.amazonaws.com/udacity-drlnd/P2/Reacher/Reacher_Linux.zip)
    - Mac OSX: [click here](https://s3-us-west-1.amazonaws.com/udacity-drlnd/P2/Reacher/Reacher.app.zip)
    - Windows (32-bit): [click here](https://s3-us-west-1.amazonaws.com/udacity-drlnd/P2/Reacher/Reacher_Windows_x86.zip)
    - Windows (64-bit): [click here](https://s3-us-west-1.amazonaws.com/udacity-drlnd/P2/Reacher/Reacher_Windows_x86_64.zip)

    (_For Windows users_) Check out [this link](https://support.microsoft.com/en-us/help/827218/how-to-determine-whether-a-computer-is-running-a-32-bit-version-or-64) if you need help with determining if your computer is running a 32-bit version or 64-bit version of the Windows operating system.

    (_For AWS_) If you'd like to train the agent on AWS (and have not [enabled a virtual screen](https://github.com/Unity-Technologies/ml-agents/blob/master/docs/Training-on-Amazon-Web-Service.md)), then please use [this link](https://s3-us-west-1.amazonaws.com/udacity-drlnd/P2/Reacher/one_agent/Reacher_Linux_NoVis.zip) to obtain the environment.

2. Place the zip file your working copy of this repository and unzip it.

### Run the notebook

1. Open a notebook
```bash
jupyter notebook
```

2. Before running code in a notebook, change the kernel to match the `drl-control` environment by using the drop-down `Kernel` menu.

3. Follow the instructions in `Continuous_Control.ipynb`
