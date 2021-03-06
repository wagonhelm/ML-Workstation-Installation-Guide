ML Workstation Installation Guide
*********************************

In this tutorial I will be going through how to install various software for machine learning development using Anaconda Python or native Python 3.5 and Ubuntu 16.04. This tutorial is mainly based on doing reinforcement learning and includes how to install alot of OpenAI’s software. This will also include building the latest version of TensorFlow from sources. 

In order to use TensorFlow with GPU support you must have a NVidia graphic card with a minimum `compute capability <https://developer.nvidia.com/cuda-gpus>`_ of 3.0 .

.. contents:: **STEPS**
    :depth: 2


Install Required Packages
=========================

Open a terminal by pressing `Ctrl + Alt + T`
Copy all lines per codeblock and paste lines into terminal using `Shift + Ctrl + V`

.. code:: shell

    sudo apt-get install git python-dev python3-dev build-essential swig libcurl3-dev libcupti-dev golang libjpeg-turbo8-dev make tmux htop cmake zlib1g-dev libjpeg-dev xvfb libav-tools xorg-dev python-opengl libboost-all-dev libsdl2-dev apt-transport-https ca-certificates curl software-properties-common coreutils mercurial libav-tools libsdl-image1.2-dev libsdl-mixer1.2-dev libsdl-ttf2.0-dev libsmpeg-dev libsdl1.2-dev libportmidi-dev libswscale-dev libavformat-dev libavcodec-dev libtiff5-dev libx11-6 libx11-dev fluid-soundfont-gm timgm6mb-soundfont xfonts-base xfonts-100dpi xfonts-75dpi xfonts-cyrillic fontconfig fonts-freefont-ttf wget unzip git nasm tar libbz2-dev libgtk2.0-dev libfluidsynth-dev libgme-dev libopenal-dev timidity libwildmidi-dev python-pip python3-pip python-wheel python3-wheel python-virtualenv 
        
TensorFlow's build system Bazel also requires Java to be installed.

.. code:: shell

    sudo add-apt-repository ppa:webupd8team/java && \
    sudo apt-get update && \
    sudo apt-get install oracle-java8-installer


Install Anaconda or Python VirtualEnv
=====================================

**If using Anaconda**

You can download the latest version of Anaconda but I prefer to setup in a conda environment using Python 3.5.

.. code:: shell

    wget https://repo.continuum.io/archive/Anaconda3-5.1.0-Linux-x86_64.sh

and install:

.. code:: shell

    bash Anaconda3-4.2.0-Linux-x86_64.sh

When Anaconda asks if you would wish prepend the Anaconda install location to your bash press enter for no.  I prefer to create my own bash alias for activitating my python environments (I run alot!) which I will show how to do later.  But if Anaconda is the only Python you plan on using type yes or edit your bashrc file with the following:

.. code:: shell

    gedit ~/.bashrc

and copy and paste this in the bottom:

.. code:: shell

    export PATH="/home/$USER/anaconda3/bin:$PATH"

Now the best thing to do is to create a new isolated environment to manage package versions so that you don’t have to reinstall Anaconda if you flub your python packages.  If like me and you have not added anaconda to your path you will have to add it temporarily using the following.

.. code:: shell

    export PATH="/home/$USER/anaconda3/bin:$PATH"
        
We can now create a Anaconda environment, adding the anaconda argument at the end will install all the Anaconda base packages.  I prefer to use python 3.5 as I feel it is the most stable with all other libraries.
        
.. code:: shell

    conda create --name ml python=3.5 anaconda

and activate the environment:

.. code:: shell

    source activate ml

We will need to build additional pylons, I mean packages.  We will install pip into our conda environment but the general rule is to always try installing a package with conda first, if that is not possible, then use pip.  

.. code:: shell

    (ml) conda install pip six libgcc swig pyopengl opencv 
        
Now if your like me and you didn't add Anaconda to your path you can create an alias by adding the following to your '~/.bash_aliases' file.

Open up your `~/.bash_aliases` file:

.. code:: shell
 
    gedit ~/.bash_aliases
        
and add the following:

.. code:: bash

    alias ml='export PATH="/home/$USER/anaconda3/bin:$PATH" && echo Activating Conda Environment && source activate ml'

Now with an alias created you can activate your environment simply by calling from the terminal with:

.. code:: shell

    ml

**If Using Native Python 3.5 w/ Virtual Env**

Create a virtualenv and activate it

.. code:: shell

    virtualenv -p python3 ~/ml && \
    source ~/ml/bin/activate
        
Update your pip

.. code:: shell
        
    pip install --upgrade pip && \
    pip install numpy opencv-python six wheel
        
Now if your lazy like me you can create an alias to activate the environment:

.. code:: bash
    gedit ~/.bash_aliases
    # And add in:
    alias ml='source ~/ml/bin/activate'


