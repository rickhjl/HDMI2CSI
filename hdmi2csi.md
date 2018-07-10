# The HDMI2CSI module

##### Quicklinks
* [HDMI2CSI for previous L4T v24.2.1 (Deprecated)](https://github.com/InES-HPMM/linux-l4t/wiki/hdmi2csi/)
* [Drivers](https://github.com/InES-HPMM/linux-l4t-4.4/wiki/hdmi2csi#drivers)
* [Compiling](https://github.com/InES-HPMM/linux-l4t-4.4/wiki/hdmi2csi#compiling-the-kernel-and-drivers)
* [Examples](https://github.com/InES-HPMM/linux-l4t-4.4/wiki/hdmi2csi#examples)
* [Changing the EDID](https://github.com/InES-HPMM/linux-l4t-4.4/wiki/hdmi2csi#changing-the-edid)

----

The High-Performance Multimedia Group has developed a High Definition Multimedia Interface (HDMI®) to MIPI® Camera Serial Interface Type 2 (CSI-2) converter module (HDMI2CSI) as a plug-in to the [NVIDIA Jetson TX1 and Nvidia Jetson TX2](http://www.nvidia.com/object/jetson-tx1-dev-kit.html) development kit.

The HDMI2CSI module supports 4K video resolution for next-generation embedded Ultra High Definition video applications. The HDMI2CSI module offers two 4K/2K HDMI video and audio streams to be simultaneously converted in MIPI CSI-2 video and TDM audio format that can be processed by the Jetson TX1 / TX2 processor.

More information about the hardware as well as contact information for ordering is available at the [HPMM-blog](https://blog.zhaw.ch/high-performance/2016/05/20/4k-hdmi-to-csi-interface-for-tx1-evalboard/).

<img src="https://cloud.githubusercontent.com/assets/19492370/15536503/d9d2a564-2271-11e6-90bf-0fccabc76d8f.png" alt="hdmi2csi_tx1-1-676x615" width="400px;"/>

Fig. 1:  The HDMI2CSI Board attached to the [Nvidia TX1 Evaluation Board](http://www.nvidia.com/object/jetson-tx1-dev-kit.html)

## Drivers
The drivers to use the HDMI2CSI board are available in the https://github.com/InES-HPMM/linux-l4t-4.4/ repository. The OS is [Linux4Tegra](https://developer.nvidia.com/embedded/linux-tegra) from Nvidia, which is based on the Linux kernel 4.4. Compared to L4T versions 24.1 and 24.2.1, the official drivers from L4T were used (except for some minor changes).

#### Features
Our drivers from Nvidia allow capturing of HDMI sources in formats up to 2160p30 (UHD).
In the current state, the capturing will work in most cases. But this is not production ready code! It is suitable to evaluate the capabilities of the HDMI-to-CSI bridge in a prototype phase. More complex HDMI functionality (e.g. changing resolution of the HDMI source on the fly, while plugged-in) is still not yet supported.
If you are interested in improving the drivers and moving towards more production-ready code, contact us!

Currently, only branch `l4t-r28.1` is available:

| **Branch** | **L4T Version** | **HDMI-In Ports** | **Resolution** | **Status** | **Audio** |
| ---------- | --------------- | -------------------------- | ------------------- | -------- | -------------- |
| **[hdmi2csi/l4t-r28.1](https://github.com/InES-HPMM/linux-l4t-4.4/tree/hdmi2csi/l4t-r28.1)** | R28.1 | A and B | Max. 2160p30 on A, Max. 1080p60 on B. Details: [EDID](https://github.com/InES-HPMM/linux-l4t-4.4/wiki/hdmi2csi#changing-the-edid) | TX1: OK, TX2: OK | Untested (see [Capturing HDMI Audio](https://github.com/InES-HPMM/linux-l4t-4.4/wiki/captureHdmiAudio) ) |
| **[hdmi2csi/l4t-r28.2.1](https://github.com/InES-HPMM/linux-l4t-4.4/tree/hdmi2csi/l4t-r28.2.1)** | R28.2.1 | A and B | Max. 2160p30 on A, Max. 1080p60 on B. Details: [EDID](https://github.com/InES-HPMM/linux-l4t-4.4/wiki/hdmi2csi#changing-the-edid) | TX1: Untested, TX2: OK | Untested (see [Capturing HDMI Audio](https://github.com/InES-HPMM/linux-l4t-4.4/wiki/captureHdmiAudio) ) |



Deprecated: [hdmi2cs/l4t-r23-1](https://github.com/InES-HPMM/linux-l4t/tree/hdmi2csi/l4t-r23-1), [hdmi2cs/l4t-r23-1-dev-4K](https://github.com/InES-HPMM/linux-l4t/tree/hdmi2csi/l4t-r23-1-dev-4K), [hdmi2cs/l4t-r24-1](https://github.com/InES-HPMM/linux-l4t/tree/hdmi2csi/l4t-r24-1), [hdmi2cs/l4t-r24-2.1](https://github.com/InES-HPMM/linux-l4t/tree/hdmi2csi/l4t-r24-2.1) 

## Compiling the kernel and drivers
Users with knowledge of Linux may want to compile the kernel from our sources. More information about compiling the kernel are available at [[Custom Kernel Compilation|customKernelCompilation]].

## Examples
Note that the `device=` parameter of v4l2src for 
* `/dev/video0` selects HDMI-In A  (maximum resolution 2160p30)
* `/dev/video1` selects HDMI-In B  (maximum resolution 1080p60) **NOTE**: Changed from `video2` to `video1` compared to L4T r24.2.1

### GStreamer
Some of these pipelines may require GStreamer plugins that are only available in the custom built GStreamer 1.8.0. Therefore it is necessary to switch to it by changing environment variables:
```
export DISPLAY=:0; export LD_LIBRARY_PATH=/home/ubuntu/build/gst_1.8.0/out/lib/; export PATH=/home/ubuntu/build/gst_1.8.0/out/bin/:$PATH; export GST_PLUGIN_PATH=$LD_LIBRARY_PATH
```
More information about how to build GStreamer can be found in the [Custom GStreamer](https://github.com/InES-HPMM/linux-l4t-4.4/wiki/CustomGStreamer) section.

#### Capturing and Rendering
* Capture 2160p30 on HDMI-In A and render on HDMI Display
  * `gst-launch-1.0 v4l2src device=/dev/video0 ! 'video/x-raw, width=3840, height=2160, framerate=30/1, format=UYVY' ! nvvidconv ! 'video/x-raw(memory:NVMM), width=3840, height=2160, framerate=30/1, format=I420' ! nvoverlaysink sync=false`
* Use RGB instead of UYVY. **NOTE**: So far RGB is running only up to 1080p30!
  * `DISPLAY=:0 gst-launch-1.0 v4l2src device=/dev/video0 ! 'video/x-raw, format=RGB, width=1920, height=1080, framerate=30/1' ! videoconvert ! 'video/x-raw, format=I420, width=1920, height=1080, framerate=30/1' ! xvimagesink sync=false`
* Capture 1080p60 on HDMI-In B and render on HDMI Display
  * `gst-launch-1.0 v4l2src device=/dev/video1 ! 'video/x-raw, width=1920, height=1080, framerate=60/1, format=UYVY' ! nvvidconv ! 'video/x-raw(memory:NVMM), width=1920, height=1080, framerate=60/1, format=I420' ! nvoverlaysink sync=false`
* *Userptr* mode for buffer passing for improved performance (requires modifications to GStreamer)
  * `gst-launch-1.0 v4l2src io-mode=3 device=/dev/video0 do-timestamp=true ! 'video/x-raw, width=3840, height=2160, framerate=30/1, format=UYVY' ! xvimagesink sync=false`
* *Dmabuf* mode for buffer passing for improved performance (requires modifications to GStreamer)
  * `gst-launch-1.0 v4l2src io-mode=4 device=/dev/video0 do-timestamp=true ! 'video/x-raw, width=3840, height=2160, framerate=30/1, format=UYVY' ! xvimagesink sync=false`

#### Video Processing on GPU
* Use GPU plugin for video processing `nvivafilter`
  * `gst-launch-1.0 v4l2src device=/dev/video0 ! 'video/x-raw, width=3840, height=2160, format=UYVY, framerate=30/1' ! nvvidconv ! 'video/x-raw(memory:NVMM), width=3840, height=2160, format=NV12' ! nvtee ! nvivafilter cuda-process=true pre-process=true post-process=true customer-lib-name="libnvsample_cudaprocess.so" ! 'video/x-raw(memory:NVMM), format=(string)NV12' ! nvoverlaysink display-id=0 -e`

#### Streaming
##### H.265 Encode and Send on TX2, receive on PC with VLC
* Install GStreamer bad plugins: `$ sudo apt install gstreamer1.0-plugins-bad`
* Stream RTP (H.265 encoded) of Input HDMI-A on TX1 <- warning: H.265 encoder has some problems with 2160p30
  * `gst-launch-1.0 v4l2src ! 'video/x-raw, width=3840, height=2160, framerate=30/1, format=UYVY' ! nvvidconv ! 'video/x-raw(memory:NVMM), format=I420' ! queue ! omxh265enc bitrate=2000000 ! 'video/x-h265, stream-format=(string)byte-stream' ! h265parse ! mpegtsmux ! rtpmp2tpay ! udpsink port=5000 async=false sync=false host=192.168.0.1`
  * Change `host=...` to the IP of the receiver
* On the receiver create a file `mpeg_ts.sdp` and open it with VLC:
```
v=0
m=video 5000 RTP/AVP 33
c=IN IP4 192.168.0.2
a=rtpmap:33 MP2T/90000
```

###### Receive/decode on a separate TX2 and render on HDMI display
Instead of receiving on PC with VLC, receive on a separate TX2 with a GStreamer pipeline:
* Install GStreamer bad plugins: `$ sudo apt install gstreamer1.0-plugins-bad`
* Connect receiver-TX2 to a HDMI display 
* Run receiver GStreamer pipeline
  * `gst-launch-1.0 udpsrc port=5000  ! 'video/mpegts, systemstream=(boolean)true' ! tsdemux ! h265parse ! queue ! omxh265dec ! nvoverlaysink sync=false -e`

##### H.264 Encode and Send on TX2, receive on PC with GStreamer
* Install GStreamer bad plugins: `$ sudo apt install gstreamer1.0-plugins-bad`
* Stream RTP (H.264 encoded) Input of HDMI-B on TX2
  * `gst-launch-1.0 v4l2src device=/dev/video1 ! 'video/x-raw, width=1920, height=1080, framerate=60/1, format=UYVY' ! nvvidconv ! 'video/x-raw(memory:NVMM), format=I420' ! queue ! omxh264enc bitrate=20000000 ! 'video/x-h264, stream-format=(string)byte-stream' ! h264parse ! mpegtsmux ! rtpmp2tpay ! udpsink port=5000 async=false sync=false host=192.168.0.1`
  * Change `host=...` to the IP of the receiver
  * Encoding bitrate may need to be adjusted, based on video content
* And receive it on a host PC with GStreamer: (or with VLC, as described above)
```
gst-launch-1.0 udpsrc port=5000 caps="application/x-rtp,media=(string)video,clock-rate=(int)90000,encoding-name=(string)MP2T-ES" ! rtpbin ! rtpmp2tdepay ! tsdemux ! h264parse ! avdec_h264 ! videoconvert ! xvimagesink sync=false -vvv -e
```

#### Recording
* Save to disk (H.264 encoded)
  * `gst-launch-1.0 v4l2src device=/dev/video0 ! 'video/x-raw, width=3840, height=2160, framerate=30/1, format=UYVY' ! nvvidconv ! 'video/x-raw(memory:NVMM), format=I420' ! queue ! omxh264enc bitrate=8000000 ! h264parse ! matroskamux ! filesink location=test_4k_h264.mkv -e`


## Changing the EDID
Each HDMI-In Port acts as a HDMI sink. Therefore each of them needs to specify the timings (resolutions) it supports to a HDMI source. This is done via the Extended Display Identification Data (EDID). A standard EDID is defined in the driver, containing the following resolutions:
* 16:9
  * 3840x2160p24/25/30
  * 1920x1080p24/25/30/50/60
  * 1280x720p24/25/30/60
* 64:27 ("21:9")
  * 1920x1080p23.98/24/25/29.97/30/60
  * 1280x720p23.98/24/25/29.94/30/59.97/60

If you want to support a different resolution, you can set it as described below. Currently there is an upper limit of 3840x2160p30, but any resolution "below" should be supported.

#### Read out EDID
The following command will read out the EDID from HDMI-In A and save it to a file.
```
$ v4l2-ctl --get-edid=pad=0,startblock=0,format=hex,file=tc358840_edid.raw
```
The file `tc358840_edid.raw` looks like:
```
00 ff ff ff ff ff ff 00 52 62 88 88 00 88 88 88
1c 15 01 03 80 00 00 78 0a 0d c9 a0 57 47 98 27
12 48 4c 00 00 00 01 01 01 01 01 01 01 01 01 01
01 01 01 01 01 01 b4 66 00 a0 f0 70 1f 80 30 20
35 00 80 88 42 00 00 1c ed 2c 80 a0 70 38 1a 40
30 20 35 00 40 44 21 00 00 1c 00 00 00 fc 00 54
6f 73 68 69 62 61 2d 55 48 32 43 0a 00 00 00 fd
00 17 3d 0f 8c 1e 00 00 00 00 00 00 00 00 01 ab
02 03 1a 74 4a 5f 10 43 3e 22 1f 20 21 4c 4a 23
09 07 01 66 03 0c 00 30 00 80 b4 66 00 a0 f0 70
1f 80 30 20 35 00 80 88 42 00 00 f8 00 00 1c ec
68 00 a0 f0 70 37 80 30 20 3a 00 00 70 f8 00 00
1c ec 68 00 a0 f0 70 37 80 30 20 3a 00 00 70 f8
00 00 1c ec 68 00 a0 f0 70 37 80 30 20 3a 00 00
70 f8 00 00 1c 00 00 00 00 00 00 00 00 00 00 00
00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 db
```

#### Change the EDID
If you want to set a new EDID, you can generate it with a program like [AnalogWay EDID Editor](http://www.analogway.com/en/products/software-and-tools/aw-edid-editor/). Save the EDID to a file (e.g. `tc358840_edid_NEW.raw`. Then apply it to HDMI-In A.
```
$ v4l2-ctl --set-edid=pad=0,file=tc358840_edid_NEW.raw
```

#### Ensure that resolution / framerate is in driver
Make sure that your desired framerate and resolution is enabled in the tc358840 driver: https://github.com/InES-HPMM/linux-l4t-4.4-kernel/blob/878fbe7ba0789f2cbc0192fa53fd9b5d37ad342a/drivers/media/i2c/tc358840_regs.h#L729