### Custom Kernel Compilation

In order to run the drivers, a Jetson TX1 development board is required. Ensure that it is flashed with the L4T version corresponding to the branch used (28.1). You can use Jetpack or do it by hand as described in the Nvidia documentation.

It is necessary to replace the Kernel, Modules and device tree with those from this repository.

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

#### Replace / Install new kernel files
After building is complete, the Kernel image, modules and device tree blob need to be replaced on the TX1 / TX2. The easiest way is to connect the Jetson to the host development PC via micro-USB. This requires: a micro-USB connection to the PC and a serial console to connect to J21 (example: http://elinux.org/Jetson/TX1_Serial_Console) for interfacing with U-Boot.

* Restart the TX1  
* On the serial console: Interrupt U-Boot (this means early on in the boot process hit any key). You should get a U-Boot console like:
```
Hit any key to stop autoboot:  0
Tegra210 (P2371-2180) #
```
* Now we can use the UBoot command line interface http://www.denx.de/wiki/view/DULG/UBootCommandLineInterface
    * Enter the command `ums 0 mmc 0` 
* The TX1 will then mount its filesystem to the host development PC (possibly you have to manually mount the file system on the host PC)
* The TX1 should mount on the PC e.g. under `/media/USERNAME/1b25da0b-1e47-49bb-b5ea-ae284fddc80f` (example on Ubuntu14.04, the last string will differ based on the partitions of the TX1)
* Copy/install the kernel files you built onto the TX1
    * Replace the kernel Image (copy `arch/arm64/boot/Image` to the TX1 in `/boot/Image` (or call it differently if you want to use multiple extlinux configurations)
    * Replace the DTB (copy `arch/arm64/boot/dts/tegra210-jetson-tx1-p2597-2180-a01-devkit.dtb` to the TX1 mount path and `/boot/tegra210-jetson-tx1-p2597-2180-a01-devkit-hdmi-4k.dtb`)
    * Install the modules (using `make modules_install` and the correct `INSTALL_MOD_PATH` environment variable to the TX1 mount path) 
* After all is installed/replaced, you have to safely remove the USB device from the host PC (make sure all write operations are complete)
* As soon as the animation in the serial console is spinning again, you can abort it with Ctrl+C 
* Start the TX1 from serial console / U-Boot using command `boot`


Instead of replacing the original files, you can create a new entry in the _extlinux_ configuration file `/boot/extlinux/extlinux.conf` and choose between multiple kernels on boot.
Here you can define multiple configurations for kernel images, dtbs and kernel boot parameters. 