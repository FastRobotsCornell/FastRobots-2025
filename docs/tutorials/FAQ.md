# FAQ

## Setup

#### 1. The serial monitor prints gibberish characters or has no output?
Match the baud rate on your serial monitor with the value specified in the Arduino sketch.

####  2. My ArduinoIDE fails to upload the sketch.
1. Try a different USB port.
2. Connect the Artemis board directly to a USB port and not through a USB dongle.
3. Lower the baud rate.
4. Follow the [CH340 tutorial](./CH340.md)
5. Try again :).

#### 3. How to read a python exception?
When python throws an exception, it first displays the code traceback and then finally the Exception message. Read the exception message at the bottom and use the traceback to find the line of code that triggered the exception.
More info on exceptions and exception handling can be found [here](https://realpython.com/python-exceptions/).

#### 4. How to improve Arduino performance?
1. Keep the loop() processing short.
2. In your final code, remove unnecessary interim variables.
3. Prevent printing excessive messages to the serial port.

#### 5. I've generated a UUID and it doesn't seem to be working?
There are some reserved UUIDs in the bluetooth specification and steer away from them. (Reserved UUID: https://www.novelbits.io/uuid-for-custom-services-and-characteristics/)


### Windows
#### 1. C++ Build Tools Error when installing pip packages?
Click on the link in the error messages and download the installer. In the installation process, make sure to select "C++ Build tools". The exact download link may change with the version of Windows. 
After installation, restart your computer and try installing the pip package again. If you are still face issues:
   1. You may need to open a CMD prompt with *C++ build tools* added to your **PATH**.
   2. You may need to update setup tools in some cases ([Ref](https://stackoverflow.com/questions/29846087/error-microsoft-visual-c-14-0-is-required-unable-to-find-vcvarsall-bat))

### macOS
#### 1. Python throws an error about a missing library named **pyobc**.
Some macOS versions may not have the library pre-installed. 
In Terminal, install the package using the following command:
```bash
python3 -m pip install pyobjc
```

#### 2. Connection issues in macOS 12.
TLDR: A documented bug ([feature](https://stackoverflow.com/questions/69097567/macos-version-returned-as-10-16-instead-of-12-0)?) in macOS reports the incorrect macOS version number in some machines. In file **base_ble.py**, change *Line 16*
from
```python
    IS_ATLEAST_MAC_OS_12 = objc.macos_available(12,0)
``` 
to
```python
    IS_ATLEAST_MAC_OS_12 = True
```
Why is this important? Well, they also modified the Bluetooth API in macOS 12. The provided basecode checks for the macOS version number and handles the Bluetooth API accordingly. Unfortunately, it does not account for this bug in some machines, where a macOS 12 might report its version number as "10.16".

#### 3. Programming issues in macOS 14+.
TLDR: Update the SVL executable following the process described [here](./CH340.md).

## Artemis
#### 1. The ToF sensors don't provide consistent values.
Make sure you (carefully) remove the protective film on top of the sensor.
