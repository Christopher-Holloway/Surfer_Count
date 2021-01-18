# Surfer_Count

This tutorial is a full guide to implementinig TensorFlow2.0's Objeect Detection API on Surfline's historical webcam data from [Oahu's Pipeline Overview Cam.](https://www.surfline.com/surf-report/pipeline-overview/584204214e65fad6a7709cdf) Operating System: macOS BigSur v 11.1

## Download, Install, and Setup of for Tensorflow Object Detection 

### 1. Installing and Setting up Anaconda 3.7 Virtual Environment
1. Follow the steps [**here**](https://docs.anaconda.com/anaconda/install/mac-os/), provided by Anaconda

      I prefer to use the command-line install but, recommend using whichever install method you are most comfortable with

2. Create and Activate the conda vitrual envrionment with python verison 3.8
* Open a *Terminal* window
* Type the following command:

      conda create -n tensorflow pip python=3.8
    
* The above will create a new virtual environment with name ***Surfer_Count***
* To activate the ***Surfer_Count*** virtual environment type the following command in the *Terminal* window: 

      conda activate tensorflow
    
* Throughout the rest of the tutorial, execution of any commands in a *Terminal* window should be done after the Anaconda virtual environment has been activated

### 2. Installing Tensorflow using pip
* Type the following command:

       pip install --ignore-installed --upgrade tensorflow==2.2.0
    
### 3. Download and unzip the workspace
1. Create a new directory ***Surfer_Count*** in your desired location
* Download the Surfer_Count.zip in your local home directory (e.g. /Users/chrisholloway/)
* Unzip the file and inspect the contents to make sure you have two directories *scripts* and *workspace

    
### 4. Downloading the TensorFlow2.0 Model Garden
1. From terminal *cd* into the ***Surfer_Count*** directory
2. To download the models type the following command:
    
            git clone https://github.com/tensorflow/models
    
### 5. Installing and Compiling Protobuf

The Tensorflow Object Detection API uses Protobufs to configure model and training parameters. Before the framework can be used, the Protobuf libraries must be downloaded and compiled.

1. Download the latest protoc-*-*.zip (release)[https://github.com/google/protobuf/releases] (e.g. protoc-3.14.0-osx-x86_64.zip)
2. Extract the contents of the downloaded .zip in a directory <PATH_TO_PB> of your choice (e.g. ~/Downloads/)
3. Add <PATH_TO_PB> to your Path environment variable by appending the following lines to ~/.bashrc

            export PATH=<PATH_TO_PB>${PATH:+:${PATH}}

4. Open a new *Terminal* window to activate the new environment variable
5. Move into the *Surfer_Count/models/research* and run the following code:

            protoc object_detection/protos/*.proto --python_out=.
    
### 6. Install the Object Detection API
1. Run the following commands from within *Surfer_Count/models/research*

            cp object_detection/packages/tf2/setup.py .
            python -m pip install .
    
### 7. Test installation
1. From the same directory run:

            python object_detection/builders/model_builder_tf2_test.py
    
## Training Custom Object Detector
We are going to begin by downloading the data and annotating the individual video frames with bounding boxes around each surfer. The data annotations can be performed by a number of applications, here we choose to use labelImg, a python library easily installed with pip.

      pip install labelImg
      
### 1. Download the data
Next, we run into a road block for many users due to the fact that you need to be a Subscribed Premium Surfline member to access cam rewind downloads. Assuming everyone here is a memeber. There are two methods you can use to download the cam rewind videos
1. Manually navigate the webpage to the download url 
2. Build a webscraper that automatically finds the url timestamp from the thumbnail filename and then use pythons urllib3 and requests to download the video

Note: I am currently working on the webscraper but also understand the legal issues so I will not be publishing that work here.

1. 

`




