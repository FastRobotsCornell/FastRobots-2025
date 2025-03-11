# Fast Robots @ Cornell

[Return to main page](../index.md)

# Lab 7: Kalman Filter

## Objective

The objective of Lab 7 is to implement a Kalman Filter, which will help you execute the behavior you did in [Lab 5](Lab5.md) faster. The goal now is to use the Kalman Filter to supplement your slowly sampled ToF values, such that you can speed towards the wall as fast as possible, then either stop 1ft from the wall or turn within 2ft.

## Parts Required

* 1 x Fully assembled [robot](https://www.amazon.com/gp/product/B07VBFQP44/ref=ppx_yo_dt_b_asin_title_o00_s00?ie=UTF8&psc=1), with [Artemis](https://www.sparkfun.com/products/15443), [TOF sensors](https://www.pololu.com/product/3415), and an [IMU](https://www.digikey.com/en/products/detail/pimoroni-ltd/PIM448/10246391).

We will have a few setups in and just outside of the labs with crash-pillows mounted along the wall to limit damages. If you practice at home, be sure to do the same!


## Lab Procedure

### 1. Estimate drag and momentum 

To build the state space model for your system, you will need to estimate the drag and momentum terms for your A and B matrices. Here, we will do this using a step response. Drive the car towards a wall at a constant imput motor speed while logging motor input values and ToF sensor output. 
  1. Choose your step responce, u(t), to be of similar size to the PWM value you used in Lab 5 (to keep the dynamics similar). Pick something between 50%-100% of the maximum u.  
  2. Make sure your step time is long enough to reach steady state (you likely have to use active braking of the car to avoid crashing into the wall). Make sure to use a peice of foam to avoid hitting to wall and damaging your car.
  3. Show graphs for the TOF sensor output, the (computed) speed, and the motor input. Please ensure that the x-axis is in seconds.
  4. Measure the steady state speed, 90% rise time, and the speed at 90% risetime. (Note, this doesn't have to be 90% rise time. You could also use somewhere between 60-90%, but the speed and time must correspond to get an accurate estimate for m. 
  5. When sending this data back to your laptop, make sure to save the data in a file so that you can use it even after your Jupyter kernal restarts. Consider writing the data to a [CSV](https://docs.python.org/3/library/csv.html) file, [pickle file](https://docs.python.org/3/library/pickle.html), or [shelve file](https://docs.python.org/3/library/pickle.html). 

### 2. Initialize KF (Python)

1. Compute the A and B matrix given the terms you found above, and discretize your matrices. Be sure to note the sampling time in your write-up.

   ```cpp
   Ad = np.eye(n) + Delta_T * A  //n is the dimension of your state space 
   Bd = Delta_t * B
   ```

2. Identify your C matrix. Recall that C is a m x n matrix, where n are the dimensions in your state space, and m are the number of states you actually measure.
   - This could look like C=np.array([[-1,0]]), because you measure the negative distance from the wall (state 0).

3. Initialize your state vector, x, e.g. like this: x = np.array([[-TOF[0]],[0]])

4. For the Kalman Filter to work well, you will need to specify your process noise and sensor noise covariance matrices. 
   - Try to reason about ballpark numbers for the variance of each state variable and sensor input. 
   - Recall that their relative values determine how much you trust your model versus your sensor measurements. If the values are set too small, the Kalman Filter will not work, if the values are too big, it will barely respond.
   - Recall that the covariance matrices take the approximate following form, depending on the dimension of your system state space and the sensor inputs.

   ```cpp
   sig_u=np.array([[sigma_1**2,0],[0,sigma_2**2]]) //We assume uncorrelated noise, and therefore a diagonal matrix works.
   sig_z=np.array([[sigma_3**2]])
   ```

### 3. Implement and test your Kalman Filter in Jupyter (Python)

1. To sanity check your parameters, implement your Kalman Filter in Jupyter first. You can do this using the function in the code below (for ease, variable names follow the convention from the [lecture slides](../lectures/FastRobots-13-KF.pdf)). 
   - Import timing, ToF, and PWM data from a straight run towards the wall (you should have this data handy from lab 5).  
   - You may need to format your data first. For the Kalman Filter to work, you'll need all input arrays to be of equal length. That means that you might have to interpolate data if for example you have fewer ToF measurements than you have motor input updates. Numpy's linspace and interp commands can help you accomplish this. 
   - Loop through all of the data, while calling the Kalman Filter.
   - Remember to scale your input from 1 to the actual value of your step size (u/step_size).
   - Plot the Kalman Filter output to demonstrate how well your Kalman Filter estimated the system state.
   - If your Kalman Filter is off, try adjusting the covariance matrices. Discuss how/why you adjust them. 
   - Be sure to include a discussion of all the paramters that affect the performace of your filter.


```cpp
def kf(mu,sigma,u,y):
    
    mu_p = A.dot(mu) + B.dot(u) 
    sigma_p = A.dot(sigma.dot(A.transpose())) + Sigma_u
    
    sigma_m = C.dot(sigma_p.dot(C.transpose())) + Sigma_z
    kkf_gain = sigma_p.dot(C.transpose().dot(np.linalg.inv(sigma_m)))

    y_m = y-C.dot(mu_p)
    mu = mu_p + kkf_gain.dot(y_m)    
    sigma=(np.eye(2)-kkf_gain.dot(C)).dot(sigma_p)

    return mu,sigma
```

### 4. Implement the Kalman Filter on the Robot

If you have time, integrate the Kalman Filter into your Lab 5 PID solution on the Artemis. Before trying to increase the speed of your controller, use your debugging script to verify that your Kalman Filter works as expected. Make sure to remove the linear extrpolation step before doing this. Be sure to demonstrate that your solution works by uploading videos and by plotting corresponding raw and estimated data in the same graph. 

The following code snippets gives helpful hints on how to do matrix operations on the robot:

```cpp
#include <BasicLinearAlgebra.h>    //Use this library to work with matrices:
using namespace BLA;               //This allows you to declare a matrix

Matrix<2,1> state = {0,0};         //Declares and initializes a 2x1 matrix 
Matrix<1> u;                       //Basically a float that plays nice with the matrix operators
Matrix<2,2> A = {1, 1,
                 0, 1};            //Declares and initializes a 2x2 matrix
state(1,0) = 1;                    //Writes only location 1 in the 2x1 matrix.
Sigma_p = Ad*Sigma*~Ad + Sigma_u;  //Example of how to compute Sigma_p (~Ad equals Ad transposed) 
```

### 5. Speed it up (optional)

If you have time, and want to get a jump start on Lab 8, try speeding up your robot with your KF to decrease the execution time of your control loop.

### Additional Tasks for 5000-level students

You are off the hook for this lab!

---

## Write-up

To demonstrate that you've successfully completed the lab, please upload a brief lab report (<800 words), with code snippets (not included in the word count), photos, and/or videos documenting that everything worked and what you did to make it happen. 
