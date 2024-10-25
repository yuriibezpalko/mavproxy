# Installation Guide

This guide provides step-by-step instructions to set up your remote Linux machine. You will:

1. Update and upgrade system packages.
2. Install ZeroTier and join a specified network.
3. Create a Python virtual environment.
4. Install **MAVProxy** within the virtual environment.
5. Establish a connection between Raspberry Pi and Pixhawk and GQC
---

## Prerequisites

- **SSH Access**: You need SSH access to the remote machine.
- **Sudo Privileges**: The user should have sudo privileges.
- **ZeroTier Network ID**: Obtain your ZeroTier network ID from [ZeroTier Central](https://my.zerotier.com/).
- **Raspberry Pi 4**: You need a Raspberry Pi 4 running Raspberry Pi OS. You can obtain it from [Raspberry Pi OS](https://www.raspberrypi.com/software/).
- **QGC**: GQC installed on your machine. Get it from [QGC Guide](https://docs.qgroundcontrol.com/master/en/qgc-user-guide/getting_started/download_and_install.html).
- **Pixhawk**
---

## Steps

### 1. Update System Packages

Update the package list and upgrade all installed packages to the latest version.

```bash
sudo apt update && sudo apt upgrade -y
```

### 2. Install Essential Packages

Install Python 3, `venv` module, `pip`, and `curl`.

```bash
sudo apt install -y python3 python3-venv python3-pip curl
```

### 3. Install ZeroTier
Import ZeroTier GPG Key

```bash
curl -s https://download.zerotier.com/contact%40zerotier.com.gpg | sudo apt-key add -
```

Add ZeroTier Repository
```bash
echo "deb https://download.zerotier.com/debian/buster buster main" | sudo tee /etc/apt/sources.list.d/zerotier.list
```
Update Package List
```bash
sudo apt update
```
Install ZeroTier Package
```bash
sudo apt install -y zerotier-one
```

Start and Enable ZeroTier Service
```bash
sudo systemctl start zerotier-one
sudo systemctl enable zerotier-one
```
Join ZeroTier Network
Replace `<YOUR_ZERO_TIER_NETWORK_ID>` with your actual ZeroTier network ID.
```bash
sudo zerotier-cli join <YOUR_ZERO_TIER_NETWORK_ID>
```

**Note:** You may need to authorize the device in your ZeroTier Central dashboard after joining.

### 4. Create Python Virtual Environment

Create Directory for Virtual Environment
Replace `<USERNAME>` with your actual username.
```bash
mkdir -p /home/<USERNAME>/mavproxy_env
```

Create Virtual Environment
```bash
python3 -m venv /home/<USERNAME>/mavproxy_env
```

### 5. Activate Virtual Environment

```bash
source /home/<USERNAME>/mavproxy_env/bin/activate
```

### 6. Upgrade `pip` in Virtual Environment
```bash
pip install --upgrade pip
```

### 7. Install MAVProxy in Virtual Environment
```bash
pip install mavproxy
```

### Notes
-   **Replace Placeholders**: Ensure you replace `<YOUR_ZERO_TIER_NETWORK_ID>` and `<USERNAME>` with your actual network ID and username.
-   **ZeroTier Authorization**: After joining the ZeroTier network, log in to ZeroTier Central to authorize the new device if necessary.
-   **System Reboot**: A reboot is not typically necessary, but if you encounter issues, consider rebooting the machine.
-   **Python Packages**: You can install additional Python packages within the virtual environment using `pip install <package_name>`.

### Verification
To verify that everything is set up correctly:

#### Check ZeroTier Status
```bash
sudo zerotier-cli info
```
##### Expected Output:

```php
200 info <node_id> ONLINE
```

#### List Joined ZeroTier Networks
```bash
sudo zerotier-cli listnetworks
```

##### Expected Output:
```php
200 listnetworks <network_id> <status> ...
```
#### Verify MAVProxy Installation
Activate the virtual environment if not already active:
```bash
source /home/<USERNAME>/mavproxy_env/bin/activate
```

Check the MAVProxy version:
```bash
mavproxy.py --version
```

### Establish connection

#### 1. Connect Pixhawk with Raspberry Pi 4 via usb cable

#### 2. Starting MAVProxy
Replace `<serial_port>` and `<your_ip_address from zerotier>` with your device's serial port and your IP address.
```bash
mavproxy.py --master=/dev/<serial_port> --out=udpout:<your_ip_address>:14550
```

##### Example:
```bash
mavproxy.py --master=/dev/ttyACM0 --out=udpout:192.168.1.10:14550
```


##### Example Output:

```bash
WARNING: You should uninstall ModemManager as it conflicts with APM and Pixhawk
Connect /dev/ttyACM0 source_system=255
Failed to load module: No module named 'cmdlong'. Use 'set moddebug 3' in the MAVProxy console to enable traceback
Failed to load module: No module named 'terrain'. Use 'set moddebug 3' in the MAVProxy console to enable traceback
Log Directory:
Telemetry log: mav.tlog
Waiting for heartbeat from /dev/ttyACM0
MAV> Detected vehicle 1:1 on link 0
online system 1
MANUAL> Mode MANUAL
Got COMMAND_ACK: REQUEST_MESSAGE: ACCEPTED
Got COMMAND_ACK: SET_MESSAGE_INTERVAL: ACCEPTED
Got COMMAND_ACK: REQUEST_MESSAGE: FAILED
AP: ArduPlane V4.5.4 (e8f937aa)
AP: ChibiOS: 6a85082c
AP: fmuv3 002A0019 51305002 2039324B
AP: IOMCU: 410 2003 412FC231
AP: RCOut: PWM:1-14
AP: IMU0: fast sampling enabled 8.0kHz/1.0kHz
Received 1247 parameters (ftp)
Saved 1247 parameters to mav.parm
Flight battery 100 percent
AP: EKF3 waiting for GPS config data
Got COMMAND_ACK: REQUEST_CAMERA_INFORMATION: UNSUPPORTED
Got COMMAND_ACK: REQUEST_MESSAGE: FAILED
FTP reply for mavlink component 190
AP: ArduPlane V4.5.4 (e8f937aa)
AP: ChibiOS: 6a85082c
AP: fmuv3 002A0019 51305002 2039324B
AP: IOMCU: 410 2003 412FC231
AP: RCOut: PWM:1-14
AP: IMU0: fast sampling enabled 8.0kHz/1.0kHz
Got COMMAND_ACK: REQUEST_CAMERA_INFORMATION: UNSUPPORTED
Got COMMAND_ACK: REQUEST_CAMERA_INFORMATION: UNSUPPORTED
Got COMMAND_ACK: REQUEST_MESSAGE: ACCEPTED
Got COMMAND_ACK: REQUEST_MESSAGE: ACCEPTED
AP: PreArm: Waiting for RC
...
```

#### 4. Check QGC
![enter image description here](https://github.com/yuriibezpalko/mavproxy/blob/main/QGC.png?raw=true)
