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
* Unzip the file and inspect the contents to make sure you have two directories *scripts* and *workspace*

    
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
    
## Preprocessing the Data to Train Custom Object Detector
We are going to begin by downloading the data and annotating the individual video frames with bounding boxes around each surfer. The data annotations can be performed by a number of applications, here we choose to use labelImg, a python library easily installed with pip.

      pip install labelImg
      
### 1. Download the data
Next, we run into a road block for many users due to the fact that you need to be a Subscribed Premium Surfline member to access cam rewind downloads. Assuming everyone here is a memeber. There are methods you can use to download the cam rewind videos

A. Manually navigate the webpage to the download url 
B. Build a webscraper that automatically finds the url timestamp from the thumbnail filename and then use pythons urllib3 and requests to download the video

For simplicity and because this project at scale would implement a pipeline approach within Surfline's existing cloud infrastructure, we choose option (A). Download the file to <PATH>/Surfer_Count/scripts/preprocessing/ Next run the *grab_frame.py* script which loads videos and captures a frame every 10 seconds. 
      
      
### 2. Annotating the data
1. Once you have collected all the images to be used to test your model (ideally more than 100 per class), place them inside the folder training_demo/images

            mv *.jpg ./../../workspace/training_demo/images/.
     
2. Open a new terminal window and change into the directory we just placed the images in

            cd Surfer_Count/workspace/training_demo/images/
            
3. Open labelImg by typing *labelImg*, load your images and begin classifying be sure to use *surfer* as your classifying label. For more information on how to use LabelImg click [***here***](https://github.com/tzutalin/labelImg)

Here is an example of an annotated image!

![alt text](https://github.com/Christopher-Holloway/Surfer_Count/blob/main/tutorial/pipeline_annotated_image.png "Annotated image")

### 3. Partion the data
When training models it is best practice to split the data set into training (80%-90%) and testing (10%-20%) data. Here we use a 90% split because of our limited sample size. We can do this manually by moving both the image and adjoining *.xml* files into the */Surfer_Count/workspace/training_demo/images/train* and */Surfer_Count/workspace/training_demo/images/test* accrodingly. We could also use a *partion_dataset.py* located in */Surfer_Count/scripts/preprocessing* script to split and copy both the images and *.xml* files into the designated directories.

      python partition_dataset.py -i /<PATH>/Surfer_Count/workspace/training_demo/images -r -x
      
### 4. Create Label Maps
Before, we were annotating and classifying our bounding boxes with surfers, we now, need to create a label map file that is required by TensorFlow2.0. This file is used in both the training and detection phase. Becuase we only have one classifying label (surfer) we create a short and simple *label_map.pbtxt* file us vi editor. 
1. Change into the annotations folder to write the file

            cd ./../annotations
      
2. Open the vi editor and create the *label_map.pbtxt* file

            vi label_map.pbtxt
            
Press *i* to be able to insert text into the file. Copy and paste the following text below into the *label_map.pbtxt* file and save by pressing *esc* to exit text editing powers and then *:wq* to write and exit the file
   
            item {
                 id: 1
                 name: 'surfer'
            }
    
### 5. Create tfrecords from the *.xml* file 
TensorFlows object detection API requires that we convert our xml files into tfrecords. This is generally done when you are working with large datasets, using a binary file format for storage of your data can have a significant impact on the performance of your import pipeline and as a consequence on the training time of your model. 

In this tutorial we take advantage of a script provided by the TensorFlow Object Detection tutorial, called *generate_tfrecord.py* located in the */Surfer_Count/scripts/preprocessing/generate_tfrecord.py* to perform this task. To use this script we must install the pandas module with pip.

      pip install pandas

An example for just the training data (don't forget to do it for both the training and testing data) looks like:

      python generate_tfrecord.py -x C:/Users/chrisholloway/Surfer_Count/workspace/training_demo/images/train C:/Users/chrisholloway/Surfer_Count/workspace/training_demo/annotations/label_map.pbtxt -o C:/Users/chrisholloway/Surfer_Count/workspace/training_demo/annotations/train.record

Once the above is done, there should be a new file under the *training_demo/annotations* folder, named train.record.

## Training Custom Object Detector
Instead of training a completely new model, we will reuse one of the pre-trained models provided by TensorFlow.

### 1. Download a pre-trained model from the model zoo
Click on the name of the desired model in the table found in [TensorFlow 2 Detection Model Zoo](https://github.com/tensorflow/models/blob/master/research/object_detection/g3doc/tf2_detection_zoo.md). Clicking on the name of your model should initiate a download for a *.tar.gz* file. Here we will choose the SSD_ResNet50_V1_FPN_640x640 model, but you can choose any model from the zoo.

Now extract its contents into the *Surfer_Count/workspace/training_demo/pre-trained-models/* folder. Use the following command if your file was downloaded to your Users Download folder:

      tar - xvf ~/Downloads/ssd_resnet50_v1_fpn_640x640_coco17_tpu-8.tar.gz ~/Surfer_Count/workspace/training_demo/pre-trained-models/.
      
You should now have two folders names *checkpoint* and *saved_model*, as well as a *pipeline.config* file.

### 2. Configuring the Training Pipeline
Now that we have the model downloaded and extracted into its correct location in our workspace we will
1. Create a new folder inside the */Surfer_Count/workspace/training_demo/models/my_ssd_resnet50_v1_fpn* 
2. Copy the *pipeline.config* file inside the newly created directory 
3. Find lines 3,131,161,167,168,172,174,178,179,182,186 edit to your setttings based on the notes provided in the file and save

### 3. Training the Model
1. Copy the *model_main_tf2.py* file from the */Surfer_Count/models/object_detection/* folder and place it directily in the */Surfer_Count/workspace/training_demo/* folder
2. Change *cd* into the *training_demo* folder  
3. Run the following command

            python model_main_tf2.py --model_dir=models/my_ssd_resnet50_v1_fpn --pipeline_config_path=models/my_ssd_resnet50_v1_fpn/pipeline.config
4. Now the model should initialize and complete training for the designated number of epochs declared in the pipeline.config file

Training times can vary depending on if you are using a GPU or CPU as well as the batch size of the training data. In my case I ran this model locally for 20 epcohs with no optimization and it took roughly two days. (Not ideal!) But, for proof of concept with limited resources I was able to succesfully train this model and perform detection on validation images.

### 4. Exporting the Model
Once the model has completed training we will want to export an inference graph that we will later use to perform the object detection on validation images

1. Copy the *Surfer_Count/models/research/object_detection/exporter_main_v2.py* file and paste it straight into your *training_demo* folder
2. Run the following command to export the model into a newly created folder located at */Surfer_Count/workspace/training_demo/exported-models/my_model*

            python exporter_main_v2.py --input_type=image_tensor --pipeline_config_path=--pipeline_config_path=models/my_ssd_resnet50_v1_fpn/pipeline.config --trained_checkpoint_dir=/models/my_ssd_resnet50_v1_fpn/ --output_directory=exported-models/my_model

This model can then be used to perform inference












