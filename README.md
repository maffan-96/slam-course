--- This part if for my reference, will be removed shortly
### To do (depending upon the availability of time, in the preceding order of importance)

Course Overview (with Learning objectives along with key concepts)
Software Setup instructions
Exercises and assignments
Community and Support (issue tracking and discussion)
Contribution (fork, push request, etc.)
Further reading/references

1. change all sh running docker file to same name "run.sh"
2. remove readme on how to run the code inside each repo a,b, and c
3. 

------
*****************

# Robotic perception SLAM course

Ville Lehtola, University of Twente, v.v.lehtola@utwente.nl   
2022-2024

This course repository is designed to introduce students to the fundamentals of SLAM (Simultaneous Localization and Mapping) through hands-on exercises. The course takes students from basic sensor integration to practical SLAM applications using modular exercises.

**Groupwork A**: Pedestrian dead reckoning by IMU integration
- Objective: With the help of this group work, students will learn about estimating a pedestrian’s movement by integrating data from an Inertial Measurement Unit (IMU). Students will learn to calculate position, velocity, and orientation by processing accelerometer and gyroscope data.
- Key Concepts:
  *  IMU calibration and data filtering **(integrated with ecercise A)
  *  Handling sensor noise and drift
  *  Dead reckoning with IMU
- Practical Application: This exercise simulates pedestrian localization in an indoor environment (without GPS), which is crucial for applications in wearable technology and mobile robots.

**Groupwork B**: 2D LIDAR SLAM with Cartographer
- Objective: Students will implement a basic SLAM system using Cartographer, an open-source SLAM framework, and a 2D Hokuyo LIDAR sensor. The exercise focuses on building 2D maps of indoor environments by processing LIDAR scans.
- Key Concepts:
  * LIDAR sensor data processing
  * Pose estimation and map building
  * SLAM with Cartographer
- Practical Application: This exercise allows building a 2D map of an indoor environment, which is useful in mobile robot navigation, autonomous vehicles, and drones.

**Groupwork C**: Coordinate Transformations and Multi-Sensor Data Registration (Advanced, could be done as a though exercise)
- Objective: This exercise covers relatively advanced SLAM concepts, specifically registering data from multiple LIDAR sensors and performing coordinate transformations. Students will explore multi-sensor fusion and transform sensor data into a unified coordinate system.
- Key Concepts:
  * Coordinate transformations (Euler angles, quaternions)
  * Multi-sensor fusion for improved map accuracy
  * Registering and aligning data from different sensors
- Practical Application: This exercise extends earlier SLAM implementation to multiple sensors, often common in autonomous driving, 3D mapping, and robot localization.

