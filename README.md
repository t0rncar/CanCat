# CanCat
CanCat is an open source multi-purpose tool for interacting and experimenting with Controller Area Networks (CAN), such as those that are often used in cars, building automation, etc. 

## Description
CanCat has two main parts:  
1) Firmware for a compatible CAN-transceiver,  
2) Python client to talk to the tool,  

The CAN-transceiver combinations that are currently supported by CanCat are:
* Arduino with SeeedStudio's CANBUS Shield
* Arduino DUE with Togglebit.net's CAN shield
* [Macchina M2 (Under-the-Hood)](https://www.macchina.cc/catalog) 
* [Macchina M2 (Under-the-Dash)](https://www.macchina.cc/catalog)

The goals of CanCat are to provide:
* a way to capture and transmit messages on an arbitrary CAN bus (whatever the speed, as supported by hardware)
* an architecture for analyzing and identifying messages on a CAN bus
* a manner for data to be shared (Ford sends different messages from GM, Saab, Toyota, Honda, Hyundai, Kia, etc.) (either in the form of lookup tables or in the form of accessor codes.)



## Software 
### Required:
* Python 2.7


### Recommended:
* CANBUS_Shield from SeeedStudio (for Firmware compilation)  
    https://github.com/Seeed-Studio/CAN_BUS_Shield

* vstructs From the Vivisect framework in the Python Path:  
    (required for J1939 module)  
    https://github.com/vivisect/vivisect

* ipython



## Installation
1) Install pyserial:
```
$ pip install --user pyserial
```

2) (OPTIONAL) Install ipython if you want to use CanCat interactively.
```
$ pip install --user ipython
```

3) Install the [Arduino IDE](https://www.arduino.cc/en/main/software).  

4) (OPTIONAL) If you are using a [Macchina M2](https://www.macchina.cc/) follow the [getting started guide](http://docs.macchina.cc/m2/getting-started/arduino.html) for the M2 to install the M2 hardware definitions in the Arduino tool.

5) (OPTIONAL) If you are on a Linux system, you may choose to install the [arduino-builder](https://github.com/arduino/arduino-builder) for your platform. The arduino-builder tool can be used to compile and flash your CAN device without opening the Arudino IDE. 

6) Clone CanCat and build the desired firmware. If you are not using the arduino-builder tool, use the Arduino IDE as normal to build and flash the sketch onto your target device. 

```
$ git clone https://github.com/atlas0fd00m/CanCat
$ cd CanCat/sketches
$ make m2
$ make bootloader
$ make flash
```

7) Ensure that your CAN-transceiver is not in bootloader mode by unplugging its USB connector and then plugging it back in again.

8) Use CanCat with your CAN-transceiver.



## Connecting to your CAN-transceiver with CanCat.
Once you have the required software installed and your device is flashed, you can use CanCat with your CAN-transceiver.

### Connect to the CAN-transceiver with CanCat [Linux]:
```bash
$ ./CanCat.py -p /dev/ttyACM0  # if CanCat device is /dev/ttyACM0
```

```
'CanCat, the greatest thing since J2534!'

Research Mode: enjoy the raw power of CanCat

currently your environment has an object called "c" for CanCat.  this is how 
you interact with the CanCat tool:
    >>> c.ping()
    >>> c.placeBookmark('')
    >>> c.snapshotCanMsgs()
    >>> c.printSessionStats()
    >>> c.printCanMsgs()
    >>> c.printCanSessions()
    >>> c.CANxmit('message', )
    >>> c.CANreplay()
    >>> c.saveSessionToFile('file_to_save_session_to')
    >>> help(c)
```

### Connect to the CAN-transceiver with CanCat [Linux and other systems]:
```python
>>> import cancat

>>> CANalysis = cancat.CanInterface('/dev/ttyACM0', 115200) # your device may vary

>>> CANalysis.ping()
```

`>>>` and `In [#]:` are used interchangeably in this instruction guide.  
`>>>` is the default interactive python prompt, and commands using this prompt will use the `CANalysis` object.  
`In [#]:` is the ipython prompt, and commands using this prompt will begin with the `c` object.  
Otherwise, the commands for CanCat will look the same.



## Getting Started
### Pinging your CAN-transceiver
To see if CanCat is communicating correctly with your computer and the connected CAN-transceiver, you can use the `ping()` command.

```python
In [1]: c.ping()
```


### Setting the Baud Rate
Once you connect to your CAN-transceiver, you will want to use CanCat to set the CAN bus interface baud rate on the device. *(Note: 500kbps is the most likely baud rate for most devices you will interact with.)*

```python
>>> CANalysis.setCanBaud(cancat.CAN_125KBPS)
```

