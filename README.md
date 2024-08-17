# Standard Operating Procedures to Upgrade the Device Computer

*I like to use markdown to take these notes*


> **NOTE:** You do not want to just upgrade the device computer
>without first considering upgrading the Monochromater, and secondly
>validating that all the little devices hanging arround work. A new
>computer will hit you at roughly $1200, it would be a shame if the
>new computer did not work with the old equipment.

> **NOTE:** Additionally, you could just upgrade the computer with
>new hardware, more ram, and new versions of windows. Unless you
>let the hardware get really old... do not buy all new hardware.

> **NOTE:** You may also be tempted to think this process is easy.
>It is not. It is a pain and will take allot of time to get everything
>working. You will need to install drivers, software, and configure
>the software to work with the devices. You will need to test the
>devices to ensure they are working. You will need to test the software
>to ensure it is working. You will need to test the computer to ensure
>it is working. You will need to make devices to test the various parts
>of the communication system. And there will be bugs... lots of bugs.

**Good Luck & Please email hduvallh@gmail.com if you need help...**

****

# Igor Pro Installation
On the current computer, there are a number of locations that
Igor Pro looks to to find the various files that it needs to
run your macros. You will need to copy these files to the new
computer. This is a very straight forward process, but it is
important that you do not miss any files. If you do, the macros
will not work.

These files come in three flavors:
1. Procedure Files
2. XOP Files
3. Documentation Files

## Procedure Files
Procedure files need to be copied to the new computer under either
the `Igor Procedures` directory, or the `User Procedures` directory.
You can just copy both directories to the new computer, and make sure
you update the Igor Pro version number in the directory name.

## XOP Files
Similar to the procedure files, XOP files need to be copied to the
new computer in the location you found them on the old computer.

## Documentation Files
The same thing goes for the documentation files. Copy them to the
new computer in the location you found them on the old computer.


# Enviroment Variables

Igor Pro does not have simple functions to parse enviroment variables
loaded in a .env file. This is the standard process in other languages,
but not in Igor Pro. Thus there are a number of repeaded string variables
in Igor Pro that need to be updated.

## Please consider implementing a .env file loader for Igor Pro

