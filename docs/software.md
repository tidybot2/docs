# Software

This page provides instructions to prepare a new mini PC for running the real robot with our [codebase](https://github.com/jimmyyhwu/tidybot2).

!!! note

    These setup steps are specific to configuring the mini PC for working with the real robot.
    If you only need to run in simulation on a dev machine, please see the [Usage](https://github.com/jimmyyhwu/tidybot2#usage) section of the codebase README.
    The full policy learning pipeline can be tested in simulation without a physical robot, including phone teleoperation, data collection, and policy inference.

## Mini PC setup

This section describes how we configured a Beelink S12 Pro mini PC as an onboard computer for running controllers.
These steps are tailored for this model but should apply to other mini PCs as well.

### Ubuntu 22

To create a bootable USB drive for Ubuntu 22, follow these steps:

1. Go the release page for [Ubuntu 22](https://releases.ubuntu.com/jammy/)
1. Scroll down and download this file: `ubuntu-22.04.4-desktop-amd64.iso`
1. Download and install [balenaEtcher](https://etcher.balena.io)
1. Use balenaEtcher to flash the ISO image onto a USB drive

!!! note

    We used Ubuntu 22.04.4 LTS.
    Newer versions should work as well but are untested and could require additional modifications.

!!! tip

    For faster installation, use a USB drive that supports USB 3.0 or higher.
    The Ubuntu installation will take much longer with a USB 2.0 drive.

Install Ubuntu 22 on the mini PC:

1. Power on the mini PC and press `<F7>` to enter the boot menu
1. Select the `UEFI USB` option to boot from the USB drive
1. Select *Install Ubuntu* when prompted
1. Connect to a Wi-Fi network when prompted
1. In the *Updates and other Software* screen, choose *Minimal installation* instead of *Normal installation*. Do not check the box for *Install third-party software*.
1. In the *Installation type* screen, select *Erase disk and install Ubuntu*
1. Continue with the installation procedure until completion

!!! tip

    If the boot menu does not appear, restart the mini PC and repeatedly press `<F7>` during the boot process.

!!! note

    We use the "minimal installation" to minimize unnecessary software, as this mini PC will be used to run performance-critical real-time controllers.

### Automatic power on

For convenience, we recommend configuring the mini PC to automatically power on when plugged in.
Follow these steps to enable this in the BIOS:

1. Reboot the mini PC and press `<Del>` to enter the BIOS setup
1. Navigate to `Chipset > PCH-IO Configuration > State After G3`
1. Change `S5 State` to `S0 State`
1. Save changes and exit
1. Turn off the mini PC and unplug the power adapter
1. Plug the power adapter back in and verify that the mini PC powers on automatically

### Headless setup

The mini PC will mainly be used in headless mode without a monitor via SSH and tmux, as it is inconvenient to keep a monitor connected to a moving mobile base.

To set up SSH and tmux:

```bash
sudo apt install openssh-server tmux
```

SSH allows you to connect to the mini PC remotely from your dev machine:

```bash
ssh <username>@<minipc-hostname>
```

Replace `<username>` and `<minipc-hostname>` as appropriate.

!!! tip

    If you are on a network with no DNS server, try appending `.local` to the hostname:

    ```bash
    ssh <username>@<minipc-hostname>.local
    ```


We use tmux to create persistent sessions that remain intact even if the SSH connection drops.
Here is a typical tmux workflow:

1. SSH into the mini PC
1. Run the command `tmux` to start a new tmux session
1. If a session already exists, run `tmux a` to reattach to it
1. Run tasks in the tmux session
1. Create new windows to run tasks in parallel

!!! tip

    If you have not used tmux before, please see the "Sessions" and "Windows" sections in the [tmux cheat sheet](https://tmuxcheatsheet.com).

!!! tip

    By default, mouse scrolling is disabled in tmux. To enable it:

    1. Open `~/.tmux.conf` in a text editor
    1. Add the line: `set -g mouse on`
    1. Save and exit
    1. Reload the config file: `tmux source ~/.tmux.conf`

### CPU frequency scaling

To minimize latency when running real-time controllers, CPU frequency scaling should be disabled.
First, install `cpufrequtils`:

```bash
sudo apt install cpufrequtils
```

Check the current CPU frequency policy:

```bash
cpufreq-info -p
```

Example output showing the `powersave` governor is active:

```
400000 4000000 powersave
700000 3400000 powersave
```

To set the CPU to use the `performance` governor (disable scaling), run:

```bash
sudo sh -c 'echo "GOVERNOR=performance" > /etc/default/cpufrequtils'
```

With this change, the default governor to will be set to `performance` upon reboot.

### Wi-Fi power management

For improved performance and latency when communicating with the mini PC over Wi-Fi, wireless interface power management should be disabled.

Check the current wireless settings:

```bash
iwconfig
```

If you see `Power Management: on`, follow these steps to disable power management:

1. Open `/etc/NetworkManager/conf.d/default-wifi-powersave-on.conf` in a text editor
1. Change `wifi.powersave` to `wifi.powersave = 2`

### Real-time kernel

To improve the real-time performance of controllers, we highly recommend installing the the `PREEMPT_RT` real-time kernel.

!!! note

    The real-time kernel is not compatible with Nvidia drivers.

**Building the real-time kernel**

Please follow the [real-time kernel setup guide](https://frankaemika.github.io/docs/installation_linux.html#setting-up-the-real-time-kernel) from the Franka documentation.
Here’s a quick summary of the steps we used:

!!! note

    If you prefer to directly install the same kernel image we built rather than building it yourself, you can download the files here:

    * [linux-image-6.8.2-rt11_6.8.2-1_amd64.deb](https://www.dropbox.com/scl/fi/tf43oizgvi69ef18fdtrn/linux-image-6.8.2-rt11_6.8.2-1_amd64.deb?rlkey=gmlsnpsq2pc1nqmwe297rsojg&st=u4xt7wfo)
    * [linux-headers-6.8.2-rt11_6.8.2-1_amd64.deb](https://www.dropbox.com/scl/fi/x6pymtd48nqpyxe2bgvsp/linux-headers-6.8.2-rt11_6.8.2-1_amd64.deb?rlkey=4u8do5syr0pkmyko9w5vyp97s&st=bknqqs69)

    To install these files, run:

    ```bash
    sudo dpkg -i linux-image-6.8.2-rt11_6.8.2-1_amd64.deb linux-headers-6.8.2-rt11_6.8.2-1_amd64.deb
    ```

    If this installs successfully, you can skip ahead to the section below on setting the real-time kernel as the default GRUB boot option.

Install the required dependencies:

```bash
sudo apt-get install build-essential bc curl ca-certificates gnupg2 libssl-dev lsb-release libelf-dev bison flex dwarves zstd libncurses-dev debhelper
```

Download the necessary source files:

```bash
mkdir linux-6.8.2-rt11
cd linux-6.8.2-rt11
curl -SLO https://mirrors.edge.kernel.org/pub/linux/kernel/v6.x/linux-6.8.2.tar.xz
curl -SLO https://mirrors.edge.kernel.org/pub/linux/kernel/v6.x/linux-6.8.2.tar.sign
curl -SLO https://mirrors.edge.kernel.org/pub/linux/kernel/projects/rt/6.8/patch-6.8.2-rt11.patch.xz
curl -SLO https://mirrors.edge.kernel.org/pub/linux/kernel/projects/rt/6.5/patch-6.8.2-rt11.patch.sign
```

!!! note

    We recommend installing the exact `PREEMPT_RT` version we used.
    Other versions may have issues such as broken Wi-Fi or Ethernet connectivity.

Extract and patch the kernel:

```bash
xz -d *.xz
tar xf linux-*.tar
cd linux-*/
patch -p1 < ../patch-*.patch
cp -v /boot/config-$(uname -r) .config
make olddefconfig
make menuconfig
```

In the menu interface that opens, apply the following changes and save before exiting:

* Under *General setup > Preemption Model*, select *Fully Preemptible Kernel (Real-Time)*
* Under *Cryptographic API > Certificates for signature checking > Additional X.509 keys for default system keyring*, remove `"debian/canonical-certs.pem"`
* Under *Cryptographic API > Certificates for signature checking > X.509 certificates to be preloaded into the system blacklist keyring*, remove `"debian/canonical-revoked-certs.pem"`
* Under *Device Drivers > Graphics support > Intel 8xx/9xx/G3x/G4x/HD Graphics*, press `<N>` to exclude (this option addresses i915 latency spikes in `cyclictest`)

To build the kernel, run:

```bash
make -j$(nproc) bindeb-pkg
```

!!! note

    Building the kernel in parallel may hide error messages.
    For clearer output, you can build sequentially:

    ```bash
    make bindeb-pkg
    ```

    However, note that the sequential build can take up to 4 hours.

Once the kernel has been built, install it with:

```bash
sudo dpkg -i linux-image-6.8.2-rt11_6.8.2-1_amd64.deb linux-headers-6.8.2-rt11_6.8.2-1_amd64.deb
```

!!! note

    After installing this kernel, if you plug in a monitor after the mini PC has booted, it will not display anything.
    To use the monitor, plug it in **before** booting the mini PC.
    You can restore normal monitor behavior by building a kernel that skips the Intel graphics option above (but it will impact latency performance).

**Setting real-time kernel as default boot option**

To set the new real-time kernel as the default boot option, follow these steps:

1. Open the `/etc/default/grub` file in a text editor
1. Change `GRUB_DEFAULT=0` to `GRUB_DEFAULT="Advanced options for Ubuntu>Ubuntu, with Linux 6.8.2-rt11"`
1. Run this command to update GRUB: `sudo update-grub`

!!! note

    If you are using a different kernel version, modify the text to match your kernel version.
    You can check `/boot/grub/grub.cfg` for the correct identifier of the real-time kernel menu entry.

!!! note

    There are no spaces around the `>` symbol in the `GRUB_DEFAULT` entry.

### Verification

Reboot the mini PC and perform the following verifications:

**CPU frequency scaling**

To verify that CPU frequency scaling is set to `performance`, run this command:

```bash
cpufreq-info -p
```

You should see output like this, indicating that frequency scaling is disabled:

```
700000 3400000 performance
```

**Wi-Fi power management**

To check if Wi-Fi power management is disabled, run:

```bash
iwconfig
```

Verify that the output reads:

```
Power Management:off
```

You can also ping the mini PC from your dev machine and verify that the ping times are around 1-3 ms.
With power management enabled, ping times are typically much higher (40-100 ms).

**Real-time kernel**

To confirm that the real-time kernel is in use, run:

```bash
uname -a
```

The output should include the `PREEMPT_RT` label.
For example:

```
Linux iprl-minipc-01 6.8.2-rt11 #1 SMP PREEMPT_RT Sun Oct 27 18:09:44 PDT 2024 x86_64 x86_64 x86_64 GNU/Linux
```

If the kernel name ends in `-generic`, it means the system is still using the generic kernel, not the real-time one:

```
Linux iprl-minipc-01 6.8.0-48-generic #48~22.04.1-Ubuntu SMP PREEMPT_DYNAMIC Mon Oct  7 11:24:13 UTC 2 x86_64 x86_64 x86_64 GNU/Linux
```

**Latency testing**

Once the real-time kernel is confirmed to be in use, run a latency test with `cyclictest` to measure system performance for 1 hour.

First, install the `rt-tests` package:

```bash
sudo apt install rt-tests
```

Next, run the following command to measure the real-time latency performance of your system:

```bash
sudo cyclictest --mlockall --smp --priority=80 --interval=200 --distance=0 --duration=1h
```

This test will capture latency statistics across all CPU cores for 1 hour.
In our tests using the Beelink S12 Pro, the generic kernel showed significantly higher latencies:

*  `6.8.2-rt11` - < 20 us (microseconds)
* `6.8.0-48-generic` - 1000+ us

!!! note

    We recommend unplugging unnecessary peripherals such as monitors during the test as they can cause latency spikes.
    We conducted our tests over an SSH connection inside a tmux session.

### Arm setup

Follow the steps below to configure the mini PC for controlling your robotic arm.

=== "Kinova"

    **Ethernet connection**

    To establish an Ethernet connection to the Kinova arm, run this command:

    ```bash
    sudo nmcli con add type ethernet con-name Kinova ifname <ethernet-interface> ipv4.method manual ipv4.addresses 192.168.1.11/24
    ```

    Replace `<ethernet-interface>` with your actual Ethernet interface name, which you can find by running `ip link`.

    Then, verify that the `Kinova` connection is listed when running:

    ```bash
    nmcli con
    ```

    Activate the connection:

    ```bash
    sudo nmcli con up Kinova
    ```

    Make sure the arm is powered on and test the connection by pinging the arm:

    ```bash
    ping 192.168.1.10
    ```

    **Communication test**

    To evaluate network communication with the arm, run the following ping test for 100 seconds:

    ```bash
    sudo ping 192.168.1.10 -i 0.001 -D -c 100000 -s 1200
    ```

    Here is some example output:

    ```
    100000 packets transmitted, 100000 received, 0% packet loss, time 99999ms
    rtt min/avg/max/mdev = 0.143/0.252/0.495/0.059 ms
    ```

    The result should show 0% packet loss and round-trip times (RTT) well under 1 ms.

=== "ARX5"

    **CAN setup**

    Follow the setup instructions in Yihuai Gao's [ARX5 SDK](https://github.com/real-stanford/arx5-sdk) to set up the USB-to-CAN adapter for your ARX5 arm.
    The only modification required is to replace all instances of `can0` with `can1`, as the `can0` interface is already used by the mobile base.

## Codebase setup

This section describes the steps required to set up the [codebase](https://github.com/jimmyyhwu/tidybot2) on the mini PC for use with the real robot.

### Real-time priority

The low-level controllers should run with real-time process priority to ensure optimal performance and minimal latency.

Create a file at `/etc/security/limits.d/99-realtime.conf` with the following contents:

```
@realtime soft rtprio 99
@realtime hard rtprio 99
```

This configuration allows members of the `realtime` group to set real-time priority for processes.

Next, create the `realtime` group (if it doesn’t already exist) and add the current user to it:

```bash
sudo addgroup realtime
sudo usermod -aG realtime $USER
```

!!! note

    These new permissions will take effect only after logging out and logging back in.

### CANivore

To communicate with the drive system, we need to enable SocketCAN support for the CANivore USB-to-CAN adapter.

First, add the CTR Electronics package repository:

```bash
export YEAR=2024
sudo curl -s --compressed -o /usr/share/keyrings/ctr-pubkey.gpg "https://deb.ctr-electronics.com/ctr-pubkey.gpg"
sudo curl -s --compressed -o /etc/apt/sources.list.d/ctr${YEAR}.list "https://deb.ctr-electronics.com/ctr${YEAR}.list"
```

Then, install the `canivore-usb` package:

```bash
sudo apt update
sudo apt install canivore-usb
```

!!! note

    More details are available in the official [CANivore documentation](https://v6.docs.ctr-electronics.com/en/stable/docs/installation/installation-nonfrc.html#canivore-installation).

### Gamepad

To enable gamepad input in headless sessions, add the current user to the `input` group:

```bash
sudo usermod -aG input $USER
```

This grants permission to read gamepad input events without requiring root access.

!!! note

    The new permission will take effect only after logging out and logging back in.

To confirm that the gamepad is working, install and run `jstest`:

```
sudo apt install joystick
jstest /dev/input/js0
```

Here is some example `jstest` output:

```
Driver version is 2.1.0.
Joystick (Logitech Gamepad F710) has 8 axes (X, Y, Z, Rx, Ry, Rz, Hat0X, Hat0Y)
and 11 buttons (BtnA, BtnB, BtnX, BtnY, BtnTL, BtnTR, BtnSelect, BtnStart, BtnMode, BtnThumbL, BtnThumbR).
Testing ... (interrupt to exit)
Axes:  0:     0  1:    -2  2:-32767  3:     0  4:    -2  5:-32767  6:     0  7:     0 Buttons:  0:off  1:off  2:off  3:off  4:off  5:off  6:off  7:off  8:off  9:off 10:off

```

!!! tip

    If you have a graphical display connected, you can use `jstest-gtk` instead of `jstest`.

### Logitech camera

If you will be using a Logitech camera (as a base or wrist camera), follow these steps to enable camera access in headless sessions.

Create a file at `/etc/udev/rules.d/99-webcam.rules` with the following contents:

```
# Logitech C930e
SUBSYSTEMS=="usb", ATTRS{idVendor}=="046d", ATTRS{idProduct}=="0843", MODE="666"
```

Then, run the following commands to apply the new udev rule:

```bash
sudo udevadm control --reload-rules
sudo service udev restart
sudo udevadm trigger
```

### Mamba environment

Please install Mamba following the [official instructions](https://mamba.readthedocs.io/en/latest/installation/mamba-installation.html).
We use the Miniforge distribution.

Run the following commands to set up the Mamba environment:

```bash
git clone https://github.com/jimmyyhwu/tidybot2.git
cd ~/tidybot2
mamba create -n tidybot2 python=3.10.14
mamba activate tidybot2
pip install -r requirements.txt
```

### Constants

There are a few constants specific to your hardware that need to be set in the codebase within the [`constants.py`](https://github.com/jimmyyhwu/tidybot2/blob/main/constants.py) file.

**Encoder offsets**

Each mobile base has a unique set of encoder offsets, which are used by the low-level controller to determine the absolute steer position of each caster.

Before setting the offsets, follow these steps to prepare the base:

1. Double-check that all components inside the mobile base, including the camping battery and SLA battery, are fully secured
1. Ensure that all cables are properly secured and kept away from the caster module gears
1. Turn the mobile base onto its side, with the right side of the base facing up

The zero steer position is the configuration that the base assumes when driving forward, with all casters in the trailing position:

![](images/integration/IMG_6863.jpg){ width="49.45%" }

!!! note

    In these photos, the robot's forward (+x) direction is towards the right side.

Use a straightedge to align the top wheels so that they are both in the zero position:

![](images/integration/IMG_8733.jpg){ width="49.45%" }

Repeat this process for the bottom two wheels as well.

!!! note

    The [wheel alignment jig](https://github.com/jimmyyhwu/tidybot2-resources/blob/main/3D%20Printing/Wheel%20Alignment%20Jig.stl) helps create a level surface on each wheel:

    ![](images/integration/IMG_8730.jpg){ width="49.45%" }

Once all four casters are in the zero steer position, run the following Python code snippet to print out the `ENCODER_MAGNET_OFFSETS` value you should use:

```python
from base_controller import Vehicle
vehicle = Vehicle()
vehicle.get_encoder_offsets()
```

Update the `ENCODER_MAGNET_OFFSETS` value in `constants.py` with the printed value.

!!! tip

    If you find later on that your base odometry is not accurate, you can improve performance by fine-tuning the encoder offsets in increments of 1/4096. Inaccurate odometry can cause the robot, when commanded to go straight, to take a curved path or rotate slightly.

**Base dimensions**

The constants `h_x` and `h_y` in `constants.py` specify the distance from the mobile base center to each caster steer axis in the `x` and `y` directions.
If you are using one of our reference designs, you can just uncomment the appropriate line in `constants.py`.

For the larger Kinova or Franka designs:

```python
h_x, h_y = 0.190150 * np.array([1.0, 1.0, -1.0, -1.0]), 0.170150 * np.array([-1.0, 1.0, 1.0, -1.0])  # Kinova / Franka
```

For the smaller ARX5 design:

```python
h_x, h_y = 0.140150 * np.array([1.0, 1.0, -1.0, -1.0]), 0.120150 * np.array([-1.0, 1.0, 1.0, -1.0])  # ARX5
```

If you are using a custom frame size, please modify these values to match your robot's measurements.

**Logitech camera**

If you are using a Logitech camera, you need to specify its serial number in `constants.py`.
Retrieve the camera's serial number using either of the following commands:

```bash
lsusb -v -d 046d:0843 | grep iSerial
udevadm info -n video0 | grep ID_SERIAL_SHORT
```

Modify the `BASE_CAMERA_SERIAL` value in `constants.py` to match your camera's serial number.

### Arm setup

=== "Kinova"

    If you are using the Kinova Gen3 arm with the codebase, follow the additional setup steps below:

    **Kortex API**

    To communicate with the arm from Python, please install the Kortex Python API, which can be downloaded from:

    * [Kinova Artifactory](https://artifactory.kinovaapps.com/ui/repos/tree/General/generic-public/kortex/API/2.6.0/kortex_api-2.6.0.post3-py3-none-any.whl)
    * [`kortex_api-2.6.0.post3-py3-none-any.whl`](https://www.dropbox.com/scl/fi/behem9thid9benovogfdf/kortex_api-2.6.0.post3-py3-none-any.whl?rlkey=13zikoe4cwtk59q86jfx3a2k5&dl=1)

    Run the following commands to install:

    ```bash
    pip install kortex_api-2.6.0.post3-py3-none-any.whl
    pip install protobuf==3.20.0  # kortex_api specifies protobuf==3.5.1 which is outdated
    ```

    **Wrist camera**

    If you want to read images from the Kinova wrist camera in Python, you need to install OpenCV with GStreamer support.

    First, install [GStreamer](https://gstreamer.freedesktop.org/documentation/installing/on-linux.html):

    ```bash
    sudo apt install libgstreamer1.0-dev libgstreamer-plugins-base1.0-dev libgstreamer-plugins-bad1.0-dev gstreamer1.0-plugins-base gstreamer1.0-plugins-good gstreamer1.0-plugins-bad gstreamer1.0-plugins-ugly gstreamer1.0-libav gstreamer1.0-tools gstreamer1.0-x gstreamer1.0-alsa gstreamer1.0-gl gstreamer1.0-gtk3 gstreamer1.0-qt5 gstreamer1.0-pulseaudio
    ```

    If you are using the same mini PC as us, you can download our pre-built OpenCV wheel:

    * [`opencv_python-4.9.0.80-cp310-cp310-linux_x86_64.whl`](https://www.dropbox.com/scl/fi/9gr7u1dw3ef5x73ppkpxi/opencv_python-4.9.0.80-cp310-cp310-linux_x86_64.whl?rlkey=8bh4tl7is0ixp69zm9ohko0yb&st=5prf2cxo)

    Otherwise, please expand the section below to build the wheel yourself:

    ??? "Building OpenCV with GStreamer support"

        Install OpenCV [build dependencies](https://docs.opencv.org/4.9.0/d2/de6/tutorial_py_setup_in_ubuntu.html):

        ```bash
        sudo apt install python3-dev python3-numpy libavcodec-dev libavformat-dev libswscale-dev libgstreamer-plugins-base1.0-dev libgstreamer1.0-dev libgtk-3-dev
        ```

        Build OpenCV from source with GStreamer support:

        ```bash
        mamba create -n opencv python=3.10.14
        pip install scikit-build==0.17.6 numpy==1.26.4
        git clone --recursive https://github.com/opencv/opencv-python.git
        git checkout tags/80
        export CMAKE_ARGS="-DWITH_GSTREAMER=ON"
        pip wheel . --verbose
        ```

        After building, you should end up with a Python wheel file called `opencv_python-4.9.0.80-cp310-cp310-linux_x86_64.whl`.

    Next, install the OpenCV Wheel:

    ```bash
    pip install --force-reinstall --no-deps opencv_python-4.9.0.80-cp310-cp310-linux_x86_64.whl
    ```

    To verify that the installed OpenCV has GStreamer support, run the following command:

    ```bash
    python -c "import cv2 as cv; print(cv.getBuildInformation())" | grep GStreamer
    ```

    You should see the following line in the output:

    ```
        GStreamer:                   YES (1.20.3)
    ```

    **Torque offsets**

    Before running torque control on the Kinova arm, it is a good idea to zero the torque offsets.
    First, make sure the surrounding area is clear, as the arm may sweep across a large space while moving to the zero configuration.
    Next, run the following Python code snippet and follow the terminal prompts:

    ```python
    from kinova import TorqueControlledArm

    arm = TorqueControlledArm()
    arm.zero_torque_offsets()
    arm.disconnect()
    ```

=== "ARX5"

    **ARX5 controller**

    To control the ARX5 arm, we use Yihuai Gao's [ARX5 SDK](https://github.com/real-stanford/arx5-sdk).

    Start by cloning the `arx5` branch of our codebase, which adds support for the ARX5 arm:

    ```bash
    git clone https://github.com/jimmyyhwu/tidybot2.git
    cd ~/tidybot2
    git fetch origin arx5  # Branch for ARX5 arm
    git checkout arx5
    ```

    Next, install the `arx5-sdk` package within the `tidybot2` directory:

    ```bash
    cd ~/tidybot2
    git clone https://github.com/real-stanford/arx5-sdk.git
    cd arx5-sdk
    git checkout d046707  # Last tested commit
    mamba env create -f conda_environments/py310_environment.yaml
    pip install mujoco==3.2.4 scipy==1.12.0
    mamba activate arx-py310
    mkdir build && cd build
    cmake .. && make -j
    ```

    These modifications enable the ARX5 arm to be used following the same steps outlined in the [Usage](usage.md) page.

    !!! note

        Be sure to activate the `arx-py310` Mamba environment in the terminal session where you run `arm_server.py`.
