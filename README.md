# install-XfreeRDP-remote-desktop-Ubuntu
**_instruction: I remote desktop Jetson nano (running Ubuntu 20.04) on laptop Ubuntu_**

## remove all XRDP and xfreeRDP on your Ubuntu
```
# Remove xrdp and xfreerdp
sudo apt-get remove --purge xrdp xfreerdp -y
sudo apt-get autoremove -y
sudo apt-get autoclean

# Verify that no files remain
sudo rm -rf /etc/xrdp
sudo rm -rf /usr/share/xrdp
sudo rm -rf /usr/bin/xfreerdp

# Check for configuration leftovers
sudo find ~ -name "*.freerdp" -exec rm -f {} \;
```

## Manually Install XFreeRDP
Step A: Install Dependencies
```
sudo apt-get install build-essential cmake git libssl-dev libx11-dev libxext-dev libxinerama-dev libxcursor-dev libxkbfile-dev libxrandr-dev libxi-dev libxrender-dev

```
Step B: Clone the Repository
```
git clone https://github.com/FreeRDP/FreeRDP.git
cd FreeRDP
git checkout stable-2.0
```

Step C: Build and Install
```
mkdir build
cd build
cmake ..
make -j$(nproc)
sudo make install
```

*** **If it has some errors like:**__ 
```
_PULSE_LIBRARY (ADVANCED)
    linked by target "rdpsnd-client-pulse" in directory /home/jetson/FreeRDP/channels/rdpsnd/client/pulse
    linked by target "audin-client-pulse" in directory /home/jetson/FreeRDP/channels/audin/client/pulse

-- Configuring incomplete, errors occurred!
See also "/home/jetson/FreeRDP/build/CMakeFiles/CMakeOutput.log".
See also "/home/jetson/FreeRDP/build/CMakeFiles/CMakeError.log"._
```
**_OR:_**
```
_jetson@nano:~/FreeRDP/build$ make -j$(nproc)
make: *** No targets specified and no makefile found.  Stop.

jetson@nano:~/FreeRDP/build$ sudo make install
make: *** No rule to make target 'install'.  Stop.
jetson@nano:~/FreeRDP/build$ xfreerdp --version

Command 'xfreerdp' not found, but can be installed with:

sudo apt install freerdp2-x11_
```
=> **_DO IT:_**

**Install Missing Dependencies:**

```
sudo apt-get update
sudo apt-get install -y build-essential cmake git libssl-dev libx11-dev libxext-dev \
    libxinerama-dev libxcursor-dev libxkbfile-dev libxrandr-dev libxi-dev libxrender-dev \
    libavutil-dev libavcodec-dev libpulse-dev libjpeg-dev libusb-1.0-0-dev \
    libgstreamer1.0-dev libgstreamer-plugins-base1.0-dev libwayland-dev
```

**Clean and Retry Configuration:**

```
cd ~/FreeRDP
rm -rf build
mkdir build
cd build
```
**then: **
```
cmake ..
```
**Compile and Install:**
```
make -j$(nproc)
sudo make install
sudo ldconfig
```

## Then install XRDP (base for XfreeRDP can works):

**Reinstall xrdp:**
```
sudo apt-get purge xrdp -y
sudo apt-get install xrdp -y
```

**_After reinstalling, check for the existence of the required files:_**
```
ls /etc/xrdp/xrdp.ini
ls /usr/share/xrdp/
```

**Restart xrdp**
**_After completing the above steps, restart the xrdp service:_**
```
sudo systemctl restart xrdp
```

**_Verify that the service is running without errors:_**

```
sudo systemctl status xrdp
```

**_result should like this:_**
![image](https://github.com/user-attachments/assets/911b8af6-0b10-48e4-9b0a-e8ad90674d8b)

## install freerdp2-shadow-x11:
```
sudo apt install freerdp2-x11 freerdp2-shadow-x11
```

## install xfce4: 
```
sudo apt install xfce4 xfce4-goodies
```

**_Set Xfce as the default session:_**
```
echo "startxfce4" > ~/.xsession
```

**_Then restart xrdp:_**
```
sudo systemctl restart xrdp
```

## fix RDP session start: 
```
sudo nano /etc/xrdp/startwm.sh
```

**_then modify this file:_**

```
#!/bin/sh
# xrdp X session start script (c) 2015, 2017 mirabilos
# published under The MirOS Licence

if test -r /etc/profile; then
    . /etc/profile
fi

if test -r /etc/default/locale; then
    . /etc/default/locale
    test -z "${LANG+x}" || export LANG
    test -z "${LANGUAGE+x}" || export LANGUAGE
    test -z "${LC_ADDRESS+x}" || export LC_ADDRESS
    test -z "${LC_ALL+x}" || export LC_ALL
    test -z "${LC_COLLATE+x}" || export LC_COLLATE
    test -z "${LC_CTYPE+x}" || export LC_CTYPE
    test -z "${LC_IDENTIFICATION+x}" || export LC_IDENTIFICATION
    test -z "${LC_MEASUREMENT+x}" || export LC_MEASUREMENT
    test -z "${LC_MESSAGES+x}" || export LC_MESSAGES
    test -z "${LC_MONETARY+x}" || export LC_MONETARY
    test -z "${LC_NAME+x}" || export LC_NAME
    test -z "${LC_NUMERIC+x}" || export LC_NUMERIC
    test -z "${LC_PAPER+x}" || export LC_PAPER
    test -z "${LC_TELEPHONE+x}" || export LC_TELEPHONE
    test -z "${LC_TIME+x}" || export LC_TIME
    test -z "${LOCPATH+x}" || export LOCPATH
fi

# Start Xfce session
startxfce4

```

**_then:_**
```
sudo chmod +x $(which xfce4-terminal)
```

**_Then restart xrdp:_**

```
sudo systemctl restart xrdp
```

**_then restart system:_**

```
sudo reboot
```

## ok, now start rdp:

**_COMMAND LINE SYSTAX:_**
```
xfreerdp /v<your jestson nano ip> /u:<jet son name> /size:1280x720
```

**_example:_**
```
xfreerdp /v:192.168.1.13 /u:jetson /size:1280x720
```
**_option size display:_**

_to use size 1280x720:_
```
... /size:1280x720
```
_to use full screen:_
```
... /f
```


**_here is mine:_**
![image](https://github.com/user-attachments/assets/6033dae1-821f-4353-a46a-f4b9543a3505)

**then, **
![image](https://github.com/user-attachments/assets/b2352f58-81d3-4379-9ad8-9a662ed9cbff)

**_now RDP is on, but we can't open Terminal, so we do:_**

> right click on **```Terminal button```**
>![image](https://github.com/user-attachments/assets/0af5231d-c8b2-4762-976d-f3bc0a164498)

**=> ```Properties```**

![image](https://github.com/user-attachments/assets/d85a2215-ec51-4c38-a49c-6bd1a68d850f)

> right click on **blue "```+```" button**
>>>>>> ![image](https://github.com/user-attachments/assets/1f36c319-607a-4f3c-a9cb-a26eec4a0bcc)

> then **add "```+```" button**
> >>>>>>![image](https://github.com/user-attachments/assets/ecaf8b6b-c928-4e84-93f4-4faa4eac2713)

> then **move to up** and then **close**
>>>>>>>> ![image](https://github.com/user-attachments/assets/76d84c72-eb2f-4a9a-a785-d07217074f90)

_Now left click on Terminal button and it works:_

![image](https://github.com/user-attachments/assets/741244b1-36fe-4633-aa28-ef88c12e4b88)

## SUMMARY: xfreeRDP works pretty fast and can temporarily replace HDMI screen
