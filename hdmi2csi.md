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

#### Features
Our drivers from Nvidia allow capturing of HDMI sources in formats up to 2160p30 (UHD).
In the current state, the capturing will work in most cases. But this is not production ready code! It is suitable to evaluate the capabilities of the HDMI-to-CSI bridge in a prototype phase. More complex HDMI functionality (e.g. changing resolution of the HDMI source on the fly, while plugged-in) is still not yet supported.
If you are interested in improving the drivers and moving towards more production-ready code, contact us!

The supported features vary based on the branch:

| **Branch** | **L4T Version** | **Dynamic Format Resolve** | **HDMI-In Ports** | **Max. Resolution** | **Status** | **EDID** | **Audio** |
| ---------- | --------------- | --------------- | -------------------------- | ------------------- | ---------------------- | -------- | --------- |
| **[hdmi2csi/l4t-r28.1](https://github.com/InES-HPMM/linux-l4t-4.4/tree/hdmi2csi/l4t-r28.1)** | R28.1 | Yes | A and B | [2160p30](https://github.com/InES-HPMM/linux-l4t/wiki/io-modes) | Under development | Fixed. Native: 2160p30, 1080p50. Extended: 2160p30, 1080p60, 720p29.97/30, 1080p30, 1080p50, 1080p29.97/30.  | Stereo Capture on HDMI In Port B with separate cable (see [[Capturing HDMI Audio|captureHdmiAudio]]) |


Deprecated: [hdmi2cs/l4t-r23-1](https://github.com/InES-HPMM/linux-l4t/tree/hdmi2csi/l4t-r23-1), [hdmi2cs/l4t-r23-1-dev-4K](https://github.com/InES-HPMM/linux-l4t/tree/hdmi2csi/l4t-r23-1-dev-4K), [hdmi2cs/l4t-r24-1](https://github.com/InES-HPMM/linux-l4t/tree/hdmi2csi/l4t-r24-1), [hdmi2cs/l4t-r24-2.1](https://github.com/InES-HPMM/linux-l4t/tree/hdmi2csi/l4t-r24-2.1) 