Install nVidia Toolkit 9.0 & CudNN 7.0
======================================

**Skip this section if you do not have a compatible NVidia GPU**

You must also have the 375 (or later) NVidia drivers installed, this can easily be done from Ubuntu’s built in additional drivers (press windows key and search additional drivers) after you update your driver packages by:

.. code:: shell

    sudo add-apt-repository ppa:graphics-drivers/ppa && \
    sudo apt update 

Once installed using additional drivers restart your computer. If you experience any troubles booting linux or logging in: try disabling fast & safe boot in your bios and modifying your grub boot options to enable nomodeset.

To install the nVidia Toolkit download base installation .run file from `nVidia <https://developer.nvidia.com/cuda-toolkit>`_ website.  I prefer to use the .run file over the deb file because it will not override your current nVidia drivers with version 384.

.. code:: shell

    cd ~/Downloads && \
    wget https://developer.nvidia.com/compute/cuda/9.0/Prod/local_installers/cuda_9.0.176_384.81_linux-run && \
    sudo sh cuda_9.0.176_384.81_linux-run --override --silent --toolkit  

This will install cuda into: /usr/local/cuda

To install CudNN download `cuDNN v7.05 Library for Linux <https://developer.nvidia.com/cudnn>`_ for Cuda 9.0 from nVidia website  and extract into /usr/local/cuda via:  

