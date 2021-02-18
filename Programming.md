### Seeed MT7628 development board programming development

Seeed MT7628 development board supports C/C++, Python, Node.js for device application development.

1. **C/C++**

   - Environment

     MT7628 is available with SDK for Linux and MacOS platforms. 

     | Software packages                                            | Version |
     | ------------------------------------------------------------ | ------- |
     | [OpenWrt SDK for C/C++ for Linux](https://files.seeedstudio.com/tool/MT7628/openwrt_mt7628_sdk_linux.tar.bz2) | 0.9.1   |
     | [OpenWrt SDK for C/C++ for OS X](https://files.seeedstudio.com/tool/MT7628/openwrt_mt7628_sdk_osx.tar.bz2) | 0.9.1   |

     Download the corresponding SDK according to your platform, take Linux as an example

     ```shell
     wget https://files.seeedstudio.com/tool/MT7628/openwrt_mt7628_sdk_linux.tar.bz2 -O SDK.tar.bz2
     sudo tar -xvjf SDK.tar.bz2 && cd SDK
     ```

   - Example

     - Download the [example package file](https://files.seeedstudio.com/tool/MT7628/7628-OpenWrt-SDK-example-package.zip), unzip it and copy the example project to the ***SDK/package/*** directory

     ```
     SDK/package
        +helloworld              # Name of the package
             -Makefile           # This Makefile describes the package
             +src
                 -Makefile       # This Makefile builds the binary
                 -helloworld.c   # C/C++ source code
     ```

     - View the sample program helloworld.c

     ```c
     /****************
     * Helloworld.c
     * The most simplistic C program ever written.
     * An epileptic monkey on crack could write this code.
     *****************/
     #include <stdio.h>
     
     int main(void)
     {
     	printf("Hell! O' world, why won't my code compile?\n\n");
     	return 0;
     }
     ```

     - In the **SDK/** directory, type make

     ```shell
     $ make package/helloworld/compile
     ```

     - After the compilation is complete, enter the ***SDK/bin/ramips/packages/base*** directory, find *helloworld_1.0.0-1_ramips_24kec.ipk.*

     - Copy *helloworld_1.0.0-1_ramips_24kec.ipk* toSeeed MT7628 development board, Refre [here](asd).

     - Go to the location of *helloworld_1.0.0-1_ramips_24kec.ipk* on the Seeed MT7628 development board.

       ```shell
       $ opkg install helloworld_1.0.0-1_ramips_24kec.ipk
       ```

     - Type **helloworld** in Terminal

   For more C/C++ application development, please refer to

   - https://wiki.openwrt.org/doc/howto/obtain.firmware.sdk
   - http://gargoyle-router.com/old-openwrt-coding.html
   - [mraa: libmraa - Low Level Skeleton Library for Communication on GNU/Linux platforms (intel.com)](http://iotdk.intel.com/docs/master/mraa/)
   - TO-DO: C/C++

2. **Python**

   - GPIO

     To control the GPIO pins, initialize the pin as GPIO pin and set its *mode*. The simplest operation mode is OUTPUT — set the pin to **HIGH** or **LOW** to enable or disable external switches or to form signal patterns.

     ```Python
     import mraa  
     pin = mraa.Gpio(2)    
     # Initialize GPIO2 (P10 on LinkIt Smart 7688 board)  
     pin.dir(mraa.DIR_OUT) # set as OUTPUT pin  
     ```

     Then, call *pin.write(0)* to set the pin state to **LOW** or call *pin.write(1)* to set the pin state to **HIGH**. To make the Wi-Fi LED blink periodically, set pin 44 (*WLED_N*) to GPIO mode and execute the following code:

     ```python
     import mraa  
     import time    
     # Refer to the pin-out digram for the GPIO number to silk print mapping  
     # in this example the number 44 maps to Wi-Fi LED.  
     pin = mraa.Gpio(44)  
     pin.dir(mraa.DIR_OUT)    
     while True:
         pin.write(1)
         time.sleep(1)
         pin.write(0)
         time.sleep(1) 
     ```

     There’s another GPIO mode which is **INPUT** and it takes a digital signal input from the pin and maps to 1s and 0s. This example continuously prints the value received from **P10** on the board. You can short **3V3** and **P10** to observe the changes in values.

     ```python
     import mraa  
     import time    
     # Refer to the pin-out diagram for the GPIO number to silk print mapping  
     # in this example the number 2 maps to P10 on LinkIt Smart 7688 board  
     pin = mraa.Gpio(2)  
     pin.dir(mraa.DIR_IN)    
     while True:
         print "P10 state:", pin.read()
         time.sleep(0.3)
     ```

     Finally, an interrupt service routine (ISR) can be installed to the pin and invoked when the value of the input pin **P10** (**GPIO2**) is changed. Call ISR API with the trigger type you want to register and the function to be called. Note that the function runs in a different thread.

     ```python
     import mraa  
     import time    
     def callback(userdata):      
         print "interrupt triggered with userdata=", 
     userdata    
     pin = mraa.Gpio(2)  
     pin.dir(mraa.DIR_IN)  
     pin.isr(mraa.EDGE_BOTH, callback, None)    
     while(True):      
         time.sleep(1)      
         # simply wait for interrupt  
     ```

   - PWM

     Use PWM module to generate a pulse width modulated signal patterns. This is useful to control actuator peripherals such as servo motors. To use PWM, initialize it on a certain pin such as GPIO. Note that only **GPIO18, GPIO19, GPIO20** and **GPIO21** support PWM on the LinkIt Smart 7688 development board. To control the PWM pattern, several parameters are required, including:

     **Period**
     This defines the carrier frequency of the modulation. It’s controlled by period(), period_ms() and period_us() APIs.

     **Duty cycle or pulse width**
     These two parameters are related to each other and usually you only need to set one of them. Duty cycle is controlled by write API with a value from 0.0 to 1.0, where 0.0 is 0% of duty cycle and 1.0 is 100% of duty cycle. Pulse width also defines the pattern in a different unit: the "uptime" of the signal in time units. This is defined by pulsewidth(), pulsewidth_ms(), and pulsewidth_us() APIs.

     The following example generates a 500Hz PWM signal with 25% duty cycle on pin P26.

     ```python
     import mraa    
     pin = mraa.Pwm(18)  # initialize on GPIO18 (pin P26)
     pin.period_ms(2)    # set PWM frequency to 500Hz (2ms period)
     pin.enable(True)    # enable PWM output
     pin.write(0.25)     # set duty cycle to 25%  
     ```

   - I2C

     Inter-Integrated Circuit (I2C) is a widely used protocol among peripherals. It consists of 2 signal pins — usually named SDA and SCL. LinkIt Smart 7688 development board has one set of I2C on **GPIO4** (**P21**) and **GPIO5** (**P20**) as SCL and SDA, respectively. The way I2Cs are initialized is slightly different from GPIO modules. Instead of using pins, I2Cs are initialized based on the **device index**. Since there is only one set of I2C master device on the board, you can simply pass **0**, and it's always on pins **GPIO4** and **GPIO5**.

     ```python
     import mraa 
     i2c = mraa.I2c(0) 
     ```

     I2C is capable of connecting multiple slave devices to a single I2C master. Each slave device is identified by a 7-bit address. The following example scans for a Seeed Studio 3-Axis Digital Accelerometer attached to the LinkIt Smart 7688 development board by reading the device ID from its registers.

     ```python
     import mraa
      
     i2c = mraa.I2c(0)
     # Grove - 3-Axis Digital Accelerometer(+-16g)
     # is a ADXL345 configured to I2C address 0x53.
     i2c.address(0x53)
     # The device ID should be 
     if 0xE5 == i2c.readReg(0x00):
      print "Grove - 3-Axis Digital Accelerometer found on I2C Bus"
     else:
      print "Grove - 3-Axis Digital Accelerometer not found" 
     ```

     For more details on the I2C API, refer [here](http://iotdk.intel.com/docs/master/mraa/python/mraa.html#i2c). To drive an I2C device, you need to operate multiple I2C sequences. The LinkIt Smart 7688 development board also has pre-installed [libUPM](http://labs.mediatek.com/site/global/developer_tools/mediatek_linkit_smart_7688/training_docs/peripherals_7688/using_upm_python/index.gsp) — a set of driver repository built upon libmraa. Please see [here](https://github.com/intel-iot-devkit/upm/blob/master/examples/python/adxl345.py) for a driver example of Seeed Studio 3-Axis Digital Accelerometer.

   - SPI

     Serial Peripheral Interface (SPI) can also be used to control peripheral devices. LinkIt Smart 7688 development board consists of 4 pins — **SPI_MOSI** (**P22**), **SPI_MISO** (**P23**), **SPI_CLK** (**P24**) and **SPI_CS1** (**P25**).

     It’s important to note that the SPI device is also used for communicating with the on-board internal flash storage. Therefore, developers should access the SPI functionality through SPI modules only and avoid treating the SPI pins as general GPIO. Otherwise, the flash storage may not operate correctly.

     The SPI module in libmraa is initialized by the **device index** instead of pin number:

     ```python
     import mraa  
     spi = mraa.Spi(0)
     spi.frequency(200000)
     while True:
         spi.writeByte(0x55)
     ```

   **More Detail refer [API Referance](http://iotdk.intel.com/docs/master/mraa/python/mraa.html)**

   