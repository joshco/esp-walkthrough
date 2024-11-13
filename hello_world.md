# Overview of walkthrough

This walkthrough will guide you through:
1. Setting up your dev environment
2. Building the ClI example onto microcontroller (MCU)
3. Flashing MCU 1 and activating the thread system
4. Flashing MCU 2 and activating the thread system
5. Running tcpsockserver on MCU 1 and tcpsockclient on MCU 2 and sending messages back and forth

## Toolchain
I tried Vscode/platformio and the espressif vscode extension. They worked except for openthread stuff.  I decided to start with the espressif SDK. This walkthrough is pretty smooth. The issues I ran into were Windows WSL related. I haven't done it on a Mac, so we'll see what happens.

## Hardware requirements
	2 esp32c6 microcontrollers
	These can be bought from Amazon for $9/each
	https://www.amazon.com/dp/B0BRMSDR4R?ref=ppx_yo2ov_dt_b_fed_asin_title&th=1
	The walkthrough assumes both MCUs are connected to a USB port at the same time.  Make sure you have enough ports!

# Walkthrough

## Setting up dev environment

Complete steps 1-4 on this setup guide for linux/macos
The steps will install the macOS prerequisites, the use git to download the idf v5.3.1 sdk, and set up environment variables need to run the sdk
https://docs.espressif.com/projects/esp-idf/en/v5.3.1/esp32/get-started/linux-macos-setup.html

Though not needed for this walkthrough, if you want to install the matter-sdk, see step 2.2 at:
https://docs.espressif.com/projects/esp-matter/en/latest/esp32/developing.html#esp-matter-setup

	Building and CLI example
	
	Flashing MCU 1 and activating the thread system
	
	Flashing MCU 2 and activating the thread system
	
	Running tcpsockserver on MCU 1 and tcpsockclient on MCU 2 and sending messages back and forth
	
		Tcpsockserver
		Tcpsockclient
		
