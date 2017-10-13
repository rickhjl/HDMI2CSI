# The HDMI2CSI module

##### Quicklinks
* [HDMI2CSI for previous L4T v24.2.1](https://github.com/InES-HPMM/linux-l4t/wiki/hdmi2csi/)
* [Drivers](https://github.com/InES-HPMM/linux-l4t-4.4/wiki/hdmi2csi#drivers)
* [Compiling (or use our prebuilt image)](https://github.com/InES-HPMM/linux-l4t-4.4/wiki/hdmi2csi#compiling-the-kernel-and-drivers)
* [Capturing Video](https://github.com/InES-HPMM/linux-l4t-4.4/wiki/hdmi2csi#capturing-hdmi-video-with-the-hdmi2csi-module)
* [Future Development](https://github.com/InES-HPMM/linux-l4t-4.4/wiki/hdmi2csi#future-development)
* [Examples](https://github.com/InES-HPMM/linux-l4t-4.4/wiki/hdmi2csi#examples)

----

The High-Performance Multimedia Group has developed a High Definition Multimedia Interface (HDMI®) to MIPI® Camera Serial Interface Type 2 (CSI-2) converter module (HDMI2CSI) as a plug-in to the [NVIDIA Jetson TX1 and Nvidia Jetson TX2](http://www.nvidia.com/object/jetson-tx1-dev-kit.html) development kit.

The HDMI2CSI module supports 4K video resolution for next-generation embedded Ultra High Definition video applications. The HDMI2CSI module offers two 4K/2K HDMI video and audio streams to be simultaneously converted in MIPI CSI-2 video and TDM audio format that can be processed by the Jetson TX1 / TX2 processor.

More information about the hardware as well as contact information for ordering is available at the [HPMM-blog](https://blog.zhaw.ch/high-performance/2016/05/20/4k-hdmi-to-csi-interface-for-tx1-evalboard/).

<img src="https://cloud.githubusercontent.com/assets/19492370/15536503/d9d2a564-2271-11e6-90bf-0fccabc76d8f.png" alt="hdmi2csi_tx1-1-676x615" width="400px;"/>

Fig. 1:  The HDMI2CSI Board attached to the [Nvidia TX1 Evaluation Board](http://www.nvidia.com/object/jetson-tx1-dev-kit.html)

## Drivers
The drivers to use the HDMI2CSI board are available in the https://github.com/InES-HPMM/linux-l4t/ repository. The OS is [Linux4Tegra](https://developer.nvidia.com/embedded/linux-tegra) from Nvidia, which is based on the Linux kernel 4.4. Compared to L4T versions 24.1 and 24.2.1, the official drivers from L4T were used (except for some minor changes).




