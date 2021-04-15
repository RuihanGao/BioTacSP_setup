# Biotac SP ROS

This repository is for hosting the Biotac SP sensors sofware released under the lgpl3.0 license. This repo is originally duplicated and modified from [this repo](https://github.com/CNCR-NTU/biotac_sp_ros)

# Requirements

## Documentation
* [Biotac SP user manual](https://github.com/pedrombmachado/biotac_sp/blob/master/doc/BioTac_SP_Product_Manual.pdf)

## Hardware
* 3x Biotac SP sensors
* Biotac Board
* micro-USB power cable for connecting the Biotac board
* Cheetah SPI host
* USB-B cable.
* Host pc

## Software
* Ubuntu Linux 18.04 LTS
* ROS Melodic [installed](http://wiki.ros.org/melodic/Installation/Ubuntu) and [configured](http://wiki.ros.org/ROS/Tutorials/InstallingandConfiguringROSEnvironment)

# Installation procedure:
## Step 1: Connect the equipment 
Figure 1 shows the configuration setup

![](https://github.com/pedrombmachado/biotac_sp/blob/master/doc/Biotac.png)

Figure 1: Biotac setup
  
## Step 2: Update the OS and install base packets

```
$ sudo apt update & sudo apt upgrade -y & sudo apt dist-upgrade -y & sudo apt autoremove -y & sudo apt autoclean -y
$ sudo apt install build-essential git terminator
```

## Step 3: clone the repository
```
$ cd ~/catkin_ws/src
$ git clone https://github.com/RuihanGao/BioTacSP_setup
```

## Step 4: install the drivers
```
$ cd ~/Desktop/BioTacSP_ws/src/BioTacSP_setup
$ ./installCheetahDriver.sh
```

## Step 5: compile and install
```
$ cd ~/Desktop/BioTacSP_ws
$ catkin_make
$ source devel/setup.sh
```

## Step 6: run

Run terminator (in slayer conda environment, which is installed with empy, rospkg, and opencv-python)

`$ roslaunch biotac_sp_ros biotac.launch`


# Understanding the data
4x data frames are colected per second. Each frame is composed follows the recommended Dafault sampling sequence.

Figure 2 shows the Default Sampling	Sequence

![](https://github.com/CNCR-NTU/biotac_sp/blob/master/doc/data_sampling.png)

Figure 2: Default sampling sequence

Table 1: Bandwidth and Sampling rate for Default Sampling Sequence at 4.4kHz

![](https://github.com/CNCR-NTU/biotac_sp/blob/master/doc/data_sampling_bandwidth.png)

Figure 3 shows the Biotac SP electrodes distribution

![](https://github.com/CNCR-NTU/biotac_sp/blob/master/doc/Electrodes_distribution.png)

Figure 3 Biotac SP electrodes distribution

The output is a vector of 163 columns where:

[time, E1_s01, PAC_s01, E2_s01, PAC_s01,...,E24_s01, PAC_s01, PDC_s01, PAC_s01, PAC_s01, TAC_s01, PAC_s01, TDC_s01, PAC_s01,
E1_s02, PAC_s02, E2_s02, PAC_s02,...,E24_s02, PAC_s02, PDC_s02, PAC_s02, PAC_s02, TAC_s02, PAC_s02, TDC_s02, PAC_s02,
E1_s03, PAC_s03, E2_s03, PAC_s03,...,E24_s03, PAC_s03, PDC_s03, PAC_s03, PAC_s03, TAC_s03, PAC_s03, TDC_s03, PAC_s03]

where:

s01 - sensor 1

s02 - sensor 2

s03 - sensor 3

With exception of the time, each of the values is the average of the individual values of the 4x frames collected during 1 sec.

# Contacts
Computational Neurosciences and Cognitive Robotics Group at the Nottingham Trent University.

Pedro Machado <pedro.baptistamachado@ntu.ac.uk>

Martin McGinnity <martin.mcginnity@ntu.ac.uk>


# RH: steps for modified calibration & visualization
* Connect BioTacSP & Cheetah
* `rosrun biotac_sp_ros biotac_sp_ros_single` to convert SPI data to rostopics
* `rosrun biotac_sp_ros BioTacSP_calibrate.py` which waits for "calibrate" signal to compute DC offsets
* use `rostopic pub -1 /trigger_biotac_sp_calib std_msgs/String "calibrate"` to trigger caibration. The calibrated data are published in "/biotac_sp_pub_calib"
* In `slayer` conda environment, `rosrun biotac_sp_ros BioTacSP_viz.py`, which displays the data in a pop-up window
* `rosrun biotac_sp_ros BioTacSP_viz_realtime.py` to see a real-time plot for pdc data. Currently limited to 1000 timesteps and one plot. **TODO** multiple plots for pac, pdc, ele, temperature + moving time window

* Tips for setting up BioTac Labview visualizer on Windows, <br/>
    - Use NI Package Manager to check the versions installed on computer. Don't just delete the files in folders
    - Install LabView 2010 (32-bit), not 64-bit. The bit version depends on the computer that built the application (which is BioTac side), not the one that runs the application/program (which is your computer).
    - Currently available software on [SynTouch's website](https://syntouchinc.com/software/)  is only for BioTac (19 electrode), not BioTacSP (24 electrodes)
