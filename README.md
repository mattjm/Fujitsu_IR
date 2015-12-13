#Fujitsu Ductless LIRC Remote

##Overview

This LIRC configuration file implements most of the functionality of many of the infrared remotes for Fujitsu ductless (mini-split) heat pumps and air conditioners.  I've confirmed by testing codes transmitted by the following remotes:

*Fujitsu AR-REG1U

I've tested the codes against the following Fujitsu indoor units:

*ASU7RLF1

##Supported Commands

The following functions are implemented:

-Heat mode.  Auto, quiet, low, medium, and high fan speeds.  Temperatures 60F through 76F, in two degree increments.  (Up to 80F supported with auto fan speed).  The units will go up to 88F with the remote but you shouldn't need to set it higher than 76F.  
-Cool mode.  Auto, quiet, low, medium, and high fan speeds.  Temperatures 64F through 88F.  
-Dry mode.  Auto, quiet, low, medium, and high fan speeds.  Temperatures 64F through 88F.  
-Fan only mode.  Auto, quiet, low, medium, and high fan speeds.  Temperature doesn't matter in this mode but we send a value of 64F since we have to send something.  
-Minimum heat.  Sets to heat mode and 50F, basically a "vacation" mode.  
-Off.  Turns off the unit.  
-I have no plans to implement "auto" mode or swing at the moment.  

##Installation

This will run under a vanilla LIRC install.  I've been using it on a Raspberry Pi with a homebuilt transmitter running off the GPIO pins, but any off the shelf transmitter that's LIRC compatible should work (I don't know if any of these transmitters choke on codes longer than 128 bits--if they do they won't work with these codes).  Once LIRC is running, install the lircd.conf file (or copy contents over if you need other remotes).  Don't forget to restart lircd after changing the config file.  

I have my Raspberry Pi sitting under the indoor unit and use cron to schedule irsend commands.  

##Usage

Use irsend to send the appropriate commands.  The general command form is:

mode-fan_speed-temp

e.g.

heat-auto-68F

So the typical irsend command would be:

irsend send_once fujitsu_heat_ac heat-auto-68F

**IMPORTANT**:  For any of the heat mode commands you can send them if the unit is on or off.  For any of the other modes, the unit must be on before you can send commands for that mode.  For convenience, I've provided "cool-on", "dry-on", and "fan-on" commands So you can start in the appropriate mode before selecting the temp and fan speed you want.  cool-on and dry-on turn on the unit with auto fan speed and 88F temp.  fan-on starts the unit with auto fan speed.  Example usage:


irsend send_once fujitsu_heat_ac cool-on
sleep 2
irsend send_once fujitsu_heat_ac cool-high-70F


(sleep puts a two second delay between the commands)

##Metric

I am in the US so the temperatures are in F (Sorry...).  You'll have to rename the commands in the remote for C.  Equation is (F - 32)*.55.  If you create a metric version submit a pull request and I will add it to the project.  

 

##Acknowledgements:

Thanks to the good folks on the LIRC mailing list for advice on how to approach this  (particularly  Alec Leamas and Bengt Martensson).  I have liberally used AnalysIR and IrScrutinizer to figure this stuff out and/or generate the appropriate codes.  This document from dabrams on remotecentral.com was also invaluable:

http://old.ercoupe.com/audio/FujitsuIR.pdf

##Commands

###*special* commands:

Name | Description
------------|-------------
dry-on	|	Sends command to turn on unit in dry mode
cool-on	|	Sends command to turn on unit in cool mode
fan-on	|	Sends command to turn on unit in fan mode
turn-off	|	Sends command to turn off unit
min-heat	|	Sends command for minimum heat mode (works if unit is on or off)


###normal commands:

In all commands below, `*fan speed*` is to be replaced with one of the following values:

*auto
*high
*medium
*low
*quiet

and `*temp*` is to be replaced with one of the following range of values:

*for dry mode:  64F to 88F
*for heat mode:  60F to 76F
*for cool mode: 64F to 88F

example:  heat-auto-68F

*`heat-*fan speed*-*temp*`  Sends command to turn on unit in heat mode with the specified fan speed and temp (works if unit is on or off--if unit is on the command will just change the fan speed and temp as specified)

*`cool-*fan speed*-*temp*`  Sends command to enable cool mode with specified fan speed and temp (unit must already be on)

*`dry-*fan speed*-*temp*`  Sends command to enable dry mode with specified fan speed and temp (unit must already be on)

*`fan-*fan speed*`  Sends command to enable cool mode with specified fan speed (unit must already be on)