## Prerequisite
 The following course repositories are recommended to run inside docker. With the help of docker, a pre-configured environment could be run without the need to install individual software dependencies, simplifying the overall process. The exercises are tested with the following versions:
 * Ubuntu Linux 20.04 (expected to work with Ubuntu Linux 22.04)
 * Docker 26.0.00 (expected to work with other versions as well)
 * MESA (`conda install -c conda-forge libglu` (if using conda) OR `sudo apt-get install libgl1-mesa-glx libgl1-mesa-dri` (if using sudo env)
Installing ROS is not necessary.

## Setup Instructions
1. Clone the Repository
```
git clone https://github.com/your-username/slam-course.git
cd slam-course
```
2. Get sensor data  
Download the `rosbag` files (sensor data) from [here](https://surfdrive.surf.nl/files/index.php/s/cKCFQRLSTa5dfBF) to the slam-course folder. The downloaded bagfiles could be organized inside in a slam-course folder as shows below (it is completely fine to organize in a different way as well)
![image](https://github.com/user-attachments/assets/da64c136-003a-4d76-a56f-78abcb296405)


4. Build the Docker Image [note: use sudo or make a docker group]
Each group exercise has a separate docker file. For any given exercise, go to the respective folder, build the corresponding docker via the following commands,
    #### For exercise A
    ```
    cd RPCN_PART_A
    sudo docker build . -t rpcna  #rpcna is the docker image name for exercise A  
    ```
    #### For exercise B
    ```
    cd RPCN_PART_B
    sudo docker build . -t rpcnb  #rpcnb is the docker image name for exercise B  
    ```
    #### For exercise C
    ```
    cd RPCN_PART_C
    sudo docker build . -t rpcnc  #rpcnc is the docker image name for exercise C  
    ```
 
3. Start the Docker container
   
    While inside any exercise folder (e.g., RPCN_PART_A) 
     ```
     ./run_docker.sh (preferred)
     ```
     If you see the following (or similar) outcome, you are successfully inside a docker container
     ![image](https://github.com/user-attachments/assets/04e55f15-0ebe-473f-939c-340f6beb8a4b)
   
	a.	Check if you have your datasets(.bag) in the container
           ```
           cd backpack/bagfiles/
           ls
           ``` 
           ![image](https://github.com/user-attachments/assets/3d72a93c-0fec-4f36-b609-755480f0e8b3)
           
	b. To playback rosbag, you need to source ROS with the following command and start ROS
   
          source /opt/ros/noetic/setup.bash
          roscore


   **PS: You must source whenever you open a new terminal and connect it with docker**
           
	c. Now open two new terminals and connect them to the running container as follows:
   
       	   
           docker ps
           xhost +local:docker
           docker exec -it <id> bash
           
     ![image](https://github.com/user-attachments/assets/626903ff-b364-4c63-b633-4077028c9afa)

   Note: `<id>` refers to the container ID shown beside the container name (e.g., here 572aa1996226)

     #### For exercise A (Continued from earlier step `c`)
   
	d. While in a newly open container terminal (one of two newly open terminals) go to `backpack/bagfiles/` and play one of the rosbags

           cd backpack/bagfiles/
           rosbag play <xx.bag> --clock
   
	e. Go to another newly open container terminal, and look for rostopics as follows

            rostopic list #Shows all the published rostopics
            rostopic echo <topicname> #shows data of specific rostopic <topicname>
            rostopic echo /imu/data > ascii_file.txt #Extract the topic data to a text file
            rostopic echo /imu/acceleration | grep x > ascii_x_acc.txt #This saves only the x-axis acceleration data into the file

	f. Use the above commands to save IMU data; this will be used to interpolate a trajectory (See the assignment document for more description).

     #### For exercise B (Continued from step `c`)
   
	d. Go to the newly open container terminal, and run cartographer
            
            source "/opt/ros/${ROS_DISTRO}/setup.bash"
            cd /home/rpcn/catkin_ws
            source "/opt/cartographer_ros/setup.bash"
            source devel/setup.bash
            roslaunch rpcn_part_b rpcn_part_b.launch bag_filename:=/home/rpcn/catkin_ws/src/rpcn_part_b/bagfiles/{your_bag_file_name}

	f. You must understand the cartographer's configuration to complete your assignment specified in the my_robot.lua file.
        Use your preferred text editor to view and edit the config file, which is placed inside the following location
	       `/home/rpcn/catkin_ws/src/rpcn_part_b/configuration_files/my_robot.lua`
	g.  Edit the my_robot.lua file to enhance the performance of the cartographer SLAM algorithm.
   You must discuss and plot results (See the assignment document for more description).

     #### For exercise C (Continued from step `c`)
   
	d. We won't use the backpack data in this exercise. We will use NTU VIRAL DATASET for the given exercise.
	First, download the data `nya_01 (Collected inside the Nanyang Auditorium)` from the website, unzip it, and place it in the `slam-course/bagfiles` folder. 
            
		wget <bag zipfile URL>
   		unzip <downloaded bag zipfile (e.g., 68144> -d <bagfile folder>
   		unzip nya_01 #To get bag and related config files
   		source "/opt/ros/${ROS_DISTRO}/setup.bash"
   		cd nya_01/
   		rosbag play nya_01.bag
		rostopic list #Shows all the published rostopics

	![image](https://github.com/user-attachments/assets/7c6d2b89-312c-4dc9-ace6-a6a207ba2f9b)


	e. Download the customize [A-LOAM](https://github.com/brytsknguyen/A-LOAM) repository for NTU VIRAL Dataset 

		cd RPCN_PART_C/
		build docker
		
 
	f. When successfully cloned the A-LOAM, find the docker image
		cd ~/catkin_ws/src/A-LOAM/docker

	g. `cd` to the launch folder by running roscd aloam/launch`

	f. You must understand the cartographer's configuration to complete your assignment specified in the my_robot.lua file.
        Use your preferred text editor to view and edit the config file, which is placed inside the following location
	       `/home/rpcn/catkin_ws/src/rpcn_part_b/configuration_files/my_robot.lua`
   iv.  Edit the my_robot.lua file to enhance the performance of the cartographer SLAM algorithm. You must discuss and plot results (See the assignment document for more description).

5. Close/Terminate the course environment
```
exit
```
6. Cross-check if the environment is closed
```
docker ps
```


