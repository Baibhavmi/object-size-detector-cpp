# Assembly Line Measurements

| Details            |              |
|-----------------------|---------------|
| Target OS:            |  Ubuntu\* 16.04 LTS   |
| Programming Language: |  C++\* |
| Time to Complete:     |  45 min     |

![app image](./images/assembly-line-monitor.png)

## Introduction

This assembly line parts monitoring application is one of a series of reference implementations for Computer Vision (CV) using the OpenVINO™ toolkit. This application is designed for an assembly line camera mounted above the assembly line belt. The application monitors mechanical parts as they are moving down the assembly line and raises an alert if detects that the size of the part on the belt is not withing the specified area range.

This example is intended to demonstrate how to use CV to measure the approximate size of detected assembly line parts.

## Requirements

### Hardware
* 6th Generation Intel® Core™ processor with Intel® Iris® Pro graphics and Intel® HD Graphics

### Software
* [Ubuntu\* 16.04 LTS](http://releases.ubuntu.com/16.04/)
*Note*: You must be running kernel version 4.7+ to use this software. We recommend using a 4.14+ kernel to use this software. Run the following command to determine your kernel version:
```
uname -a
```
* OpenCL™ Runtime Package
* OpenVINO™ toolkit

## Setup

### Install OpenVINO™ Toolkit
Refer to https://software.intel.com/en-us/articles/OpenVINO-Install-Linux for more information about how to install and setup the OpenVINO™ toolkit.

You will need the OpenCL™ Runtime package if you plan to run inference on the GPU as shown by the
instructions below. It is not mandatory for CPU inference.

## How it works

The application uses a video source, such as a camera, to grab frames, and then uses `OpenCV` algorithms to process the captured data. It detects objects on the assembly line, such as bolts, and calculates the are they occupy. If this are is not withing the predefined range as specified via command line parameters it raises alert to notify the assembly line operator.

The data can then optionally be sent to a MQTT machine to machine messaging server, as part of an assembly line data analytics system.

![Code organization](./images/arch3.png)

The program creates three threads for concurrency:

- main thread that performs the video i/o
- worker thread that processes video frames using the deep neural networks
- worker thread that publishes any MQTT messages

## Setting the build environment

You must configure the environment to use the OpenVINO™ toolkit one time per session by running the following command:
```
    source /opt/intel/computer_vision_sdk/bin/setupvars.sh
```

## Building the code

Start by changing the current directory to wherever you have git cloned the application code. For example:
```
    cd assembly-line-measurements
```

If you do not yet have a `build` directory create one:
```
    mkdir build
```

Then change to that directory:
```
    cd build
```

Now run the following commands:
```
    cmake ..
    make
```

Once the commands are finished, you should have built the `monitor` application executable.

## Running the code

To see a list of the various options:
```
    ./monitor -help
```

To run the application with the needed models using the webcam:
```
    ./monitor -min=10000 -max=30000
```

The `-min` flag controls the minimum size of the are the part needs to occupy to be considered good

The `-max` flag controls the maximum size of the are the part needs to occupy to be considered good

## Sample videos

There are several videos available to use as sample videos to show the capabilities of this application. You can download them by running these commands from the `assembly-line-measurements` directory:
```
    mkdir resources
    cd resources
    wget https://github.com/intel-iot-devkit/sample-videos/raw/master/bolt-detection.mp4
    cd ..
```

To then execute the code using one of these sample videos, run the following commands from the `assembly-line-measurements` directory:
```
    cd build
    ./monitor -min=10000 -max=30000 -i=../resources/bolt-detection.mp4
```

### Machine to machine messaging with MQTT

If you wish to use a MQTT server to publish data, you should set the following environment variables before running the program:
```
    export MQTT_SERVER=localhost:1883
    export MQTT_CLIENT_ID=cvservice
```

Change the `MQTT_SERVER` to a value that matches the MQTT server you are connecting to.

You should change the `MQTT_CLIENT_ID` to a unique value for each monitoring station, so you can track the data for individual locations. For example:
```
    export MQTT_CLIENT_ID=assemblyline1337
```