.. code:: shell

    tar -xzvf cudnn-9.0-linux-x64-v7.tgz && \
    sudo cp cuda/include/cudnn.h /usr/local/cuda/include && \
    sudo cp cuda/lib64/libcudnn* /usr/local/cuda/lib64 && \
    sudo chmod a+r /usr/local/cuda/include/cudnn.h /usr/local/cuda/lib64/libcudnn* && \
    sudo ln -s /usr/local/cuda/lib64/* /usr/lib

Then update your `bash file <http://askubuntu.com/questions/540683/what-is-a-bashrc-file-and-what-does-it-do>`_:

.. code:: shell

    echo $'export CUDA_HOME=/usr/local/cuda\nexport LD_LIBRARY_PATH="$LD_LIBRARY_PATH:/usr/local/cuda/lib64:/usr/local/cuda/extras/CUPTI/lib64"\nexport PATH=$PATH:/usr/local/cuda/bin' >> ~/.bashrc

To update your terminal type this command to reload your .bashrc file, or easier yet just close your terminal and open a new one.

.. code:: shell

    source ~/.bashrc

Install Tensorflow From Sources
===============================
        
**Install Bazel**

You will need Google's build tool Bazel to install TensorFlow from sources.  Instructions also on `Bazel <http://www.bazel.io/docs/install.html>`_ website

.. code::

    echo "deb [arch=amd64] http://storage.googleapis.com/bazel-apt stable jdk1.8" | sudo tee /etc/apt/sources.list.d/bazel.list && \
    curl https://bazel.build/bazel-release.pub.gpg | sudo apt-key add - && \
    sudo apt-get update && \
    sudo apt-get install bazel && \
    sudo apt-get upgrade bazel

**Clone Tensorflow**

.. code:: shell

    cd ~ && git clone https://github.com/tensorflow/tensorflow && cd ~/tensorflow

**Configure Tensorflow Installation**

Make sure your python environment is activated if you created one and run the TensorFlow configure script.

.. code:: shell

    (ml) ~/tensorflow/./configure

The configure script is pretty good at finding the proper to use settings.  Use defaults by pressing enter for all except the option for CUDA support if you are using a GPU. It doesn't hurt to install cloud support as well.  Here is how my install looked.

.. code:: shell

        Please input the desired Python library path to use.  Default is [/home/justin/envs/ml/lib/python3.5/site-packages]

    Do you wish to build TensorFlow with jemalloc as malloc support? [Y/n]: 
    jemalloc as malloc support will be enabled for TensorFlow.

    Do you wish to build TensorFlow with Google Cloud Platform support? [Y/n]: 
    Google Cloud Platform support will be enabled for TensorFlow.

    Do you wish to build TensorFlow with Hadoop File System support? [Y/n]: 
    Hadoop File System support will be enabled for TensorFlow.

    Do you wish to build TensorFlow with Amazon S3 File System support? [Y/n]: 
    Amazon S3 File System support will be enabled for TensorFlow.

    Do you wish to build TensorFlow with XLA JIT support? [y/N]: 
    No XLA JIT support will be enabled for TensorFlow.

    Do you wish to build TensorFlow with GDR support? [y/N]: 
    No GDR support will be enabled for TensorFlow.

    Do you wish to build TensorFlow with VERBS support? [y/N]: 
    No VERBS support will be enabled for TensorFlow.

    Do you wish to build TensorFlow with OpenCL SYCL support? [y/N]: 
    No OpenCL SYCL support will be enabled for TensorFlow.

    Do you wish to build TensorFlow with CUDA support? [y/N]: Y
    CUDA support will be enabled for TensorFlow.

    Please specify the CUDA SDK version you want to use, e.g. 7.0. [Leave empty to default to CUDA 9.0]: 


    Please specify the location where CUDA 9.0 toolkit is installed. Refer to README.md for more details. [Default is /usr/local            /cuda]: 


    Please specify the cuDNN version you want to use. [Leave empty to default to cuDNN 7.0]: 7.0.5


    Please specify the location where cuDNN 7.0.5 library is installed. Refer to README.md for more details. [Default is /usr/local/cuda]:


    Please specify a list of comma-separated Cuda compute capabilities you want to build with.
    You can find the compute capability of your device at: https://developer.nvidia.com/cuda-gpus.
    Please note that each additional compute capability significantly increases your build time and binary size. [Default is:   3.0,3.0]


    Do you want to use clang as CUDA compiler? [y/N]: 
    nvcc will be used as CUDA compiler.

    Please specify which gcc should be used by nvcc as the host compiler. [Default is /usr/bin/gcc]: 

    Do you wish to build TensorFlow with MPI support? [y/N]: 
    No MPI support will be enabled for TensorFlow.

    Please specify optimization flags to use during compilation when bazel option "--config=opt" is specified [Default is -march=native]: 


    Add "--config=mkl" to your bazel command to build with MKL support.
    Please note that MKL on MacOS or windows is still not supported.
    If you would like to use a local MKL instead of downloading, please set the environment variable "TF_MKL_ROOT" every time before build.

    Would you like to interactively configure ./WORKSPACE for Android builds? [y/N]: 
    Not configuring the WORKSPACE for Android builds.

    Configuration finished


You can find the compute capability of your NVidia card `here <https://developer.nvidia.com/cuda-gpus>`_ 

If all was done correctly you should see:

.. code:: shell

        Configuration finished

**Build Tensorflow**

Warning Resource Intensive! I recommend having at least 8GB of computer memory.

If you want to build TensorFlow with GPU support enter:

.. code:: shell

    bazel build --config=opt --config=cuda //tensorflow/tools/pip_package:build_pip_package

For **CPU Only** enter:

.. code:: shell

    bazel build --config=opt //tensorflow/tools/pip_package:build_pip_package

**Build & Install Pip Package**

This will build the pip package required for installing TensorFlow in your /tmp/ folder

.. code:: shell

    bazel-bin/tensorflow/tools/pip_package/build_pip_package /tmp/tensorflow_pkg/

**Install Tensorflow using Pip**

.. code:: shell

    pip install /tmp/tensorflow_pkg/tensorflow
        # with no spaces after tensorflow hit tab before hitting enter to fill in blanks

** Test Your Installation**

Close all your terminals and open a new terminal to test. Also make sure your terminal is not in the ‘tensorflow’ directory.

.. code:: python

        (ml) python
        import tensorflow as tf
        sess = tf.InteractiveSession()
        sess.close()
        


Install Docker
==============

Docker is an open-source project that automates the deployment of applications inside software containers.  It is also used by Open AI’s Universe.

Start by:

.. code:: shell

    sudo apt-get install \
            apt-transport-https \
            ca-certificates \
            curl \
            software-properties-common

For **Ubuntu 14.04**:

.. code:: shell

    sudo apt-get install \
            linux-image-extra-$(uname -r) \
            linux-image-extra-virtual

Followed by:

.. code:: shell

    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

Followed with:

.. code:: shell

    sudo add-apt-repository \
        "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
        $(lsb_release -cs) \
        stable"

And to finish:

.. code:: shell

        $ sudo apt-get update
        $ sudo apt-get install docker-ce

And test installation by:

.. code:: shell

        $ sudo service docker start
        $ sudo docker run hello-world

You should see a message Hello from Docker! informing you that your installation appears correct. 

To make it so you don’t have to use sudo to use docker you can:

.. code:: shell

        sudo groupadd docker
        sudo usermod -aG docker $USER
        sudo reboot
        
        # IF LATER YOU GET DOCKER CONNECTION ISSUES TRY:
        sudo groupadd docker
        sudo gpasswd -a ${USER} docker
        sudo service docker restart   
        sudo reboot

Install OpenAI's Gym & Universe
===============================

If you plan on doing any Reinforcement Learning you are definitely going to want OpenAI’s gym.

.. code:: shell

        cd ~ && git clone https://github.com/openai/gym.git
        cd gym
        pip install -e .

Followed by Universe:

.. code:: shell

        pip install universe

We can also clone Open AI’s starter agent which will train an agent using the A3C Algorithim.

.. code:: shell

        cd ~ && git clone https://github.com/openai/universe-starter-agent.git
        cd ~/universe-starter-agent
        python train.py --num-workers 4 --env-id PongDeterministic-v0 --log-dir /tmp/vncpong --visualise
