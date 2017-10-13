## Build GStreamer Manually

The pre-installed GStreamer application does not support `Userptr` or `Dmabuf` modes, because GStreamer uses the `libv4l2.so` library which does not support the aforementioned modes (see following link). As a result, REQBUFS IOCTLs other than MMAP memory are not handled by `libv4l2.so`. Therefore, it is necessary to build GStreamer without the `libv4l2.so` library in order to enable `Userptr` and `Dmabuf` modes.

https://git.linuxtv.org/v4l-utils.git/tree/lib/libv4l2/libv4l2.c#n1304

### How to build GStreamer

* [Accelerated GStreamer User Guide L4T r28.1](https://developer.nvidia.com/embedded/dlc/l4t-accelerated-gstreamer-guide-28-1) - See section _GStreamer Build Instructions_ on p. 21

**IMPORTANT**: When building `gst-plugins-good`, do **NOT** use `libv4l-dev` as suggested in the _Accelerated GStreamer User Guide_. Run `$ sudo apt-get remove libv4l-dev` to make sure that the library is not installed!

We successfully tested GStreamer version `1.8.0` with `Userptr` and `Dmabuf` modes.

#### Using the Manually Built GStreamer Version
When executing a GStreamer pipeline that should use the manually built version of GStreamer, export the following environment variables first (replace `YOURPATH` with your path to the GStreamer build):
```shell
export LD_LIBRARY_PATH=/YOURPATH/gst_1.8.0/out/lib/
export PATH=/YOURPATH/gst_1.8.0/out/bin/:$PATH
export GST_PLUGIN_PATH=$LD_LIBRARY_PATH
```
