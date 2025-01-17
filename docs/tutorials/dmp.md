# Digital Motion Processing for Orientation

In Fast Robots, we use the [Pimoroni ICM-20948 9DoF breakout board](https://shop.pimoroni.com/products/icm20948) to detect our car's orientation. The Pimoroni board is a platform for the [TDK InvenSense ICM-20948](https://invensense.tdk.com/products/motion-tracking/9-axis/icm-20948/), a 9-axis motion tracking sensor which we'll simply call the "ICM". We also use the SparkFun ICM-20948 [Arduino library](https://github.com/sparkfun/SparkFun_ICM-20948_ArduinoLibrary), which was written by SparkFun for their own [9DoF IMU breakout board](https://www.sparkfun.com/products/15335), but is compatible with any board fitted with the ICM.

One of the ICM's key features is the InvenSense digital motion processor (DMP), which is described in the [ICM datasheet](https://www.invensense.com/wp-content/uploads/2016/06/DS-000189-ICM-20948-v1.3.pdf) as follows:

> The DMP enables ultra-low power run-time and background calibration of the accelerometer, gyroscope, and compass, maintaining optimal performance of the sensor data for both physical and virtual sensors generated through sensor fusion.

In other words, the DMP is capable of **error and drift correction** by fusing readings from the ICM's 3-axis gyroscope, 3-axis accelerometer, 3-axis magnetometer/compass.

By default, the DMP is disabled in the SparkFun Arduino library because it requires 14 kB of additional program memory on the host microprocessor. Activating the DMP requires a small modification to the library (detailed in Step 1 below), while usage and configuration is illustrated in several of SparkFun's example Arduino sketches:

- [Example6_DMP_Quat9_Orientation](https://github.com/sparkfun/SparkFun_ICM-20948_ArduinoLibrary/blob/main/examples/Arduino/Example6_DMP_Quat9_Orientation/Example6_DMP_Quat9_Orientation.ino)
- [Example7_DMP_Quat6_EulerAngles](https://github.com/sparkfun/SparkFun_ICM-20948_ArduinoLibrary/blob/main/examples/Arduino/Example7_DMP_Quat6_EulerAngles/Example7_DMP_Quat6_EulerAngles.ino)
- [Example8_DMP_RawAccel](https://github.com/sparkfun/SparkFun_ICM-20948_ArduinoLibrary/blob/main/examples/Arduino/Example8_DMP_RawAccel/Example8_DMP_RawAccel.ino)
- [Example9_DMP_MultipleSensors](https://github.com/sparkfun/SparkFun_ICM-20948_ArduinoLibrary/blob/main/examples/Arduino/Example9_DMP_MultipleSensors/Example9_DMP_MultipleSensors.ino)
- [Example10_DMP_FastMultipleSensors](https://github.com/sparkfun/SparkFun_ICM-20948_ArduinoLibrary/blob/main/examples/Arduino/Example10_DMP_FastMultipleSensors/Example10_DMP_FastMultipleSensors.ino)
- [Example11_DMP_Bias_Save_Restore_ESP32](https://github.com/sparkfun/SparkFun_ICM-20948_ArduinoLibrary/blob/main/examples/Arduino/Example11_DMP_Bias_Save_Restore_ESP32/Example11_DMP_Bias_Save_Restore_ESP32.ino)

Most relevant to our purposes is [Example7_DMP_Quat6_EulerAngles](https://github.com/sparkfun/SparkFun_ICM-20948_ArduinoLibrary/blob/main/examples/Arduino/Example7_DMP_Quat6_EulerAngles/Example7_DMP_Quat6_EulerAngles.ino), which will take quaternion data from the DMP and convert it to roll, pitch, and yaw angles. The video below shows a visual demo of the DMP quaternion output.

<iframe width="672" height="378" src="https://www.youtube-nocookie.com/embed/b1g0XmEFRLw?rel=0" frameborder="0" allow="accelerometer; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

Alright, enough preamble! Follow the steps below to get the DMP and the visual demo running on your Artemis.

## Getting Started

### Step 1: Modifying the Library

Open the SparkFun ICM-20948 library directory on your computer. The location is typically something like:

- Windows: `Documents\Arduino\libraries\SparkFun_ICM-20948_ArduinoLibrary\src\util`
- Mac: `~/Documents/Arduino/libraries/SparkFun_9DoF_IMU_Breakout_-_ICM_20948_-_Arduino_Library/src/util`

There, edit the file `ICM_20948_C.h` and uncomment [line 29](https://github.com/sparkfun/SparkFun_ICM-20948_ArduinoLibrary/blob/main/src/util/ICM_20948_C.h#L29) to define the constant `ICM_20948_USE_DMP`.

### Step 2: Example Sketch

In the Arduino IDE, open the sketch `Examples > SparkFun 9DoF IMU Breakout > Arduino > Example7_DMP_Quat6_EulerAngles`.

Similar to what we did in [Lab 2](https://fastrobotscornell.github.io/FastRobots/labs/Lab2.html), you may need to change the value of `AD0_VAL` to `0` in the line:

```cpp
#define AD0_VAL 1
```

Burning the sketch to your Artemis with the IMU breakout connected over I<sup>2</sup>C should present you with the serial prompt `Press any key to continue...`. Pressing `Enter` will then start a stream of Euler angles for pitch, roll, and yaw. This can be plotted using the serial plotter function.

### Step 3: Visualization (Optional)

Graphically visualizing the DMP output as seen in the video above is **completely optional**. However, we've found that doing this is both an interesting demo and a useful debugging tool to understand the behavior of the DMP.

If you want to try the visualization, install [Node.js](https://nodejs.org/en/download) v20 or later to your computer. Then, clone the [3D visualization repository](https://github.com/synthghost/quaternion_sensor_3d_nodejs) and edit the file `index.js` to set the `SERIAL_PORT` and `SERIAL_BAUD` values for your particular setup. Also run the following in the repository directory to add the needed libraries:

```bash
npm install
```

Next, edit `Example7_DMP_Quat6_EulerAngles` and uncomment [line 27](https://github.com/sparkfun/SparkFun_ICM-20948_ArduinoLibrary/blob/9a10c510ddb694f08aa93c12d586358cb45abd2b/examples/Arduino/Example7_DMP_Quat6_EulerAngles/Example7_DMP_Quat6_EulerAngles.ino#L27) to define `QUAT_ANIMATION`. Burn the sketch to your Artemis but *close all serial monitors and serial plotters* to give the Node.js process exclusive control over Serial.

To start the demo, run:

```bash
node index.js
```

The console should start outputting quaternion data. The visualization can then be opened at [http://localhost:3000/](http://localhost:3000/) in a browser.

## Now What?

With the example sketch—and possibly the visualization—running, you should have access to Euler angles for the various orientations of the IMU. The trick now is to integrate the DMP functionality into the rest of your Artemis logic. For the most part, this can be done by using the code from the example sketch [Example7_DMP_Quat6_EulerAngles](https://github.com/sparkfun/SparkFun_ICM-20948_ArduinoLibrary/blob/main/examples/Arduino/Example7_DMP_Quat6_EulerAngles/Example7_DMP_Quat6_EulerAngles.ino), starting with the `setup()` function:

```cpp
bool success = true;

// Initialize the DMP
success &= (myICM.initializeDMP() == ICM_20948_Stat_Ok);

// Enable the DMP Game Rotation Vector sensor
success &= (myICM.enableDMPSensor(INV_ICM20948_SENSOR_GAME_ROTATION_VECTOR) == ICM_20948_Stat_Ok);

// Set the DMP output data rate (ODR): value = (DMP running rate / ODR ) - 1
// E.g. for a 5Hz ODR rate when DMP is running at 55Hz, value = (55/5) - 1 = 10.
success &= (myICM.setDMPODRrate(DMP_ODR_Reg_Quat6, 0) == ICM_20948_Stat_Ok); // Set to the maximum

// Enable the FIFO queue
success &= (myICM.enableFIFO() == ICM_20948_Stat_Ok);

// Enable the DMP
success &= (myICM.enableDMP() == ICM_20948_Stat_Ok);

// Reset DMP
success &= (myICM.resetDMP() == ICM_20948_Stat_Ok);

// Reset FIFO
success &= (myICM.resetFIFO() == ICM_20948_Stat_Ok);

// Check success
if (!success) {
    Serial.println("Enabling DMP failed!");
    while (1) {
        // Freeze
    }
}
```

The above should run once, after the call to `myICM.begin(Wire, AD0_VAL)` that you likely already have somewhere in your own `setup()` from completing [Lab 2](https://fastrobotscornell.github.io/FastRobots/labs/Lab2.html). When in doubt, use the `Example7_DMP_Quat6_EulerAngles` sketch to guide you.

To then continuously read the orientation quaternions from the DMP, use the code from the example's `loop()` function:

```cpp
icm_20948_DMP_data_t data;
myICM.readDMPdataFromFIFO(&data);

// Is valid data available?
if ((myICM.status == ICM_20948_Stat_Ok) || (myICM.status == ICM_20948_Stat_FIFOMoreDataAvail)) {
    // We have asked for GRV data so we should receive Quat6
    if ((data.header & DMP_header_bitmap_Quat6) > 0) {
        double q1 = ((double)data.Quat6.Data.Q1) / 1073741824.0; // Convert to double. Divide by 2^30
        double q2 = ((double)data.Quat6.Data.Q2) / 1073741824.0; // Convert to double. Divide by 2^30
        double q3 = ((double)data.Quat6.Data.Q3) / 1073741824.0; // Convert to double. Divide by 2^30

        // Convert the quaternion to Euler angles...
    }
}
```

The quaternion components can be converted to Euler angles as shown in the example sketch and explained on [Wikipedia](https://en.wikipedia.org/w/index.php?title=Conversion_between_quaternions_and_Euler_angles&section=8#Source_code_2). Note that for our purposes in Fast Robots, you will probably want **yaw**. To maximize performance, try to calculate only what you need.

## Pitfall: FIFO

An important concept to understand when using the DMP for orientation is that the sensor fusion data is sent through a [FIFO queue](https://en.wikipedia.org/wiki/FIFO_(computing_and_electronics)) (first in, first out) before sent over I<sup>2</sup>C. To help explain this idea, imagine the DMP as an assembly line: sensor readings go in, sensor fusion math is performed, and quaternions get pulled out. This means older data leaves the DMP before newer data. To get to the latest orientation readings, we have to completely empty the queue by pulling data out in a loop like in the example code shown earlier. But there's a catch.

If improperly configured or *read too slowly* (yes, really), the DMP's queue can **fill up**, causing the chip to crash! Typically, with sensors like the individual accelerometer or gyroscope on the IMU, we repeatedly poll for data until it is ready. When sensors aren't as fast as the Artemis control loop, we have to poll several times before data arrives. But the DMP isn't typical; the dedicated fusion processor on the breakout board can generate data much faster than any individual sensor. Once powered and initialized, the DMP's readings will enter the queue indefinitely. It's *your* job to pull them out (using `myICM.readDMPdataFromFIFO()`, shown earlier) so they don't get stuck there. And because the chip has limited memory, just like any microprocessor, too many readings left stuck in the queue will overload the DMP, causing unexpected behavior.

Alright, so how do we always get the latest orientation readings while also being careful not to fill up the queue? There are two options: read the data fast enough to keep up, or configure the DMP to generate data less frequently.

The first option is illustrated at the bottom of the example sketch [Example7_DMP_Quat6_EulerAngles](https://github.com/sparkfun/SparkFun_ICM-20948_ArduinoLibrary/blob/main/examples/Arduino/Example7_DMP_Quat6_EulerAngles/Example7_DMP_Quat6_EulerAngles.ino):

```cpp
// Only delay between readings if no more data is available
if (myICM.status != ICM_20948_Stat_FIFOMoreDataAvail) {
    delay(10);
}
```

The above is the only `delay()` statement in the example's `loop()` function. When, after we pull out a reading, there's more data left in the DMP's queue, the condition evaluates to false and the Artemis will continue polling as fast as it can until the queue is empty; only then will it wait for the next reading.

This first option works fine in isolation when the Artemis is able to loop freely. In our case, with a BLE connection to maintain and other sensors to check, there may be significant unavoidable delays elsewhere in the program, rendering the Artemis unable to keep up with the DMP, as ironic as that may sound.

The second option, slowing down the DMP, is a safer bet. In the `setup()` code shown earlier, the relevant line is this:

```cpp
// Set the DMP output data rate (ODR): value = (DMP running rate / ODR) - 1
// E.g. for a 5Hz ODR rate when DMP is running at 55Hz, value = (55/5) - 1 = 10.
success &= (myICM.setDMPODRrate(DMP_ODR_Reg_Quat6, 0) == ICM_20948_Stat_Ok); // Set to the maximum
```

The second argument passed to `setDMPODRrate()` is an integer value denoting the desired output data rate (ODR). As the comment suggests, that value can be calculated from the DMP's assumed clock speed of 55 Hz and the desired frequency of readings. For example, to receive orientation quaternions at a rate of roughly 10 Hz, we would pass `4` as the second argument to `setDMPODRrate()`.

By slowing down the output rate of the DMP, the Artemis control loop should now be running faster than the DMP generates data, and the queue should not fill excessively. That said, there's always a chance that another bit of logic elsewhere slows down the Artemis and leaves a window for memory overflow. So to be absolutely sure, we recommend combining both options above to ensure you always have the latest orientation data, no matter what.

## Your Turn

That's all for now. Go try out the DMP! The higher quality and reliability of the orientation quaternions over the basic IMU readings are worth the extra effort and will help make your car a more robust autonomous system.

---

*Special thanks to [Stephan Wagner](https://fast.synthghost.com/) for providing this tutorial to Fast Robots.*
