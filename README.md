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
   
     a. Check if you have your datasets(.bag) in the container
           ```
           cd backpack/bagfiles/
           ls
           ``` 
           ![image](https://github.com/user-attachments/assets/3d72a93c-0fec-4f36-b609-755480f0e8b3)
           
     b. To playback rosbag, you need to source ROS with the following command and start ROS
   

          source /opt/ros/noetic/setup.bash
          roscore


   PS: You must source whenever you open a new terminal and connect it with docker
           
     c. Now open two new terminals and connect them to the running container as follows:
   
       	   ```
           docker ps
           docker exec -it <id> bash
           ```
          ![image](https://github.com/user-attachments/assets/626903ff-b364-4c63-b633-4077028c9afa)

           Note: <id> refers to the container ID shown beside the container name (e.g., here 572aa1996226)

         

Note: The above will run a docker file for exercise A, but it could be followed similarly for exercise B or C.
4. Access the container environment from another terminal
```
docker ps
```
You will receive a response similar to the one attached in the picture. Copy the value shown below the container ID (e.g., here 8425931a5549) corresponding to the name of the container you want to open in another terminal/shell. Now, paste the copied value in the format as shown in the following command in-place at `<id>`
![image](https://github.com/user-attachments/assets/5c67feeb-4f1a-45a3-a726-032f82fee4ff)
```
sudo docker exec -it <id> bash
```
5. Close/Terminate the course environment
```
exit
```
6. Cross-check if environment is closed
```
docker ps
```


