---
author: simon.jonassen@gmail.com
comments: true
date: 2018-09-18 00:00:00+01:00
layout: post
title: Running Open AI Gym on Windows 10
tags: [today_i_learned, programming, machine learning, ai]
share: true
---


[Open AI Gym](https://gym.openai.com/) is a fun toolkit for developing and comparing reinforcement learning algorithms. It provides a variety of environments ranging from [classical control problems](https://gym.openai.com/envs/#classic_control) and [Atari games](https://gym.openai.com/envs/#atari) to [goal-based robot tasks](https://gym.openai.com/envs/#robotics). Currently it requires an amout of effort to install and AI Gym on Windows 10. In particular you need to recursively install [Windows Subsystem for Linux](https://docs.microsoft.com/en-us/windows/wsl/install-win10), [Ubuntu](https://www.ubuntu.com), [Anaconda](https://www.anaconda.com), Open AI Gym and do a robot dance to render simulation back to you. To make things a bit easier later you would also like to use [Jupyter Notebook](http://jupyter.org/). So here is a brief step-by-step description as of September 2018.

First we install the Linux subsystem by simply running the following command as Administrator in Power Shell:

```bash
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux
```

After restarting the computer, install [Ubuntu 18.04](https://www.microsoft.com/en-us/p/ubuntu-1804-lts/9n9tngvndl3q) from the Windows Store, launch and run system update:

```bash
sudo apt-get update
sudo apt-get upgrade
```

Next we install Anaconda 3 and its dependencies:

```bash
sudo apt-get install cmake zlib1g-dev xorg-dev libgtk2.0-0 python-matplotlib swig python-opengl xvfb
wget https://repo.continuum.io/archive/Anaconda3-5.2.0-Linux-x86_64.sh
chmod +x Anaconda3-5.2.0-Linux-x86_64.sh
./Anaconda3-5.2.0-Linux-x86_64.sh
```

Then we ensure that .bashrc file has been modified as necessary. If not, adding `. /home/username/anaconda3/etc/profile.d/conda.sh` at the very end should do the kick. 

After this, we relaunch the terminal and create a new conda environment called `gym`:

```bash
conda create --name gym python=3.5
conda activate gym
```

and install Gym and its dependencies:

```bash
git clone https://github.com/openai/gym
cd gym
pip install -e .[all]
```

As we are also going to need matplotlib, we install it by running:
```bash
pip install matplotlib
```

Next we install jypyter notebooks and create a kernel pointing to the gym environment created in the previous step:

```bash
conda install jupyter nb_conda ipykernel
python -m ipykernel install --user --name gym
```

Now we run the magic command which will create a virtual X server environment and launch the notebook server:

```bash
xvfb-run -s "-screen 0 1400x900x24" ~/anaconda3/bin/jupyter-notebook --no-browser
```

no-browser tells that we don't want to open a new browser in the terminal, but prompt the address. The address can now be pasted in the browser in your Windows 10, outside of the Ubuntu env. 

Next we create a new notebook by choosing "New" and then "gym" (thus launching a new notebook with the kernel we created in the steps above), and writing something like this:

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

This tells to create a new cart pole experiment and perform 100 iterations of doing a random action and rendering the environment to the notebook.

If you are lucky, hitting enter will display an animation of a cart pole failing to balance. Congratulations, this is your first simulation! Replace 'CartPole-v0' with 'Breakout-v0' and rerun - we are gaming! AWESOME!

![Open AI Gym in Action](http://s-j.github.io/images/cartpole.png)
