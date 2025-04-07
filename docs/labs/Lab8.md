# Fast Robots @ Cornell

[Return to main page](../index.md)

# Lab 8 Stunts!

## Objective
The purpose of this lab is to combine everything you've done up till now to do fast stunts. _This_ is the reason you labored all those long hours in the lab carefully soldering up and mounting your components!
Your grade will be based partially on your hardware/software design and partially on how fast your robot manages to complete the stunt (relative to everyone else in class). We will also have everyone vote on the coolest stunt and the best blooper video - the top picks will receive up to 2 bonus points.   

## Parts Required

* 1 x Fully assembled [robot](https://www.amazon.com/gp/product/B07VBFQP44/ref=ppx_yo_dt_b_asin_title_o00_s00?ie=UTF8&psc=1), with [Artemis](https://www.sparkfun.com/products/15443), [TOF sensors](https://www.pololu.com/product/3415), and an [IMU](https://www.digikey.com/en/products/detail/pimoroni-ltd/PIM448/10246391).

## Lab Procedure

### Controlled Stunts

Pick one of the following tasks. All of the related stunts must be performed on the tracks setup in the lab (or hallway outside of the lab). **Update: you can also perform this stunt at home due to Spring Break**. We have set up crash pads which should help prevent excessive damage when vehicles go rogue. We will need video evidence that your stunt works at least three times, and graphs showing the sensor data, KF output (if applicable), and motor values with time stamps. 

#### Task A: Flip

Your robot must start at the designated line (<4m from the wall), drive fast forward, and upon reaching the sticky matt with a center located 1ft from the wall, perform a flip, and drive back in the direction from which it came. You will need to add mass to the front of your car to get it to flip.
   - Your stunt will be considered successful if your robot manages to do a flip. The score will depend on how quickly (if at all) you make it back past the initial starting line. If your robot runs off at an angle without re-crossing the starting line this is a failed run. 
   - Disengage your PID position control, and give the robot a set, fast speed (to do your flip, you need the robot to go fast, therefore it should not be slowing down as it would if it was doing PID control on the position). If you want to go above and beyond and create a very robust solution, consider doing PID control on the speed of the robot instead of the position (this is optional).  
   - The TOF sensors are not all calibrated equally well; manually tuning the distance to the wall for which your robot initiates the flip is fine.
   - If you are using the Kalman Filter to estimate the distance to the wall quickly, do the following. When you have new sensor readings, run both the prediction and the update step. When you don't have new sensor readings, run only the prediction step (i.e. compute your estimated distance based only on the motor inputs and your dynamics model). Remember to adjust your discrete A and B matrices to the new sample rate and your input to the new PWM values.  

[![Lab 8, Task A](https://img.youtube.com/vi/cffupvOlyUM/1.jpg)](https://youtu.be/cffupvOlyUM "Lab 8, Task A")


#### Task B: Drift

Your robot must start at the designated line (<4m from the wall), drive fast forward, and when the robot is within 3ft (914mm = 3 floor tiles in the lab) from the wall, initiate a 180 degree turn. 
   - The TOF sensors are not all calibrated equally well; manually tuning the distance to the wall for which your robot initiates the turn is fine.
   - If you are using the Kalman Filter to estimate the distance to the wall quickly, do the following. When you have new sensor readings, run both the prediction and the update step. When you don't have new sensor readings, run only the prediction step (i.e. compute your estimated distance based only on the motor inputs and your dynamics model). Remember to adjust your discrete A and B matrices to the new sample rate and your input to the new PWM values.  
  
[![Lab 8, Task B](https://img.youtube.com/vi/d2JvpHIE_Pg/1.jpg)](https://youtu.be/d2JvpHIE_Pg "Lab 8, Task B")

### Extra credit for coolest stunt and best bloopers!

Everyone, including your teaching team, will be given the chance to vote for the coolest stunt video and the best blooper. We will send you a link to vote after Spring break. The highest scores will be given up to 2 extra credit points!

---

## Write-up

To demonstrate that you've successfully completed the lab, please upload a brief lab report (<800 words), with code snippets (not included in the word count), photos, graphs, and/or videos documenting that everything worked and what you did to make it happen. 
