# Thread Hello World

## Overview of walkthrough

This walkthrough will guide you through:

1. Setting up your dev environment
2. Building the ClI example
3. Flashing MCU 1 and activating the thread system
4. Flashing MCU 2 and activating the thread system
5. Running tcpsockserver on MCU 1 and tcpsockclient on MCU 2 and sending Hello_World messages back and forth

### Toolchain
I tried Vscode/platformio and the espressif vscode extension. They worked except for openthread examples.  I decided to start with the espressif SDK. This walkthrough is pretty smooth. The issues I ran into were Windows WSL related. If you are using WSL2 and have trouble with USB/IP, contact me. I haven't done it on a Mac, so we'll see what happens.

### Hardware requirements
2 esp32c6 microcontrollers. These are the type we used at the hackathon

These can be bought from Amazon for $9/each at https://www.amazon.com/dp/B0BRMSDR4R?ref=ppx_yo2ov_dt_b_fed_asin_title&th=1

The walkthrough assumes both MCUs are connected to a USB port at the same time.  Make sure you have enough ports!

## Walkthrough

### Setting up your dev environment

Complete steps 1-4 on this setup guide for linux/macos

https://docs.espressif.com/projects/esp-idf/en/v5.3.1/esp32/get-started/linux-macos-setup.html

#### Notes
The steps will install the macOS prerequisites, the use git to download the idf v5.3.1 sdk, and set up environment variables need to run the sdk.
In step 3, when you run the ./install.sh, don't add the esp32 parameter.  That will only install esp32, not esp32c6.  Don't add a param for it to install all

> ./install.sh

You should finish with

> source ./export.sh

That adds environment variables needed by the SDK.

Open a second terminal window, cd to ./esp-idf and source ./export.sh.  You can use the two terminal windows for MCU1 and MCU2.

#### Assumptions

The following steps assume that 

1. You installed the SDK in ./esp-idf
2. Your current directory is ./esp-idf
3. That you are using the esp32c6 MCUs mentioned above
4. You are plugging your USB cable into the MCU port that says "UART" not "USB"
5. That you have both MCUs plugged in.

Some of the referenced guides use a different MCU, such as esp32h2, we will using esp32c6 instead.

When flashing with both devices connected, you will need to specify the serial port.  Plug in the first and look in /dev to see the serial port for MCU 1, write down PORT_MCU1, then plug in MCU 2 and write down  PORT_MCU2.

On WSL2 using USB/IP, these are: 

> /dev/ttyUSBX

Based on my research (not having a mac), on mac, the ports should be something like:

> /dev/tty.usbmodemXXXX

### Building and CLI example

> cd  examples/openthread/ot_cli

Look over the README.md file to get an overview of the steps:

> less README.md

We're skipping some unnecessary steps. No need for the menuconfig step unless you need to change defaults, or use JTAG.

Now lets build. First set the MCU type

> idf.py set-target esp32c6

Next, build the project

> idf.py build

### Flashing MCU 1 and activating the thread system

Flash the MCU and then run serial monitor.  This will give you tty access to the CLI.

>  idf.py -p PORT_MCU1 flash monitor

You should now have access to the CLI.  Set up the Thread network.

> factoryreset
> dataset init new
> dataset commit active
> ifconfig up
> thread start

Wait a few seconds then run

> state

The response should be "leader".  

Now we need to save some information that we will give to MCU2 when we set that up on the same network.  We will get the IPv6 addresses and the dataset in hex to cut and past into MCU2 later.

> ip print

Output will contain the ip addresses:

```
netif: ot
ot inet6: FE80::6C0F:B3AD:5369:D8CF 48
ot inet6: FD1C:D88F:2EFF:A6FC:CCE0:E15A:26B9:8E46 16
ot inet6: FF03::2
ot inet6: FF02::2
ot inet6: FF33:40:FD1C:D88F:2EFF:A6FC:0:1
ot inet6: FF32:40:FD1C:D88F:2EFF:A6FC:0:1
ot inet6: FF03::FC
ot inet6: FF03::1
ot inet6: FF02::1

netif: lo
lo inet6: ::1 16
```

The address that worked for me is the one suffixed by "16"

Get the Active Dataset

> dataset active -x

Output will contain the hex representation of the dataset.  You'll need this later.

```
0e080000000000010000000300001835060004001fffe00208fe................
```

### Flashing MCU 2 and activating the thread system

In your other terminal window, make sure you source ./export.sh in ./esp-idf.  Change directory to ./esp-idf/examples/openthread/ot_cli

Flash and start serial monitor

> idf.py -p PORT_MCU2 flash monitor

You should now have access to the CLI.  Set up the Thread network.  We'll be using MCU1's hex representation of its dataset.

> factoryreset
> dataset set active 0e080000000000010000000300001835060004001fffe00208fe................
> ifconfig up
> thread start

Wait for a bit and you should see a log message like:

```
I(548857) OPENTHREAD:[N] Mle-----------: RLOC16 9c00 -> fffe
I(549077) OPENTHREAD:[N] Mle-----------: Attach attempt 1, AnyPartition reattaching with Active Dataset
I(549877) OPENTHREAD:[N] Mle-----------: RLOC16 fffe -> 6401
I(549887) OPENTHREAD:[N] Mle-----------: Role detached -> child
```

### Running tcpsockserver on MCU 1 and tcpsockclient on MCU 2 and sending messages back and forth

The tcpsockserver and tcpsockclient commands in this walkthrough are part of the OpenThread CLI extensions defined here:

https://github.com/espressif/esp-thread-br/blob/main/components/esp_ot_cli_extension/README.md

We will run the socket server on MCU1 and the client on MCU2.  

#### Tcpsockserver on MCU1

Open the tcpsockserver and bind to port 12345

> tcpsockserver open
> tcpsockserver bind :: 12345

You should see log messages like:

```
I (375370) ot_ext_cli: Socket created
I (375370) ot_ext_cli: Socket bound, port 12345
I (375370) ot_ext_cli: Socket listening
I (375380) ot_ext_cli: Successfully created
I(389660) OPENTHREAD:[N] RouterTable---: Allocate router id 39
```

#### Tcpsockclient on MCU2

We will open the tcpsockclient on MCU2 and connect it to MCU1 on port 12345.  Replace ADDRESS with one of the IPv6 addresses on MCU1. I had to try more than one address before it worked, you may need to as well.
The address that worked for me is the one suffixed by "16"

> tcpsockclient open
> tcpsockclient connect ADDRESS 12345

If it worked, you should see log messages:

```
I (1096557) ot_ext_cli: Socket created, connecting to FD1C:D88F:2EFF:A6FC:CCE0:E15A:26B9:8E46:12345
I (1096617) ot_ext_cli: Successfully connected
```

If it didn't work, you'll see:

```
I (1060397) ot_ext_cli: Socket created, connecting to FF33:40:FD1C:D88F:2EFF:A6FC:0:1:12345
E (1078897) ot_ext_cli: tcp_client_add(83): Socket unable to connect: errno 113
I (1078897) ot_ext_cli: Fail to create TCP client
```

Once it is connected, let's send Hello_World!

> tcpsockclient send Hello_World

Note: If you want to send a message with spaces in it, you'll need to use backslash as an escape.  Eg Hello\ World

#### Send from server MCU1 to MCU2

You can send a message back to the client:

> tcpsockserver send Thread\ Rules

In your MCU2 window, you should see logs like:

```
I (3296957) ot_ext_cli: sock 54 Received 12 bytes from FD1C:D88F:2EFF:A6FC:CCE0:E15A:26B9:8E46
I (3296957) ot_ext_cli: Thread Rules
```

We are all done!



