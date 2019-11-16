# First Baby Steps with nanoFramework

This guide is aimed at users who don't necessarily have a lot of experience with Visual Studio. It sticks exclusively to the latest stable release of nanoFramework so you can quickly and confidently gain familiarity with the build, deployment, and debugging process without getting sidetracked by a bug that appeared yesterday, will be fixed by tomorrow, and would probably derail you *tonight* if you encountered it because you're still learning how everything works.

While reading through this guide, just be aware that nanoFramework is under active, daily development, and almost any "real" project you plan to use it for is going to require using a newer release... *especially* if you're using a library developed by somebody else. For now, don't worry about that. Get your first project using nanoFramework to illuminate LEDs and read buttons to work using the instructions in this guide, then worry about [the more advanced "Getting Started" guide](getting-started-managed.md).

## The hardware

In this guide, we'll be covering two popular platforms: the ST Microelectronics [STM32F746 NUCLEO](http://www.st.com/content/st_com/en/products/evaluation-tools/product-evaluation-tools/mcu-eval-tools/stm32-mcu-eval-tools/stm32-mcu-nucleo/nucleo-f746zg.html), and ESP32 boards based on the Wemos Lolin32 design.

## Installing Visual Studio 2019

The first part is to get Visual Studio 2019 and the **nanoFramework** extension installed.

1. Download Visual Studio 2019. If you already have it installed, you can skip this step. If you don't, please download the free [Visual Studio Community 2019](https://www.visualstudio.com/downloads) edition. Either way, make sure you've selected the .NET desktop workload.

2. Launch Visual Studio 2019 (we'll just refer to it as VS from now on) and install the **nanoFramework** extension. 

* Select Extensions > Manage Extensions (was "Tools > Extensions and Update" in VS2017). 

* In the left pane, select "Online", and enter "nanoFramework" in the Search box (upper right). In the middle pane, you should find something with a name like, "nanoFramework VS2019 Extension [Preview]". Download & install it.

## Understanding the Workflow

When you ***build*** a project, Visual Studio doesn't compile it directly into a binary for your target platform... it compiles it into Common Intermediate Language (CIL). 

CIL gets compiled into a native binary for your target platform when you ***deploy*** it. The target device's second stage bootloader is what actually performs this task. When you deploy a newly-compiled program, nanoFramework's Visual Studio extension does something like this:

* reboots the target device
* waits for the secondary bootloader ("nanoFirmware") to begin executing
* uploads the new program's CIL

At that point, the nanoFirmware compiles the CIL into "real" native binary code and writes it to flash (on some platforms, compilation to native binary might be deferred until the program gets launched).

Subsequently, when the device gets rebooted, the first-stage bootloader launches, executes, and hands control to the second-stage bootloader (nanoFirmware). The second-stage bootloader launches your program.

To get the nanoFirmware second-stage bootloader installed, you need to use [nano Firmware Flasher](https://github.com/nanoframework/nanoFirmwareFlasher):

1. Open a command prompt

2. execute the following command:

    ```console
    dotnet tool install -g nanoFirmwareFlasher
	```
	
3. The next step depends upon whether your board is an ESP32 or a STM32F746:

#### ESP32:

(in the example below, "COM{x}" would be something like "COM4", "COM12", etc... the address Windows has assigned to your board's USB-serial bridge).

    ```console
	nanoff --target ESP32_WROOM_32 --stable --serialport COM{x}
    ```
#### ST board connected through JTAG (ST-Link):

    ```console
	nanoff --target ST_NUCLEO144_F746ZG --update
	```
	
### ST board connected through DFU (like NETDUINO3)

    put the board in DFU mode. This can be accomplished by pressing a certain combination of buttons, and is specific to the particular hardware you're using. Then, execute:
	
	```console
	nanoff --target NETDUINO3_WIFI --update
	```
	
(@ToDo: finish)