---
author: simon.jonassen@gmail.com
comments: true
date: 2018-09-18 00:00:00+01:00
layout: post
title: Running Open AI Gym on Windows 10
tags: [today_i_learned, programming, machine learning, ai]
share: true
---


[Open AI Gym](https://gym.openai.com/) is a fun toolkit for developing and comparing reinforcement learning algorithms. It provides a variety of environments ranging from [classical control problems](https://gym.openai.com/envs/#classic_control), to [Atari games](https://gym.openai.com/envs/#atari) and [goal-based robot tasks](https://gym.openai.com/envs/#robotics). Currently it requires effort to install and AI Gym on Windows 10, in particular you need recursively install [Windows Subsystem for Linux](https://docs.microsoft.com/en-us/windows/wsl/install-win10), [Ubuntu](https://www.ubuntu.com), [Anaconda](https://www.anaconda.com), Open AI Gym and do a small dance to render simulation back to Windows. To make things a bit easier later we also want to use [Jupyter Notebook](http://jupyter.org/). Here are the step-by-step deails as of September 2018.

To install the Linux subsystem, you can simply run the following command as administrator in Power Shell:

```bash
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux
```

Restart the computer, install [Ubuntu 18.04](https://www.microsoft.com/en-us/p/ubuntu-1804-lts/9n9tngvndl3q) from the Microsoft Store, launch and run system update:

```bash
sudo apt-get update
sudo apt-get upgrade
```

Next install Anaconda 3 and its dependencies:

```bash
sudo apt-get install cmake zlib1g-dev xorg-dev libgtk2.0-0 python-matplotlib swig python-opengl xvfb
wget https://repo.continuum.io/archive/Anaconda3-5.2.0-Linux-x86_64.sh
chmod +x Anaconda3-5.2.0-Linux-x86_64.sh
./Anaconda3-5.2.0-Linux-x86_64.sh
```

Before proceeding ensure you have something like this in your .bashrc:

```bash
export PATH=/home/username/anaconda3/bin:$PATH
```

run `source .bashrc` or relaunch your terminal.

Now, we can create and install the Gym, as follows:

```bash
git clone https://github.com/openai/gym
cd gym
pip install -e .[all]
```

As we are also going to need matplotlib, run:
```bash
pip install matplotlib
```

Next we have to install jypyter notebooks and create a kernel pointing to your environment:

```bash
conda install jupyter nb_conda ipykernel
python -m ipykernel install --user --name gym
```

Now we can run the magic command:

```bash
xvfb-run -s "-screen 0 1400x900x24" ~/anaconda3/bin/jupyter-notebook --allow-root --no-browser
```

it will shortly prompt an address you can paste into your browser. 

Create a new notebook by clicking "New" and then "gym", and add something like this:

```python
import gym
env = gym.make('CartPole-v0')

from IPython import display
import matplotlib
import matplotlib.pyplot as plt
%matplotlib inline

env.reset()
img = plt.imshow(env.render(mode='rgb_array')) # only call this once
for _ in range(100):
    img.set_data(env.render(mode='rgb_array')) # just update the data
    display.display(plt.gcf())
    display.clear_output(wait=True)
    action = env.action_space.sample()
    env.step(action)
```

Hit enter and if you are lucky, you will get your first cartpole simulation running. Swap 'CartPole-v0' out with 'Breakout-v0' and we are gaming - AWESOME!
