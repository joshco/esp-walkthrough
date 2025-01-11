# Arduino ESP32 OpenThread Example

The Arduino ESP32 core has now included OpenThread and built in examples which can be accesed from the Arduino IDE, referred to as IDE below.
This walkthrough will take you through adding the board manager for ESP32 and running the Thread Scan example. Once running, this will scan and list all thread networks and devices.  This walkthrough assumes you are using an ESP32c6 microcontroller.  These can be bought from Amazon for $9/each at https://www.amazon.com/dp/B0BRMSDR4R

## Steps

1. Set up IDE
2. Add board manager
3. Open Thread Scan example
4. Run and display nearby Thread devices

## Setup Arduino IDE

Follow the Arduino IDE install steps here:

https://support.arduino.cc/hc/en-us/articles/360019833020-Download-and-install-Arduino-IDE

## Add ESP32 Board Manager

In the menu, go to File->Preferences.  In the preferences dialog, look for "Additional boards manager URLs" and click the button to the right of it.
In the dialog that pops up, add the following URL. (if you have existing URLs, make them one per line)

> https://docs.espressif.com/projects/arduino-esp32/en/latest/installing.html

Press OK, and OK.  The IDE will then download the required files.  This could take a bit.

For more detail see this page on espressif's github:  https://espressif.github.io/arduino-esp32/package_esp32_index.json

Next, go to the Boards manager by clicking the board icon in the left nav, or via the menu Tools->Board->Boards Manager.  search for esp32, look for esp32 by Espressif Systems. 
Make sure you are on the latest version in the dropdown.  If not, select it and click update/install.  The current version at the time of writing this is 3.1.1.

## ThreadScan Example

In the menu, select File->OpenThread->ThreadScan.  This will open an IDE window with the example in it.  You can compile it to make sure your install is correct using the "Check mark" icon in the top toolbar.  Don't run it or download it yet.

## Connect and select ESP32c6 Microcontroller

Plug in your esp32c6 microcontroller into a USB port.  On the MCU, make sure to use the "UART" socket, not "USB".  Next select the right MCU type and serial port.

In the top menu bar, there is a dropdown, drop it down.  At the bottom, click "Select other board and port".  In the "BOARDS" search box type esp32c6 and select "ESP32C6 Dev Module".

On the right side, under "PORTS", select the port (COM or mac equivalent) that you have plugged in the MCU.  If you are on windows and aren't sure, open device manager and look under "Ports".  
You should see something like "Silicon Labs CP210x USB to UART Bridge (COMXX)."   Note the XX.  Go back to the Arduino IDE and find that port in the PORTS section.

Press OK

## Run Example

In the top toolbar, click the right arrow "->" to flash the code to your MCU and run it.  It will say "Compiling sketch" and then "Uploading..."

Once it finishes, in the output window you should see:

```
Hard resetting via RTS pin...
```

Then open the Serial Monitor.  In the top menu bar on the right, you should see an icon like a magnifying glass.  Click it, or in the menu, Tools->Serial Monitor. It will open a new "Serial Monitor" tab.  Make sure the baud rate is set to 115200.

In the serial monitor, you will see the device starting Thread. it will show nearby thread devices.  It may take a minute for it to settle into a Thread role, then it will do an MLE discover.  

The output will look like:

```
Scanning for nearby IEEE 802.15.4 devices:
| PAN  | MAC Address      | Ch | dBm | LQI |
+------+------------------+----+-----+-----+
| f9cd | e2d23afed33b5705 | 11 | -71 |  10 |
| b835 | 52f2cbf464600b36 | 16 | -76 |  11 |
| 2681 | b2548053b974122a | 20 | -31 |   9 |
| 45c9 | fafa5607f627a1c6 | 21 | -87 |   9 |
| 3b8b | 6a22693272675e8b | 25 | -63 |  10 |
| 6ec0 | bb274d023e63371e | 25 | -66 |  11 |
| fbc3 | 00d02dfffe10b8e4 | 25 | -85 |   9 |

Scanning MLE Discover:
| Network Name     | Extended PAN     | PAN  | MAC Address      | Ch | dBm | LQI |
+------------------+------------------+------+------------------+----+-----+-----+
| OpenThread-2681  | 246111c98d5a931e | 2681 | b2548053b974122a | 20 | -32 |  11 |
| NEST-PAN-45C9    | 6e01b7c4fc68932a | 45c9 | fafa5607f627a1c6 | 21 | -83 |  10 |
| MyHome21         | f4167e799d2b44dc | 3b8b | 6a22693272675e8b | 25 | -64 |   8 |
| eero-thread-c686 | 991e261d115fa54e | c686 | 6a9b8f271a8dd146 | 25 | -83 |  10 |
| MyHome76         | 5a8ebe67d7a24ef7 | 9363 | 42ca87e08b67203b | 25 | -72 |  10 |
| MyHome803848385  | 7c015bee450c4bcb | faea | 227fdd11bd248d80 | 25 | -88 |   8 |
| MyHome2094945166 | 9dd5ec833e244bb9 | c848 | 4646d5f23bd08aac | 25 | -84 |   9 |
| MyHome25         | 0a230a8f0f7b4335 | 1dcc | 6606cbd7ee465aa6 | 25 | -83 |   8 |
| MyHome25         | 0a230a8f0f7b4335 | 1dcc | 6606cbd7ee465aa6 | 25 | -81 |  10 |
| MyHome829145893  | 0312002621614514 | 676f | ee5d5690198efb59 | 25 | -80 |  10 |
| MyHome16         | 67d5af696b8547e5 | a768 | 5ea695e0b4d06afe | 25 | -71 |  10 |
| eero-thread-dce4 | 1d17f0fe73ae4f87 | dce4 | 87fa0fa07a54bdd2 | 25 | -90 |   7 |
| MyHome1143715505 | d52c215002884e8b | 2ebc | ea9d7d35abfcc56b | 25 | -95 |   4 |

```
