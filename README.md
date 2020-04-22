# Installing and Using SAM-ResNet for Dummies

Simple, step-by-step instructions for setting up SAM-ResNet on Windows 10.

Author: Brenden Eum (Apr 2020)

## Introduction 

SAM-ResNet is a Saliency Attentive Model developed by Cornia, Baraldi, Serra, and Cucchiara (2018). Without diving into too many technicalities, SAM-ResNet utilizes a convolutional LSTM (long short term memory) neural network structure to predict saliency maps for images by incorporating neural attentive mechanisms. To learn more about the model, see the [paper](https://aimagelab.ing.unimore.it/imagelab/pubblicazioni/2018-tip.pdf).

As of April 21, 2020, SAM-ResNet is the top performing model on the MIT saliency benchmark test (cat2000). Rankings can be found [here](http://saliency.mit.edu/results_cat2000.html).

This document will provide simple step-by-step instructions to get you up and running with SAM-ResNet. Its purpose is not to get you acquainted with the theory behind the model. Instead, it will simply go over:
* How to install everything you need to run SAM-ResNet
* How to run SAM-ResNet using pre-trained models by Cornia, Baraldi, Serra, and Cucchiara (2018)
* How to train SAM-ResNet using your own images 
* How to use Python to adjust image characteristics (e.g. brightness, color saturation, etc.)

Everything will be done using Python and Command Prompt (PC) or terminal (Mac). The instructions here are for PC, and I will interchange between saying “command prompt” and “terminal”. This is not the only way to set up SAM-ResNet (and not the most efficient way), but if you are unfamiliar with setting up your GPU to use for deep learning, I find that this may be one of the simplest ways. Once you get acquainted with this process, you might find more advanced setups on your own that can lead to faster processing.

If you feel you do have a strong background in using terminals, you may be interested in using the instructions laid out in Cornia, Baraldi, Serra, and Cucchiara’s [README file](https://github.com/marcellacornia/sam). They are the original instructions followed by the author of this document, which assumes you are familiar with using terminals and are much more concise. The instructions found there are missing some steps needed for PC users.

## Installing

### 1 Setting up a toolkit folder

This first step is more of a stylistic choice, but I like to set aside a folder where I am going to install everything I need for deep learning. Preferably, you would place this in a drive with plenty of space (at least 30 GB of free space). For instance, I created a folder in my D: drive.

```
D:\toolkits.win\
```

Let’s call this your *toolkit folder*.

### 2 Download Anaconda3

Download [Anaconda 3](https://docs.anaconda.com/anaconda/install/windows/) into *toolkit folder*. Python is included with Anaconda.

Some options will come up when you install Anaconda:
* Don't add it to your PATH (we will do this manually later)
* Register Anaconda3 as your default Python 3.7
* Don't install PyCharm

Now you'll have a folder in *toolkit folder* called "anaconda" or something similar. For example:

```
D:\toolkits.win\anaconda3\
```

Let's call this your *anaconda folder*.

### 3 Add Python to your PATH

Your PATH is like a group of folders that your computer will search through for any commands you type into a terminal.

In the Start menu, type/search “environment variables” and click “Edit the system environment variables”. System Properties will open. Click “Environment Variables…”. Under “System variables”, click “Path” so that the row is highlighted, then click “Edit…”. Press “New” on the right and add in the address to *anaconda folder*.

We are adding *anaconda folder* because if you look inside the folder, you'll notice python.exe exists here. We want the terminal to use python.exe whenever you tell it to run something through python.

### 4 Check that pip install exists and is working

In the Start menu, type and click "Anaconda prompt (anaconda3)". This will open up a terminal in a base environment, probably to your local user directory. You don't really have to now what these mean now, and I'll explain what an environment is later.

Inside the terminal, type “pip” and press enter. If you see a list of commands and general options, then move on to the next step. If not, then type “conda update --all”. This will update your version of Python (as well as everything else), and pip will come along with that. 

### 5 Clone the SAM repository

Go to the [repository for SAM](https://github.com/marcellacornia/sam), find the green "Clone or download" button, and download the ZIP folder wherever you like. If you’re familiar with using GitHub, then feel free to clone the repository into *toolkits folder*.

Extract the contents of the ZIP folder into *toolkit folder*. It will create a new subfolder inside, so let’s call this your *sam folder*. 

```
D:\toolkits.win\sam\
```

While we’re here, add a subfolder called “weights” to your *sam folder*. Let's call this *weights folder*.

```
D:\toolkits.win\sam\weights\
```

Then, go back to the [repository for SAM](https://github.com/marcellacornia/sam), scroll down to the “Pretrained Models” section, and download “sam-resnet_salicon2017_weights.pkl”. Save this file into *weights folder* as “sam-resnet_salicon_weights.pkl”.

### 6 Check config.py in *sam folder*

Look for config.py in *sam folder*. Open it up with a text editor (e.g. Notepad or Sublime). Make sure version is set to 1. This ensures we are using SAM-ResNet.

```
version = 1
```

### 7 Install tdm-gcc

Go to [this link](https://jmeubank.github.io/tdm-gcc/) and install any tdm-gcc to *toolkit folder*. This is a GCC compiler for Windows.

### 8 Install Microsoft Visual Studio 2015

Go to [this link](https://visualstudio.microsoft.com/vs/older-downloads/) and download Microsoft Visual Studio 2015 to *toolkit folder*. I haven't tested any other versions, so for now I'll just say you need 2015 (though I'd imagine you can use any year). 

When installing:
* Choose installation location to be *toolkit folder*
* Select Custom Installation
* For select features, you don't need much. Just check:
  * Programming Languages\Visual C++\Common Tools for Visual C++ 2015
  * Windows and Web Development\Universal Windows App Development Tools\Tools (1.4.1) and Windows SDK...
  * Windows and Web Development\Universal Windows App Development Tools\Windows 10 SDK (10.0.10240)
Finally, just click install. This will take a bit of time.

When that is done, add the address below to your PATH (see step 3 above if you need an example of how to do this):

```
D:\toolkit.win\Microsoft Visual Studio 14.0\VC\bin\
```

We do this to have cl.exe in the PATH, so make sure that cl.exe is in the bin folder.

### 9 Create a deep learning environment

You can choose to do everything in the base environment, but I personally prefer to have an environment set aside for deep learning. This step is more of a stylistic option.

Open Anaconda prompt. Enter:

```
conda create --name deep python=2.7
```

We are creating an environment named "deep" (you can name it whatever you want). This environment is not going to utilize the default version of Python that was installed with Anaconda 3. Instead, we told it to initialize with an older version of Python (2.7). I believe Python 3.4 will also work, but for now, let's just stick to using 2.7.

If you've installed other modules and packages in the past, this environment will not carry those over. Essentially, it's like creating a new sandbox with a version of Python of your choosing. 

### 10 Install necessary modules

We need to install modules and packages for Python to use. Not only that, we need to make sure we are installing the correct versions for some of them. I'll just write out the code below. You might want to keep it in this order, since some of the packages will override previous versions otherwise. *ENTER ONE LINE AT A TIME*.

```
pip install libpython
```
```
pip install m2w64-toolchain
```
```
pip install h5py
```
```
pip install opencv-python
```
```
pip install keras==1.1.0
```
Note: This must be Keras version 1.1.0. For Theano, I believe you can use other versions like 0.10b.0.
```
pip uninstall theano
```
```
pip install theano==0.9.0
```
