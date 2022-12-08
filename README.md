# LcdDriver using IC2 #
***
## PROJECT ##
| Project name: | Halflink/LCDDriver |
|---------------|--------------------|
| Author:       | Jeroen van Zwam    |
| Date:         | 2022-12-08         |
| Last update:  | 2022-12-08         |
| Project type: | Raspberry Pi       | 
etje van slag door de aankomende conferentie...zelfs de lokale zebrapaden en opst

### Executable ###
* None
* You can run LcdDriver.py to test your LCD display. 

***

## PROJECT DESCRIPTION ##

OK, why another project describing how to drive an LCD with your Raspberry PI?<br>
Well, in short: because all others confused me.<br>
So What happened; While researching hard/software for my [InternetRadio](https://github.com/Halflink/InternetRadio) project, 
I came across a lots of LCD - Python how-tos, but none of them painted the complete picture for me. One explained wiring and code without I2C, and therefore needed lots of GPIO pins. Another explained the I2C wiring, but failed to mention that the LCD screen is 5 volts while the PI GPIO is based on 3.3 volts (smokey!) etc.<br>
With this project I try to describe how I solved this, and why. Please feel free to reach out to me in case you feel that I missed something or got something totally wrong. 
Please note that Pi & electronics are my hobby. **I'm not a professional**, and will not take any responsibility or liability on what you do with my tips & explanations. 

### Scope ###
* Drive an LCD screen with 1-4 lines
* Explanation of correct wiring

### Hardware ###
* Raspberry Pi 3B / 4B
* [HD44780 with I2C adapter module.](https://www.benselectronics.nl/16-x-2-hd44780-lcd-gemonteerde-i2c-adaptor-module.html)
<br>
Alternatively you could buy a 
[HD44780](https://www.benselectronics.nl/16-x-2-hd44780-lcd-display.html) and the 
[I2C module](https://www.benselectronics.nl/i2c-module-1602-2004-lcd.html) separately.
* [I2C Bi-directional Logic Level Converter](https://elektronicavoorjou.nl/product/4-channel-i2c-logic-level-converter/)
<br>
Make sure its BI-directional! See explanation of the wiring why. 

***

## The project ##
Finally, the project! Start with the wiring. 

### Hardware ###

![Breadboard set-up](/docs/lcddriver_bb.png)

1. Connect power and GND
    <br>
    As your Pi and its GPIO ports works on 3v3 volts and the LCD display on 5 volts, you will need both on your breadboard. 
Luckily the Pi produces both. I see 3v3 as the 'main' voltage. This is why I use the red wires for 3v3. 
On the breadboard I use the lower plus-line for the 3v3 voltages. 
The 5 volts get the orange wires, and the upper plus-line on the breadboard.    
   1. connect PI `pin 1 (3V3)` to the bottom breadboard plus line (red)
   2. connect PI `pin 2 (5V)` to the top breadboard plus line (orange)
   3. connect the breadboard bottom and top GND lines (black)
   4. Connect PI `pin 6 (GND)` to the bottom breadboard GND line (black)
2. Connect the I2C logic level converter.
<br>
The voltage difference between your Pi and the display presents a challenge. Either the display does not get enough juice, or changes are you fry your GPIO ports.
There is a way to solve this: Logic level converters. There are a few out there, and not all of them will work. Make sure you have a bi-directional, so you do not need to worry if you go from 3v3 to 5 or from 5 to 3v3.
Also, the number of channels differ. You need 2, which means 8 pins (HV, GND, HSCL, HSDA, LV, GND, LSCL, LSDA). And to add to the confusion, there are different ways to name the pins too.
The one I actually used has pins on one side called `GND`, `HV`, `A1`, `A2`, `A3` and `A4`, and on hte other side called `GND`, `LV`, `B1`, `B2`, `B3` and `B4`. 
I've also seen converters using pin names  `HV`, `HV1`, `HV2`, `GND` on one side and `LV`, `LV1`, `LV2`, `GND` on the other, and probably there are more variants out there.
<br>
More information why using the level converter you can find [here](https://littlebirdelectronics.com.au/guides/196/using-a-logic-level-shifter-with-raspberry-pi) .
There are people out there suggesting you do not need the converter, as long as you disable the pull-up resistors. [Here](https://community.element14.com/products/raspberry-pi/f/forum/19334/is-level-shifting-really-needed-for-i2c) you can find a discussion about that.
My advice would be a quote from Brian Cohen: *'You don't need to follow me. You don't need to follow anybody! You've got to think for yourselves! You're all individuals!'*
<br>
And now the pinning:
    1. place the converter on the breadboard, the 5V side up (near the 5 volts line)
    2. Connect the `HV` (the VCC high) to the 5V line on the breadboard
    3. Connect the `GND` of the high side to a GND line on the breadboard
    4. Connect the `LV` (the VCC low) to the 3v3 line on the breadboard
    5. Connect the `LV1` or `B1` (depending on your I2C type) to PI `pin 5 (SCL)` (green wire)
    6. Connect the `LV2` or `B2` (depending on your I2C type) to PI `pin 3 (SDA)` (yellow wire)
4. Connect the LCD display 
<br>   
I assume you have the I2C adapter already soldered on the LCD display. If not, the wiring is straightforward. `pin 1` to `pin 1`, `pin 2` to `pin 2`, ..`pin 16` to `pin 16`.
   1. Connect `VCC` to `HV` line of the converter on the breadboard
   2. Connect `GND` to the GND line on the breadboard
   3. Connect `SCL` to the `HV1` or `A1` line of the converter on the breadboard
   4. Connect `SDA` to the `HV2` or `A2` line of the converter on the breadboard

### preparing your PI ###
1. On your PI, open bash / terminal
2. install I2C tools `sudo apt-get install i2c-tools`
3. Install SMBUS `sudo apt-get install python-smbus`
4. Next, enable I2C. 
   1. Open the config menu by typing `sudo raspi-config`
   2. Go to 'advanced settings'
   <br>

   ![AdvancedSettings](/docs/raspi-config_1.png)
   
   5. Select 'I2C Enable/Disable automatic loading'
   
   ![AdvancedSettings](/docs/raspi-config_2.png)

5. Reboot your pi
6. Open a terminal again and check the address of the LCD. Make sure only one device is connected to the I2C ports (yes, you can staple multiple devices ;) ) `i2cdetect -y 1`

![scan i2c](/docs/i2c_device_scan.png)

note the address here, 27. You need this when initiating your LCD in python

### Python ###
 
Alter the `lcd = LcdDriver()` line of LcdDriver.py in case your address is NOT 27. Imagine your address is 68, you can change the line to `lcd = LcdDriver(lcd_address=0x68)`.
Now run the test! `python3 LcdDriver.py`
<br>
<br>
Using the code in your project, you simply add the LcdDriver.py to your project and import it. 4 things you need to do in your code:
1. import it `from LcdDriver.py import LcdDriver`
2. Initialise: `lcd = LcdDriver()`. There are 3 options you can give here:
   1. lcd_address (default 0x27)
   2. lcd_with (default 16)
   3. lcd_lines (default 2)
3. Show something! `lcd.lcd_string('Halflink',0)` First parameter is the text you want to show, second is the line number (starting from 0)

In case you want to know more on how I2C works, I found [this](https://www.circuitbasics.com/basics-of-the-i2c-communication-protocol/) helpful.




















