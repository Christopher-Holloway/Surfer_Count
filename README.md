# Surfer_Count

This tutorial is a full guide to implementinig TensorFlow2.0's Objeect Detection API on Surfline's historical webcam data from [Oahu's Pipeline Overview Cam.](https://www.surfline.com/surf-report/pipeline-overview/584204214e65fad6a7709cdf) Operating System: macOS BigSur v 11.1

## 1. Installing and Setting up Anaconda 3.7 Virtual Environment
1. Follow the steps [**here**](https://docs.anaconda.com/anaconda/install/mac-os/), provided by Anaconda

      I prefer to use the command-line install but, recommend using whichever install method you are most comfortable with

2. Create and Activate the conda vitrual envrionment with python verison 3.8
* Open a *Terminal* window
* Type the following command:

    conda create -n tensorflow pip python=3.8
    
* The above will create a new virtual environment with name ***tensorflow***
* To activate the ***tensorflow*** virtual environment type the following command in the *Terminal* window: 

    conda activate tensorflow
    
* Throughout the rest of the tutorial, execution of any commands in a *Terminal* window should be done after the Anaconda virtual environment has been activated

## 2. Installing Tensorflow using Pip
* Type the following command:

    pip install --ignore-installed --upgrade tensorflow==2.2.0
    
## 3. Downloading the TensorFlow2.0 Model Garden
1. Create a new directory ***TensorFlow*** in your desired location
2. From terminal *cd* into the ***TensorFlow*** directory\
3. To download the models type the following command:
    
    git clone https://github.com/tensorflow/models
    
## 4. Installing and Compiling Protobuf

The Tensorflow Object Detection API uses Protobufs to configure model and training parameters. Before the framework can be used, the Protobuf libraries must be downloaded and compiled.

1. Download the latest protoc-*-*.zip (release)[https://github.com/google/protobuf/releases] (e.g. protoc-3.14.0-osx-x86_64.zip)
2. Extract the contents of the downloaded .zip in a directory <PATH_TO_PB> of your choice (e.g. ~/Downloads/)
3. Add <PATH_TO_PB> to your Path environment variable
4. Open a new *Terminal* window to activate the new environment variable
5. Move into the *TensorFlow/models/research* and run the following code:

    protoc object_detection/protos/*.proto --python_out=.
    
## 5. Install the Object Detection API
1. Run the following commands from within *Tensorflow/models/research*

    cp object_detection/packages/tf2/setup.py .
    python -m pip install .
    
## 6. Test installation
1. From the same directory run:

    python object_detection/builders/model_builder_tf2_test.py
    
    
    
    



