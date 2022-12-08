# LcdDriver using IC2 #
***
## PROJECT ##
| Project name: | Halflink/LCDDriver |
|---------------|--------------------|
| Author:       | Jeroen van Zwam    |
| Date:         | 2022-12-08         |
| Last update:  | 2022-12-08         |
| Project type: | Raspberry Pi       | 
***
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
Alternatively you could buy a [HD44780](https://www.benselectronics.nl/16-x-2-hd44780-lcd-display.html) and the [I2C module](https://www.benselectronics.nl/i2c-module-1602-2004-lcd.html) separately.
* [I2C Bi-directional Logic Level Converter](https://elektronicavoorjou.nl/product/4-channel-i2c-logic-level-converter/)
<br>
Make sure its BI-directional! See explanation of the wiring why. 
***
## The project ##
Finally, the project! Start with the wiring. 

![Breadboard set-up](/docs/lcddriver_bb.png)