You might want to spend some time constructing a .env file loader for igor.
You can base the code off of [this](https://github.com/Isty001/dotenv-c).

```c
// Sample Igor Like Code
struct env_vars{
    string name;
    string value;
};

struct env{
    string path;
    env_vars *vars;
};

function env_load("path/to/.env"){
    // Load the .env file
    // Parse the .env file
    // Set the variables in the .env file
}
```
## Enviroment Variables Needed
There are three ways devices will talk to the device computer.

1. NI VISA - Com Port & USB & GPIB
2. NI VISA - IP
3. Special Mono USB Com Port

### NI VISA - Com Port & USB & GPIB
These are most of your devices. You need to first install the NI VISA
software. Plug in one important device that is connected though a usb,
or GPIB (Start with the lock in). Open the NI MAX software and see if
the device is recognized. If it is not recognized, you need to install
the drivers for the device. If the device is recognized, it will display
a Instrument Descriptor, and allow you to communicate with the device.
Validate that you can communicate with the device though the NI MAX
software using the VISA test panel. To test you can communicate with the
device, you will want to query the device using the interogate command
`*IDN?`. It will respond with a string that contains the device name,
model, and serial number. If you do not get a response, the device
is not connected, not working, the drivers are not installed, or the
device has no interogation response.

If you can communicate with the device, write down the Instrument
Descriptor. This is a string that is used to communicate with the device
though the NI VISA interface. Once you have this string, you can either
toss it into the .env file, or you can place it in the code at every
location that needs to communicate with the device.

### NI VISA - IP
There are like two devices that communicate over IP (Though these devices
are of the most importance). Only tackle these devices after you have ensured
that the NI VISA software is installed and working, and probably after you have
gotten the Com Port & USB & GPIB devices working.

NI VISA IP devices are a bit more complicated. You will need to know the IP
address of the device, and the port that the device is listening on. To do this,
you will need to look though the devices that are connected to the computer
over a local area network. You can do this by opening the terminal and typing

```powershell
arp -a
```

This will list all the devices that are connected to the computer. You will
need to look for the devices that are connected on the same network as the
device computer. Once you have the IP address of the device, you can use
the NI MAX software to setup the IP connection by adding a new device with
the proper IP address. You can then test the connection by selecting the
test connection button. If the connection is successful, you can then
communicate with the device using the VISA test panel. Once more you will
want to query the device using the interogate command `*IDN?`. It will
respond with a string that contains the device name, model, and serial
number. If you do not get a response, the device is not connected, not
working, the drivers are not installed, or the device has no interogation
response.

Once you have the IP address of the device, NI VISA will create a string
that is used to communicate with the device though the NI VISA interface.
This string is called the Instrument Descriptor. Once you have this string,
you can either toss it into the .env file, or you can place it in the code
at every location that needs to communicate with the device.

### Special Mono Com Port
The monochromater is a special device that communicates over a TCP/SOCKET
connection. Before you can communicate with the monochromater, you will
need to insure that all other devices are working reliably, as there is
currently (for good reasons) no way to atomaticly test the monochromater
connection.

To communicate with the monochromater, you will need to achieve the following
landmarks:

1. Install the monochromater drivers
2. Install the monochromater software
3. Ensure the monochromater software can communicate with the monochromater
and set wavelengths, gratings, and filters.
4. Transfer the monochromater .NET communication code to the device computer
5. Update all places in the code that communicate with the monochromater to
correctly point to this .NET code.

Each of these can be a pain, and will take time to get working.

#### **Install the monochromater drivers**
You will need to locate the drivers for the monochromater. DO NOT TRUST THE
FILDERS IN THIS DIRECTORY! They are old, may not work, or may not work for the
monochromater that we have. PLEASE UPDATE THE FOLDERS IN THIS DIRECTORY WITH
THE LATEST DRIVERS FROM THE MONOCHROMATER MANUFACTURER! The current monochromater
is the.

**Model:** [Oriel Cornerstone 130 1/8 m Monochromater](https://www.newport.com/f/cs130-high-resolution-monochromators).

You will need to install the drivers using the windows device manager. Pointing
to the drivers in your install directory. Once the drivers are installed, you
will see a new USB device in the device manager that named something like
`Oriel Cornerstone 130 1/8 m Monochromater`. You next need to test the connection
because you may have installed the wrong drivers. and the device will
not work.

#### **Install the monochromater software**
On the same website there will be a software download. Make sure to download
that software and install it. You will need to install both
- Mono Control Utility
- Mono Terminal Utility

Once these are installed, restart the computer. Plug in the monochromater
and turn on the monochromater. Once you flip the switch, the monochromater
will make some strange noises, and the computer will recognize the device.
If it does not... something is wrong... email me at hduvallh@gmail.com.

#### **Ensure the monochromater software can communicate with the monochromater**
Ensure the mono is plugged in and turned on. Open the Mono Control Utility
and see if you can change the wavelength, and filter. If you can, the
monochromater is working. If you can not, the monochromater is not working.

Close the Mono Control Utility and open the Mono Terminal Utility. You will
need now want to send the commands "WAVE?" and "GRAT?" to the monochromater.
If you get a response, the monochromater is working. If you do not get a
response, the monochromater is not working.

#### **Transfer the monochromater .NET communication code to the device computer**
You will need to copy the .NET code that igor runs to interface with the
Mono. This code is located under the WaveMetrics directory in the Documents
folder. It will be labeled something like `IgorMonoControl`. You will need
to copy this directory to the new device computer. Once you have copied it,
do not dare touch the code. It is very fragile and will break if you look
at it wrong. It was compiled in a very specific way, using a very specific
version of visual studio, and a very specific version of the .NET framework.
If you need to change the code, you will need to recompile. Go to the end of
this document to see how to recompile the code.

Igor runs this code by running a script that is located in the bin of the
`IgorMonoControl` directory. Igor cannot run executables, but can run scripts.
You need to edit the `start.bat` file to point to the correct location of the
`IgorMonoControl.exe` file. Once you have done this, you can run the `start.bat`
file and it will start the .NET code. This should bring up a terminal window
displaying information about the status of a connection to the monochromater,
and to igor. If you do not see this, the code is not working.

#### **Update all places in the code that communicate with the monochromater to correctly point to this .NET code**
You will need to update all the places in the code that communicate
with the monochromater to point to this `start.bat` file. You can do this
either by updating the code to point to the `start.bat` file, or by creating
the .env file to follow the single source of truth pattern.


# Recompiling and Updating the Mono Control Utility
You will never want to do this. If you need to, you have two options.

1. Recompile the code using the visual studio project on the old
windows 8 computer. If you choose this option, you will need to make a copy of the entire code base to preserver the code.

2. Construct your own .NET runtime enviroment to recompile the code. This is a pain, and will take a long time to figure out if you have not done this before. But it is possible, and would benifit the lab in the long run.

Using either of these options, it is important to note the general
structure of the API. The API is a simple TCP/SOCKET connection that
acts as a tunnel between the monochromater control software and Igor.
Igor sends commands to the monochromater control software, and the
monochromater control software parses these commands and sends them to
the monochromater using a old crappy Cypress USB to Serial driver.
The general flow of the API is as follows:

1. Igor instantiates the IgorMonoControl (IMC) program.
2. IMC connects to the monochromater.
3. IMC opens a TCP/SOCKET connection on port 700.
4. Igor connects to the IMC on port 700.
5. Igor sends commands to the IMC. (WAVE?, GRAT?, FILTER?, etc.)
6. IMC parses the commands and sends them to the monochromater.
7. The monochromater responds to the commands.
8. If the monochromater responds, the IMC sends the response to Igor.
9. If Igor is trying to receive a response, Igor will receive the response.
10. Repeat

In the code, there are two important classes that you will need to
understand to make changes to the code. These classes are the
`IgorMonoControl` class and the `Cornerstone` class. `IgorMonoControl`
is the main class that is run by the `start.bat` file. It is responsible
for creating the TCP/SOCKET connection, and passing the commands to an
instance of the `Cornerstone` class. The `Cornerstone` class is responsible
for handling the commands that are sent to it by interfacing with the
`CyUSB` DLL. The `CyUSB` DLL is a DLL that was provided by oriel to
interface with the monochromater.

When the `Cornerstone` class is instantiated, it will set a number of
public and private properties that it will use to communicate with the
monochromater. Of importance it does not set it's own copy of the state
of the monochromater. It will always query the monochromater for it's
state. This is important because every time you query the monochromater
it will atempt to parse the response from a string into a float, int
ot leave it as a string. If the response is something that this old
code does not understand, it will return a -1, and the entire system
will break. Dispite the wonders of C# and .NET, the ABI interface
with the `CyUSB` DLL is likely incompatible with the current frameworks
used by Visual Studio and .NET and is likely the source of this issue.

These is one location in the code that could be the root of the issue,
however, will require you to recompile the `Cornerstone.dll` file. You may
be able to use Visual Studio's disassembler to look at the code, and
rewrite your own implementation of the `Cornerstone` class to debug the
issue. If not, then you will need to use Microsofts `ildasm` tool to
disassemble the `Cornerstone.dll` file into a `.ilasm` assembly file.
You will be able to edit the assembly and reassemble it using the
corresponding `ilasm` tool.

In either case, you will want to look at two interactions in the code.

```c#
// Cornerstone.Dll
namespace CornerstoneDll;

public class Cornerstone{
    // Properties
    private int waitTime = 10;
    private uint dev_timeout = 500u;
}
```

Both the private properties `waitTime` and `dev_timeout` are used to
set the timeout for the monochromater. The `waitTime` property is used
to determine how many iterations the code will try to read from the
monochromater before it gives up. The `dev_timeout` property is used
to set the timeout for the monochromater. Unfortunately, increasing
the `waitTime` property will reduce the number of times the code will
try to read from the monochromater before it gives up. Increasing the
frequency of bugs. You cannot increase the `dev_timeout` property
because the property is private and there are no setter methods for
the property. You will need to recompile the `Cornerstone.dll` file
to change the `dev_timeout` property to a public property with a setter
method. This will allow you to change the timeout for the monochromater
and hopefully reduce the number of bugs.... This is not easy!
