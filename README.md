
# Aero-X Software team
  
## Overview : 

We, the Software team, work on the simulation and the software system that allows our quadcopter to be controlled remotely and operable autonomously. 
Our mission is to provide a robust, reliable and modular software elements to implement different functions of our drone that enables basic flight, fail safes, payload delivery and autonomy. Using concepts of control systems we tailor each of the algorithms to achieve stable and time efficient flight. Using concepts of computer vision, we implement autonomy and aim to achieve higher efficiency using concepts of data science.

## Intro about SAE Aerothon : 
AeroTHON 2022 competiton has a primary focus on developing skills to design and build an UAV to prepare them to be industry-ready in the emerging market. <br>

This contest provides a real-life engineering exercise to undergraduate and graduate
engineering students. <br>

In this contest, students will perform trade studies and make decisions to arrive at a
design solution that will meet the mission requirements and conform to the defined
configuration limitation and build a flightworthy UAV. <br>

The UAV must meet the specified mission requirements and a flight demonstration in the flying event.<br>

The competition is divided into two parts :
1. Phase 1 : Design Report & Oral Presentation.
2. Phase 2 : Flying Competition.

### Phase 1:
- This phase invites innovative designs from the participant teams
- The innovative designs will be evaluated by industry and academic experts
- Top 20 teams shortlisted for phase - 2

### Phase 2:
- Students build physical prototype of their design
- Flight Test
- Prizes awarded to seven teams

### The previous year objective : 
The objective for this year’s contest is to design, build and fly a multirotor UAV that
can deliver cargo to a specified location. The teams shall design a UAV that can carry
a specified payload and deliver it to a target area by manual as well as autonomous
operations.<br>

For simplicity we are going to assume that the objective is going to remain the same and proceed, however as the alorithms as well as the part that picks up the payload is modular, we can repurpose the drone to perform the objective if it is changed.


## Hardware and Firmware : 

We analyzed some of top drone controllers of the market by gathering information for factors like:
1. Affordability
2. Open Source Firmware
3. FPV Racing friendly
4. Autonomous functionality
5. Linux or microcontroller based environment
6. Typical frame size
7. Popularity (Higher popularity means more online resources and help)
8. Processor




                                                                 COMPARING BOARDS 

|  Board        |   Weight (in gms)  | Open Source Firmware                |  FPV  |  Autonomous Functionality  |  Typical Frame Size  |  Popularity  |  Processor  |
| ---------     |  ----------------  |  -------------------                |  ---  |  ------------------------  |  ------------------  |  ----------  |  ---------  |
|  Pixhawk      |      39            | ArduPilot, PX4                      |  Yes  |         Yes                |   82 x 16 x 50 mm    |   High       | 32          |   
|     CC3D      |       9            |  CleanFlight,BetaFlight,ArduPilot   |  Yes  |         Yes                |   40 x 40 x 18       |   High       | 32          |
|  Navio2       |       23           |  ArduPilot,PX4                      |  No   |         Yes                |   55 x 65 mm         |   Medium     |  32         |
|               |                    |                                     |       |                            |                      |              |             |


                                                          
                                                          
                                                          
   COMPARING ArduPilot and PX4
                                                                                                                 

  | Specifications  |  ArduPilot    |  PX4  |
  |:---             |:---           |:---   |
  |License          |  GPL          |  BSD  |
  | Sources         |  Open-Source  | Open-Source|
  | Form-Factor     | High          |High   |
  




**The main difference between Ardupilot and PX4 arises in License Factor. GPL ( General Public License) is a copy left open-source license. This means that all derivatives of Ardupilot must also be released in GPL. Where PX4 is BSD (Berkeley Source Distribution) for which we don't have to openly release the source.**





***Based on the above data, we've decided to use Pixhawk along with PX4***

## Software Configuration : 

### We are going to build a drone which can switch between autonomous and RC mode
The switching of the drone modes is facilitated by the Pixhawk flight controller.
Our drone will have some features it can execute in the RC mode and some in the autonomous and some of the features are going to be common for both the modes like **Altitude hold**, **Fail - safe** and **Geofencing**, In the next section of the document, we dive deeper into the different control systems and algorithms of the following features.
<br>
All of the features we are going to implement is either an inbuilt feature in the pixhawk's px4 firmware or one's that we are going to Develop using the Dronekit library.
<br>

### On board abstraction : 
Both the methods abstract the individual speed control of the motors using the motor mixing algorithm and have the PID controllers which will take care of all the differential equation involved and gives output to the **mma** based on the command and the state of the drone.

