# Software

This page provides instructions to prepare a new mini PC for running real-time controllers and communicating with the robotic arm.
After completing the steps below, follow the setup instructions in the [codebase](https://github.com/jimmyyhwu/tidybot2) for further setup.

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

## Arm setup

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

    **Wrist camera setup**

    To access the Kinova wrist camera stream using OpenCV in Python, we need to install OpenCV with GStreamer support.

    First, install [GStreamer](https://gstreamer.freedesktop.org/documentation/installing/on-linux.html):

    ```bash
    sudo apt install libgstreamer1.0-dev libgstreamer-plugins-base1.0-dev libgstreamer-plugins-bad1.0-dev gstreamer1.0-plugins-base gstreamer1.0-plugins-good gstreamer1.0-plugins-bad gstreamer1.0-plugins-ugly gstreamer1.0-libav gstreamer1.0-tools gstreamer1.0-x gstreamer1.0-alsa gstreamer1.0-gl gstreamer1.0-gtk3 gstreamer1.0-qt5 gstreamer1.0-pulseaudio
    ```

    Install OpenCV [build dependencies](https://docs.opencv.org/4.9.0/d2/de6/tutorial_py_setup_in_ubuntu.html):

    ```bash
    sudo apt install python3-dev python3-numpy libavcodec-dev libavformat-dev libswscale-dev libgstreamer-plugins-base1.0-dev libgstreamer1.0-dev libgtk-3-dev
    ```

    If you are using the same mini PC as us, you can download our pre-built wheel:

    * [opencv_python-4.9.0.80-cp310-cp310-linux_x86_64.whl](https://www.dropbox.com/scl/fi/9gr7u1dw3ef5x73ppkpxi/opencv_python-4.9.0.80-cp310-cp310-linux_x86_64.whl?rlkey=8bh4tl7is0ixp69zm9ohko0yb&st=5prf2cxo)

    Otherwise, to build the wheel yourself, follow these steps to build from source with GStreamer support:

    ```bash
    mamba create -n opencv python=3.10.14
    pip install scikit-build==0.17.6 numpy==1.26.4
    git clone --recursive https://github.com/opencv/opencv-python.git
    git checkout tags/80
    export CMAKE_ARGS="-DWITH_GSTREAMER=ON"
    pip wheel . --verbose
    ```

    After building, you should end up with a Python wheel file called `opencv_python-4.9.0.80-cp310-cp310-linux_x86_64.whl`.

=== "ARX5"

    Follow the setup instructions in Yihuai Gao's [ARX5 SDK](https://github.com/real-stanford/arx5-sdk) to set up your ARX5 arm.
    The only modification required is to replace all instances of `can0` with `can1`, as the `can0` interface is already used by the mobile base.
