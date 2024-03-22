# Enabling the IMU DMP

In Fast Robots, we use the [Pimoroni ICM-20948 9DoF breakout board](https://shop.pimoroni.com/products/icm20948) to detect our car's orientation. The Pimoroni board is a platform for the TDK InvenSense ICM-20948, a 9-axis motion tracking sensor. We also use the SparkFun ICM-20948 [Arduino library](https://github.com/sparkfun/SparkFun_ICM-20948_ArduinoLibrary), which was written by SparkFun for their own [9DoF IMU breakout board](https://www.sparkfun.com/products/15335), but is compatible with any board fitted with the TDK InvenSense ICM-20948, which we'll simply refer to as the "ICM".

One of the ICM's key features is the InvenSense digital motion processor (DMP), which is described in the [ICM datasheet](https://www.invensense.com/wp-content/uploads/2016/06/DS-000189-ICM-20948-v1.3.pdf) as follows:

> The DMP enables ultra-low power run-time and background calibration of the accelerometer, gyroscope, and compass, maintaining optimal performance of the sensor data for both physical and virtual sensors generated through sensor fusion.

In other words, the DMP is capable of **error and drift correction** by fusing readings from the ICM's 3-axis gyroscope, 3-axis accelerometer, 3-axis magnetometer/compass.

By default, the DMP is disabled in the SparkFun Arduino library, because it requires 14 kB of additional program memory on the host microprocessor. Activating the DMP requires a small modification to the library (detailed below), while configuration is illustrated in several of SparkFun's example Arduino sketches:

- [Example6_DMP_Quat9_Orientation](https://github.com/sparkfun/SparkFun_ICM-20948_ArduinoLibrary/blob/main/examples/Arduino/Example6_DMP_Quat9_Orientation/Example6_DMP_Quat9_Orientation.ino)
- [Example7_DMP_Quat6_EulerAngles](https://github.com/sparkfun/SparkFun_ICM-20948_ArduinoLibrary/blob/main/examples/Arduino/Example7_DMP_Quat6_EulerAngles/Example7_DMP_Quat6_EulerAngles.ino)
- [Example8_DMP_RawAccel](https://github.com/sparkfun/SparkFun_ICM-20948_ArduinoLibrary/blob/main/examples/Arduino/Example8_DMP_RawAccel/Example8_DMP_RawAccel.ino)
- [Example9_DMP_MultipleSensors](https://github.com/sparkfun/SparkFun_ICM-20948_ArduinoLibrary/blob/main/examples/Arduino/Example9_DMP_MultipleSensors/Example9_DMP_MultipleSensors.ino)
- [Example10_DMP_FastMultipleSensors](https://github.com/sparkfun/SparkFun_ICM-20948_ArduinoLibrary/blob/main/examples/Arduino/Example10_DMP_FastMultipleSensors/Example10_DMP_FastMultipleSensors.ino)
- [Example11_DMP_Bias_Save_Restore_ESP32](https://github.com/sparkfun/SparkFun_ICM-20948_ArduinoLibrary/blob/main/examples/Arduino/Example11_DMP_Bias_Save_Restore_ESP32/Example11_DMP_Bias_Save_Restore_ESP32.ino)

Most relevant to our purposes is [Example7_DMP_Quat6_EulerAngles](https://github.com/sparkfun/SparkFun_ICM-20948_ArduinoLibrary/blob/main/examples/Arduino/Example7_DMP_Quat6_EulerAngles/Example7_DMP_Quat6_EulerAngles.ino), which will take quaternion data from the DMP and convert it to roll, pitch, and yaw angles. The video below shows a visual demo of the DMP quaternion output.

<iframe width="672" height="378" src="https://www.youtube-nocookie.com/embed/b1g0XmEFRLw?rel=0" frameborder="0" allow="accelerometer; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

Follow the steps below to get the DMP and the visual demo running on your Artemis.

## Step 1: Modifying the Library

Open the SparkFun ICM-20948 library directory on your computer. The location is typically something like:

- Windows: `Documents\Arduino\libraries\SparkFun_ICM-20948_ArduinoLibrary\src\util`
- Mac: `~/Documents/Arduino/libraries/SparkFun_9DoF_IMU_Breakout_-_ICM_20948_-_Arduino_Library/src/util`

There, edit the file `ICM_20948_C.h` and uncomment [line 29](https://github.com/sparkfun/SparkFun_ICM-20948_ArduinoLibrary/blob/main/src/util/ICM_20948_C.h#L29) to define the constant `ICM_20948_USE_DMP`.

## Step 2: Example Sketch

In the Arduino IDE, open the sketch `Examples > SparkFun 9DoF IMU Breakout > Arduino > Example7_DMP_Quat6_EulerAngles`.

Similar to what we did in [Lab 2](https://fastrobotscornell.github.io/FastRobots/labs/Lab2.html), you may need to change the value of `AD0_VAL` to `0` in the line:

```cpp
#define AD0_VAL 1
```

Burning the sketch to your Artemis with the IMU breakout connected over I2C will present you with the serial prompt `Press any key to continue...`. Pressing `Enter` should then start a stream of Euler angles for pitch, roll, and yaw. This can be plotted using the serial plotter function.

## Step 3: Optional: Visualization

The visualization seen in the video above requires [Node.js](https://nodejs.org/en/download). Install v20 or later.

Edit the `Example7_DMP_Quat6_EulerAngles` sketch and uncomment [line 27](https://github.com/sparkfun/SparkFun_ICM-20948_ArduinoLibrary/blob/9a10c510ddb694f08aa93c12d586358cb45abd2b/examples/Arduino/Example7_DMP_Quat6_EulerAngles/Example7_DMP_Quat6_EulerAngles.ino#L27) to define `QUAT_ANIMATION`. Burn the sketch to your Artemis but *close the serial monitor and serial plotter*. The Node.js runtime will need exclusive access to the serial port.

After installation, clone the [3D visualization repository](https://github.com/synthghost/quaternion_sensor_3d_nodejs) and edit the file `index.js` to set the `SERIAL_PORT` and `SERIAL_BAUD` values for your particular setup.

Next, run the following in the respository directory to install the needed libraries:

```bash
npm install
```

Then, to start the demo, run:

```bash
node index.js
```

The console should start outputting quaternion data. The visualization can then be opened under [http://localhost:3000/](http://localhost:3000/) in a browser.

Acknowledgements: Special thanks to Stephan Wagner for providing this tutorial to Fast Robots.