Once you connect to the CAN-transceiver and set the CAN-transceiver, CanCat will automatically capture any messages it sees on the CAN bus it is attached to. CanCat will store these messages in the current session for analysis. Unless you save the CanCat capture, the messages you have captured will no longer be stored once you end your cancat session.

### Saving CanCat captures
CanCat will only save what it has captured when you tell it to save, so make sure to save your capture session / analysis periodically. 

```python
>>> CANalysis.saveSessionToFile('filename_for_this_session')
```

Once you save for the first time, the file name will be cached so you can simply save your capture to the same file again by typing:

```python
>>> CANalysis.saveSessionToFile()
```

### CanCat help and tips
To access the help function in CanCat:
```>>> help(cancat)```

If help doesn't provide you with what you are looking for, you can do a tab-complete search to bring up each of the possible commands.
```python
In [6]: c.<PRESS_TAB_KEY>
c.CANrecv
c.genCanMsgs
c.ping
c.recv
c.saveSessionToFile
c.CANreplay
c.getArbitrationIds
c.placeCanBookmark
c.recvall
c.setCanBaud
c.CANsniff
c.getBookmarkFromMsgIndex
c.port
c.register_handler
c.setCanBookmarkComment
c.CANxmit
c.getCanMsgCount
c.printAsciiStrings
c.remove_handler
c.setCanBookmarkCommentByMsgIndex
c.bookmark_info
c.getMsgIndexFromBookmark
c.printBookmarks
c.reprBookmark
c.setCanBookmarkName
c.bookmarks
c.getSessionStats
c.printCanMsgs
c.reprBookmarks
c.setCanBookmarkNameByMsgIndex
c.clearCanMsgs
c.getSessionStatsByBookmark
c.printCanMsgsByBookmark
c.reprCanMsgs
c.snapshotCanMessages
c.comments
c.loadFromFile
c.printCanSessions
c.reprCanMsgsByBookmark
c.verbose
c.filterCanMsgs
c.log
c.printSessionStats
c.restoreSession
c.filterCanMsgsByBookmark
c.name
c.printSessionStatsByBookmark
c.saveSession
```


## Other CanCat Uses
### Using CanCat to Analyze Previous Captures
If all you want to do is analyze a previous CanCat capture you can skip the hardware set up steps, clone the CanCat repository, add the file you wish to analyze to the CanCat folder and run the command:

```bash
$ ./CanCat.py -f filename_of_previous_capture  # no CanCat device required
```


### CAN-in-the-Middle
CAN-in-the-Middle is another way to utilize your CanCat. It requires two CAN shields 
on one arduino. One of the CAN shields needs to be modified so that the CS pin of the 
MCP2515 CAN controller is on D10, rather than D9. This is accomplished by cutting a 
trace on the CAN shield PCB and bridging (solder bridge or 0-ohm resistor) the pads
for CS and D10. Instructions are also on the seeedstudio Wiki, although their board 
differed slightly from mine, mostly in that the pads are on the bottom of the board 
on mine and on the top of the board in their example.

Once you have a properly modified CAN Bus shield, you'll be able to isolate components
connected to the CAN bus to see which messages a specific device is sending, without
changing the conditions by fully removing it from the CAN Bus. This can be very helpful for 
certain reverse engineering tasks.

Flash the CAN_in_the_middle firmware to the Arduino. Hook the CAN wires up so that the 
device you are trying to isolate is connected to the modified CAN shield that uses D10
for CS, and the vehicle CAN bus (with the rest of the devices) is connected to the 
unmodified CAN shield. These are referred to as the Isolation network (ISO)
and the Vehicle network (VEH) respectively.

Start CAN_in_the_middle with the following command:

`./CanCat.py -I CanInTheMiddle -p /dev/tty.usbmodem1411 -S 500K`

where the -p option is your port and -S is the CAN Baud rate

Most of the commands for CanInTheMiddle are the same as the normal CanCat interface. 
Functions that report only what has been received on the Isolation side have Iso appended 
to the end of the function name. For example:

```sh
$ citm.getCanMsgCount() # The number of CAN packets seen in aggregate

$ citm.getCanMsgCountIso() # The number of CAN packets received on the Isolation network

$ citm.printCanMsgs() # Prints all CAN messages

$ citm.printCanMsgsIso() # prints all CAN messages received on the Isolation network
```

Placing a bookmark places a bookmark simultaneously on both the Isolation information (Iso interface messages) and the aggregate information (standard CAN interface messages).

## Acknowledgments
This project is made possible through collaboration with researchers at GRIMM (SMFS, Inc.), most notably Matt Carpenter and Tim Brom.

## Happy Hacking!



