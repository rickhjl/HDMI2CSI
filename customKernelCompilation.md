# Custom Kernel Compilation

In order to run the drivers, a Jetson TX1 or TX2 development board is required. Ensure that it is flashed with the L4T version corresponding to the branch used (28.1 or 28.2.1). You can use Jetpack or do it by hand as described in the Nvidia documentation.

It is necessary to replace the Kernel, Modules and device tree with those from this repository.

## Pre-built Kernel Images
The pre-built Kernel image contains the Linux Kernel (`/boot/...`), the Device Tree (`/...` or `/boot/...`) and the modules (`/lib/modules/...`). Note that flashing the device is still necessary.

For 28.1 branch:
* [TX1 Kernel (g878fbe7)](https://drive.switch.ch/index.php/s/HXzr1NfkEbqQWPX)
* [TX2 Kernel (g878fbe7)](https://drive.switch.ch/index.php/s/d18AeSp6yi6xKG2)

For 28.2.1 branch:
* TX1 Kernel: Not available
* [TX2 Kernel (gf64be34)](https://github.com/InES-HPMM/linux-l4t-4.4/releases/download/l4t-r28.2.1-v2/l4t_r28_2_1_v2_kernel_tx2_gf64be34.tar.gz)

## Kernel Compilation

Go to the kernel-4.4 directory:
* `cd kernel/kernel-4.4`

Set the environment variables:
* `export ARCH=arm64`
* `export CROSS_COMPILE=<aarch64_toolchain_prefix>`    
(e.g. `export CROSS_COMPILE=/opt/gcc4.8.5/install/bin/aarch64-unknown-linux-gnu-`)

Create the kernel config file for the HDMI2CSI module:
* **TX1**: `make tegra21_hdmi2csi_defconfig`
* **TX2**: `make tegra18_hdmi2csi_defconfig`


A good Wiki for compiling TX1 / TX2 source code (Linux4Tegra) is available at:
* **TX1**: http://developer.ridgerun.com/wiki/index.php?title=Compiling_Tegra_X1_source_code
* **TX2**: http://developer.ridgerun.com/wiki/index.php?title=Compiling_Tegra_X2_source_code

In the end the following files need to be built:
* Kernel Image (`make Image`)
* Kernel modules (`make modules`) **Note**: The TC358840 driver is new included in the Linux kernel
* Device Tree Blob (`make dtbs`)
  * TX1: tegra210-jetson-tx1-p2597-2180-a01-devkit-hdmi2csi.dtb
  * TX2: tegra186-quill-p3310-1000-c03-00-base-hdmi2csi.dtb

## Replace / Install new Kernel Files
After building is complete, the Kernel image, modules and device tree blob need to be replaced on the TX1 / TX2. 
The files are copied over network using SSH since `ums` in U-Boot is only available on TX1 but not for TX2.

### Kernel and Modules
**Preparation**:
* Create a temporary folder, e.g. : `$ mkdir ~/tmp`
* Change into the kernel directory: `$ cd kernel/kernel-4.4`
* Install the modules into the temporary folder: `$ INSTALL_MOD_PATH=~/tmp/ make modules_install`
* Create directory for kernel image: `$ mkdir ~/tmp/boot`
* Copy kernel image: `$ cp arch/arm64/boot/Image ~/tmp/boot/Image-hdmi2csi`

**Copy**:
* Start TX1 / TX2
* Synchronize with files from host: `$ sudo rsync -rlptDv HOST_USER@HOST_PC:/home/HOST_USER/tmp/ /`

**Extend Extlinux File** (do only once):
* Stay on TX1 / TX2
* In `/boot/extlinux/extlinux.conf`
  * Add `hdmi2csi` entry
  * Change `DEFAULT` to `hdmi2csi`

```
TIMEOUT 30
DEFAULT hdmi2csi

MENU TITLE p2371-2180 eMMC boot options

LABEL primary
      MENU LABEL primary kernel
      LINUX /boot/Image
      INITRD /boot/initrd
      APPEND ${cbootargs} root=/dev/mmcblk0p1 rw rootwait

LABEL hdmi2csi
      MENU LABEL HDMI2CSI
      LINUX /boot/Image-hdmi2csi
      INITRD /boot/initrd
      APPEND ${cbootargs} root=/dev/mmcblk0p1 rw rootwait
```
From now, the TX1 / TX2 system will boot the self-built kernel. 

### Device Tree
(Tested for 28.1)

The Device Tree must **NOT** be defined in `extlinux.conf` because U-Boot is modifying the Device Tree on-the-fly. If the Device Tree is defined in `extlinux.conf`, changes from U-Boot are overwritten. Instead, flash the Device Tree Blob as described below.

**Preparation**:
* Download the Driver Package from Nvidia
  * For 28.1
    * [Jetson TX1 64-bit Driver Package](https://developer.nvidia.com/embedded/dlc/l4t-jetson-tx1-driver-package-28-1)
    * [Jetson TX2 64-bit Driver Package](https://developer.nvidia.com/embedded/dlc/l4t-jetson-tx2-driver-package-28-1)
  * For 28.2.1
    * _Jetson TX1 64-bit Driver Package: Not available from Nvidia_
    * [Jetson TX2 64-bit Driver Package](https://developer.nvidia.com/embedded/dlc/tx2-driver-package-r2821)
* Unpack and change to downloaded Folder: `$ cd Linux_for_Tegra`
* Replace the existing Device Tree
  * TX1: `$ cp MY_KERNEL_DIR/kernel/kernel-4.4/arch/arm64/boot/dts/_ddot_/_ddot_/_ddot_/_ddot_/_ddot_/_ddot_/hardware/nvidia/platform/t210/jetson/kernel-dts/tegra210-jetson-tx1-p2597-2180-a01-devkit-hdmi2csi.dtb kernel/dtb/tegra210-jetson-tx1-p2597-2180-a01-devkit.dtb`
  * TX2: `$ cp MY_KERNEL_DIR/kernel/kernel-4.4/arch/arm64/boot/dts/_ddot_/_ddot_/_ddot_/_ddot_/_ddot_/_ddot_/hardware/nvidia/platform/t18x/quill/kernel-dts/tegra186-quill-p3310-1000-c03-00-base-hdmi2csi.dtb kernel/dtb/tegra186-quill-p3310-1000-c03-00-base.dtb`
* Put the system into _recovery mode_
  * Turn device completely off
    * If unsure remove power cable
  * Connect host system to Micro USB port (J20)
  * Hold _Recovery_ button while turning on the system
  * Keep the _Recovery_ button pressed
  * Reset the system by pressing the _Reset_ button while keep pushing the _Recovery_ button
  * Release the _Recovery_ button after approx 2 s
  * `$ lsusb` on the host system should show `NVidia Corp.`
* Flash Device Tree Blob
  * TX1: `$ sudo ./flash.sh -k DTB jetson-tx1 mmcblk0p1`
  * TX2: `$ sudo ./flash.sh -k kernel-dtb jetson-tx2 mmcblk0p1`