Using the feedback from the onboard sensors, we design a closed loop control system for different autonomous operations.

<br><br>
A typical mma : <br>
<img src="mma.png" alt="mma">


# Features of drone
R C Controlled : 

During the fliying competetion, the remote controlled drone must :
- Fly to height of 30m.
- Locate the target at that altitude.
- Fly towards that maintaining an alititude of 30m.
- After approaching the target, fly down to an altitude of 20m.
- Drop the target as close to the bull's eye as possible.
- Return to base.

There is evaluation for maintaining mission altitude and payload drop distance, so we find it best to automate the two processes.
<br>Hence we arrive on the following features our drone will be able to perform.
<br>Our geographical fence, failsafe and the payload delivery is going to be similar for both autonomous and rc operation.
1. Throttle
2. Pitch 
3. Roll
4. Yaw
5. Altitude hold
6. Hover
7. Geographical fence

<br>

### 1. Throttle

<br>

Throttle is just a upward (or downward) movment of quadcopter which can be achived by increasing (or decreasing) all the propeller speeds by the same amount. It leads to a vertical force with respect to body-fixed frame which raises or lowers the quad-rotor.

<br>

### 2. Pitch

<br>

The second dimension an aircraft can move in is called “pitch.” The pitch means the drone tilts upwards or downwards based on its orientation and the location of its nose. A downwards tilt will move the aircraft (drone in this case) in a forwards motion, while an upwards tilt will move it backwards.

<br>

### 3. Yaw

<br>

“yaw” refers to the direction the front of your drone (or even a plane or car) is facing when rotating either clockwise or counterclockwise (or left and right if you prefer) on its vertical axis.
<br>

### 4. Roll

<br>
"Roll" Refers to the rotation of the drone along the axis that connects head to tail of the drone.
 <br>

### 5. Hover
<br>

two of a drone's four rotors move clockwise, while the other two move counterclockwise, ensuring that the sideways momentum of the drone remains balanced.

<br>

### 6. Geographical fence

<br>

This is the use of GPS to create a virtual boundary that triggers a predetermined response when the drone flies into — or out of — a particular area. If the drone flies towards a “fenced” or restricted area, it will stop mid-flight. If you try to take off from a restricted area, the drone will not start up at all. Geofences can be placed to keep drones out of certain fields, from flying over a particular building, and prevent them from entering into “no fly zones.”

<br>
<br>

## How it all works : 

## Control systems of the features : 
All the features of the drone can be accomplished by a applying closed loop control system.
<the main control system of the RC mode : >
In RC mode, the basic degrees of freedom can be accomplished where the feedback and the correction of the motor speed values can be provided by the end user (pilot) where he controls the drone by flying it in FPV mode. <br><br>

Control system of RC :<br><br>
<a href="https://ibb.co/Hz3t6tX"><img src="https://i.ibb.co/zRDXpXN/Rccontrolsystem.png" alt="Rccontrolsystem" border="0"></a><br> taken from https://www.youtube.com/watch?v=GK1t8YIvGM8.
<br>
We can observe here that the input is from the user, so we have no need of PID controllers.

However due to the restraints provided by the Aerothon Competition, we find it best to have an autonomous mode **Altitude hold**, when it is switched ON, the drone will autonomously stay in the same altitude while executing the actions the pilot provides in all the other axis. <br>
<img src = "thrust-altitudehold.png" alt = "Thrust alg for altitude hold">

<br>
And Implementing the geofencing : <br>
<img src = "geofence.png" alt = "geofence">

We would also like to have another switchable autonomous **hover** mode for the drone which will help us performing the most important objective of the flying competition which is **Delivery of the payload** as close to the Bull's eye as possible.<br><br>


For this we might need to calculate the possible trajectory of the projectile and the as there are points alloted for dropping as close to the bull's eye as possible, we find it best to automate the Dropping process too rather than eyeball it. <br> <br>


As we approach the Bull's eye in the RC mode, we will hover over its approximated position and switch to the autonmous **Delivery Process**, after the payload is delivered or dropped at the height of 20m over the Bull's eye, we switch back to RC mode and return to base following the objective of the PHASE II flying competition.

## Autonomy of the drone : 
One of our flying competitions include the drone perform the following operations autonomously : 

# Summary : 
We have finalized on the hardware and the software configuration of our drone based on the advantages it provides for our objecive, we are designing algorithms for some of the features not provided in the px4 kit and learning the algorithms which are provided in it so that we can tailor them to our need.
