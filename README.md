DCC Base Station Using ESP8266 plaform
------------------------------------------------

Develop an arduino sketch to enable ESP8266 (WeMos D1 Mini or alternative) to receive data from Model Railway command package (JMRI DCC++) and drive DC Motor (eg via WeMos D1 Motor shield or alternative), Servo's x 3, LED's. Anticipated DCC++ Base Station Commands are Engine Decoder (CAB) Operation Commands per https://github.com/DccPlusPlus/BaseStation/wiki/Commands-for-DCCpp-BaseStation

Enable Serial communication over WiFi

SSID (user edit in sketch)
Password (user edit in sketch)
#define IP_Address (user to comment out to use DHCP if not used)
#define ETHERNET_PORT 2560 (Default, user to comment out if not used)
#define MAC_ADDRESS { 0xDE, 0xAD, 0xBE, 0xEF, 0xFE, 0xEF }


Action Throttle commands from JMRI only if they relate to this ‘CAB’ address

Store in EEPROM the ‘CAB’ address, value 1-10293 (example “03”, user to edit the sketch)
If the command string relates to that ‘CAB’ Address (example “03”);
Where;
The CAB throttle format is < t REGISTER CAB SPEED DIRECTION>
Example < t 1 03 20 1 >.
Breakdown for this example is:

"<" = Listen to me I am the beginning of a DCC++ command. (A space after < is not required but acceptable)
"t" = (lower case t) This command is for a Decoder installed in a engine or simply a "cab".
"1" = REGISTER: (ignore)
"03" = CAB: the short (1-127) or long (128-10293) address of the engine decoder (this has to be already programmed in the decoder) See Programming Commands bellow.
"20" = SPEED: throttle speed from 0-126, or -1 for emergency stop (resets SPEED to 0)
"1" = DIRECTION: 1=forward, 0=reverse. Setting direction when speed=0 or speed=-1 only effects directionality of cab lighting for a stopped train
">" = I am the end of this command

o If the command was successful the serial monitor should reply with : meaning :
"<" = Begin DCC++ command
"T" = (upper case T) DCC++ Cab command was sent from DCC++ BaseStation
"1" = register 1 was changed
"20" = set to speed 20
"1" = forward direction
">" = End DCC++ command?

Drive the WeMos Mini motor shield;
Change to DIRECTION per command
Change to SPEED (0-126) per command;
Subject to
delay(200) (user to modify as per CV)
+F13 Momentum_Value (value user entered in sketch as per a CV)
-F21 Brake_Value (treat as decimal, 0-255(?), decrease speed by this factor, this is emulating braking)


Action CAB Functions

If the commands string relates to that ‘CAB’ Address (example “03”)
Where;
The CAB function format is < f CAB BYTE1 BYTE2 >
The byte 1 & byte 2 values are binary, transmitted as decimal
See https://github.com/DccPlusPlus/BaseStation/wiki/Commands-for-DCCpp-BaseStation for translation of binary commands
If F0 (Lights) is on
Enable LED if DIRECTION = 1 (Forward)
Enable LED if DIRECTION = 0 (Reverse)
Enable LED and if SPEED>0, then brighten for 20s every 120s (firebox)
If F2 (Front Coupler) is
On - Control servo to increase by 45 degrees
Off– Control servo to return to 0 position
If F3 (Rear Coupler) is
On - Control servo to increase by 45 degrees
Off – Control servo to return to 0 position
If F9 Reverser_Value recieved ie < f 03 BYTE1 >, control servo to increase/decrease by 45 degrees
If F13 Momentum_Value received ie < f 03 222 BYTE2 >, map(F13,0,255,0,200),
If F21 Brake_Value received ie < f 03 223 BYTE2 >, map(F21,0,255,0,200), reduce motor speed using delay(Brake_Value)

What’s DCC++
------------

DCC++ is an open-source hardware and software system for the operation of DCC-equipped model railroads.

The system consists of two parts, the DCC++ Base Station and the DCC++ Controller.

The DCC++ Base Station consists of an Arduino micro controller fitted with an Arduino Motor Shield that can be connected directly to the tracks of a model railroad.

The DCC++ Controller provides operators with a customizable GUI to control their model railroad.  It is written in Java using the Processing graphics library and IDE and communicates with the DCC++ Base Station via a standard serial connection over a USB cable or wireless over BlueTooth.

What’s in this Repository
-------------------------

This repository, BaseStation-Uno, contains a complete DCC++ Base Station sketch designed for compiling and uploading into an Arduino Uno.  All sketch files are in the folder named DCCpp_Uno. More information about the sketch can be found in the included PDF file.

To utilize this sketch, simply download a zip file of this repository and open the file DCCpp_Uno.ino within the DCCpp_Uno folder using your Arduino IDE.  Please do not rename the folder containing the sketch code, nor add any files to that folder.  The Arduino IDE relies on the structure and name of the folder to properly display and compile the code.

The latest production release of the Master branch is 1.2.1:

* Supports both the Arduino Uno and Arduino Mega
* Built-in configuration for both the original Arduino Motor Shield as well as a Pololu MC33926 Motor Shield
* Built-in configuration and support of Ethernet Shields (for use with Mega only)

For more information on the overall DCC++ system, please follow the links in the PDF file.

Detailed diagrams showing pin mappings and required jumpers for the Motor Shields can be found in the Documentation Repository

The Master branch contains all of the Base Station functionality showed in the DCC++ YouTube channel with the exception of 2 layout-specific modules:

* Control for an RGB LED Light Strip using pins 44, 45, and 46 on the Mega
* An embedded AutoPilot routine that randomly selects a train to run through the entire layout, after which it is brought back into its original siding and the the patterns repeats with another randomly-selected train.  This is the AutoPilot routine showed on the DCC++ YouTube channel.  It does not require any computer, not DCC++ Controller to be running (DCC++ Controller contains a much more complicated 3-train Auto Pilot mode, also as shown on the DCC++ YouTube channel).

Since these modules are very layout-specififc, they are not included in the Master branch.  However, they are included in the Development branch.  Please feel free to download and copy any relevant code to customize your own version of DCC++ Base Station.

-December 27, 2015

