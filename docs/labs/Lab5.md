# Fast Robots @ Cornell

[Return to main page](../index.md)

# Lab 5: Linear PID control and Linear interpolation

## Objective
The purpose of this lab is to get experience with PID control. The lab is fairly open ended, you can pick whatever controller works best for your system. 4000-level students can choose between P, PI, PID, PD; 5000-level students can choose between PI and PID controllers. Your hand-in will be judged upon your demonstrated understanding of PID control and practical implementation constraints, and the quality of your solution. 

This lab is part of a series of labs (5-8) on PID control, sensor fusion, and stunts. 
This week you will do position control.

## Parts Required
* 1 x Fully assembled [robot](https://www.amazon.com/gp/product/B07VBFQP44/ref=ppx_yo_dt_b_asin_title_o00_s00?ie=UTF8&psc=1), with [Artemis](https://www.sparkfun.com/products/15443), [TOF sensors](https://www.pololu.com/product/3415), and an [IMU](https://www.digikey.com/en/products/detail/pimoroni-ltd/PIM448/10246391).

## Prelab / BLE 

No matter which task you take on, it will be essential that you first setup a good system for _debugging_. 

**Please attempt to implement this before your lab session. Feel free to discuss the best strategy with your team mate.**

A good technique will be to: 
1. Have the robot controller start on an input from your computer sent over Bluetooth
2. Execute PID control over a fixed amount of time (e.g. 5s) while storing debugging data in arrays.
   - Remember to have a hard stop implemented directly on your Artemis, so that your robot will stop even if the Bluetooth connection fails.
4. Upon completion of the behavior, send the debugging data back to the computer over Bluetooth. 

Debugging data may for example include sensor data with time stamps similar to what you implemented in lab 2-3, output from the individual branches of your PID controller, and/or the input that you are sending to your motors. Remember, however, the storage cannot exceed the internal RAM of 384kB.  If you plan to do a lot of tweaking of your gains, you can also consider writing a Bluetooth command that lets you update the gains without having to reprogram the Artemis. 


## Lab Procedure

### Position Control

For this task, you will have your robot drive as fast as possible (given the quality of your controller) towards a wall, then stop when it is exactly 1ft (=304mm=1 floor tile in the lab) away from the wall using feedback from the time of flight sensor. Your solution should be robust to changing conditions, such as the starting distance from the wall (2-4m). If you attempt to do this at home, you could also show that your solution is robust to changing floor surface, e.g. linoleum or carpet. The catch is that any overshoot or processing delay may lead to crashing into the wall.

Beyond the considerations mentioned above, think about the following:
   - Given the range of motor input values and the output from your TOF sensor, discuss what a reasonable range of the proportional controller gain will be. 
   - Consider the range and sampling time you choose for your TOF sensor; it may be worth lowering the accuracy for faster updates. Note that the medium range is only available if you are using the ([ToF Pololu library](https://github.com/pololu/vl53l1x-arduino.git)). 
   - Also note that the sensor has a programmable integration time. If this is set too high, you will see large jumps in your data as the robot drives and you can no longer assume that the measurements are independent. You can lower the integration time (trading off accuracy for speed) using: `proximitySensor.setProxIntegrationTime(4); //A value of 1 to 8 is valid`. Again this function is only available in the [Tof Pololu library](https://github.com/pololu/vl53l1x-arduino.git).

Below you can see an example of a simple PI controller acting on the TOF signal.

**Tips and tricks:**
   - _LOG DATA:_ If you don't want to repeat work during Lab 7, be sure to log all data (time stamped sensor values and motor outputs), as well as setup variables from at least one successful run. Even if you are doing orientation control, be sure to log ToF data as you speed towards the wall as well. 
   - _Lectures:_ Brush up on your PID control skills by checking out [Lectures 7 and 8](https://cei-lab.github.io/FastRobots-2023/lectures/).
   - _PID library:_ There exists an [Arduino PID library](https://playground.arduino.cc/Code/PIDLibrary/). You are welcome to use this library if you prefer, but we will only offer limited TA support if you run into issues. Implementing a basic PID controller from scratch is easy (<10 lines of code), and will give you more freedom in dealing with noise, wind-up, and system non-linearities. 
   - _Start simple:_ E.g. with a proportional controller running at low speeds and a generous setpoint, then you can work your way up to faster speeds, more advanced control, and more difficult setpoints if you have time. 
   - _Documentation:_ Please clearly document the maximum linear or angular speed you are able to achieve (you can use your sensors to compute this). To demonstrate reliability, please upload videos of at least three repeated (and hopefully successfull) experiments.  
   - _Frequency:_ Fast loop times means everything to a good controller. Be sure to include a discussion of sensor sampling rate and how this affects the timing of your control loop. Avoid using blocking statements when you can (e.g. `delay()` or `while(sensor not ready) ){wait}` ). Also, remember that any serial.print/BLE sending that occurs during execution may slow down your loop time considerably. 
   - _Deadband:_ From Lab 5, you should have found the deadband of your motor (the region below which the power to the motors does not overcome the static friction in your system). Consider writing a scaling function that converts the output from your PID controller to an output for which the motors can actually react. 
   - _Wind up:_ If you include an integrator, consider whether you need to worry about integrator wind-up. 
   - _Derivative LPF:_ If you include a derivative, consider whether it is necessary to include a low pass filter in the derivative branch. 
   - _Derivative kick:_ Consider whether the derivative kick can cause any issues, given the task you choose. Here is a great overview on how to eliminate derivative kick: http://brettbeauregard.com/blog/2011/04/improving-the-beginner’s-pid-derivative-kick/
   - _Anything goes:_ The goal is a working system. When you have a reasonable control setup working, you should feel free to add any "hacks" that will improve your robot performance in a reliable way. If you don't have time to implement them, discussing what you imagine would help can still get you grading points. 
   - _Motor drivers:_ Recall [Lecture 6 on Actuators](https://cei-lab.github.io/ECE4960-2022/lectures/FastRobots-6-Actuators.pdf) and that the motor drivers have both coasting and active breaking modes. These might come in handy.

<img src="../Figs/Lab6_TaskA_PIcontrol_example.png" width="400">

Corresponding videos are here: 

[![Solution 1](https://img.youtube.com/vi/nhYkWK4wSiE/1.jpg)](https://youtu.be/nhYkWK4wSiE "Solution 1")      [![Solution 2](https://img.youtube.com/vi/mBxqnfi0VOE/1.jpg)](https://youtu.be/mBxqnfi0VOE "Solution 2").

### Extrapolation

In Lab 7, you will learn how the Kalman Filter works, you could implement this on your robot and use it to speed up sampling of the estimated distance-to-the-wall. However, getting the Kalman Filter to work in practice takes time. A simple but less accurate alternative is a data extrapolator. 

Write a function to extrapolate new TOF values based on recent sensor values, such that you can drive your robot quickly towards the wall with high accuracy. Be sure to demonstrate that your solution works by uploading videos and by plotting corresponding raw and estimated data in the same graph.

#### Instructions:

1. Determine the frequency at which the ToF sensor is returning new data. 
- This is likely the rate at which your PID control loop is running as well. We want to decouple these two rates. 
2. Change your loop to calulate the PID control every loop, even if there is no new data from the ToF sensor.
- Check if new data from the ToF sensor is ready. If it is, update the variable that PID controller is using to estimate the motor speed. 
- If a new datapoint isn't ready, recalculate the PID control using using the last saved datapoint. 
- The net effect this should have on your system should be the same. You PID control should now be running faster than your ToF sensor is generating new data.
3. How fast is the PID control loop running? Compare this rate to ToF sensor rate.
4. Rather than use an old datapoint to calulate the PID control, extrapolate an estimate for the car's distance to the wall using the last two datareadings from the ToF sensor. 
- Calcuate the slope from the last two datapoint, and extrapolate the current distance based on the ammount of time that has passed since the last reading and the slope. 
- This is a simple linear extrapolation algorithm. Everytime you get a new ToF reading, use it along with the previous reading to estimate the current distance to the wall untill a new reading is recieved. If you have any questions about this, please ask one of the TAs for clarification. 

## Tasks for 5000-level students
   
Implement wind-up protection for your integrator. Argue for why this is necessary (you may for example demonstrate how your controller works reasonably independent of floor surface). Demonstrate your system with and without wind-up protection. 

## Write-up

Word Limit: < 800 words
                 
**Webpage Sections**

This is not a strict requirement, but may be helpful in understanding what should be included in your webpage. It also helps with the flow of your report to show your understanding to the lab graders. *This lab is more open ended in terms of the steps taken to reach the end goal, so just make sure to document your process you take to complete your task, including testing and debugging steps!*

1. Prelab
   * Clearly describe how you handle sending and receiving data over Bluetooth
   * Consider adding code snippets as necessary to showcase how you implemented this on Arduino and Python

2. Lab Tasks
   * P/I/D discussion (Kp/Ki/Kd values chosen, why you chose a combination of controllers, etc.)
   * Range/Sampling time discussion
   * Graphs, code, videos, images, discussion of reaching task goal 
   * Graph data should include Tof vs time and Motor input vs time (and whatever helps with debugging)
   * (5000) Wind-up implementation and discussion
   
Add code (consider using [GitHub Gists](https://gist.github.com)) where you think is relevant (DO NOT paste your entire code).
