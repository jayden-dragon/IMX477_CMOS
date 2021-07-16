# IMX477 CMOS sensor setting

Assigned: Jayden Chang, Kyungmin Kook
Status: Complete
Sub-tasks: https://www.notion.so/Jetson-Nano-DEV-kit-B01-Setting-ad6e5ea9267a4f60990dc04975ae5e08, https://www.notion.so/HW-Block-Diagram-2075b730d99f430d98285118dadb2694
Type: R&D🔨
Weekly Check: -
Weekly_status: No

# 🌍 Jetson Nano IMX477 CMOS sensor setting

- Host Ubuntu 18.04
- Jetson Nano DEV kit B01

    Module             P3448-0002

    Carrier Board    P3449-0000

    [https://developer.nvidia.com/embedded/linux-tegra-r325](https://developer.nvidia.com/embedded/linux-tegra-r325)

- Target Ubuntu 18.04
- IMX477 Linux Driver
    1. ArduCAM Driver

        [https://www.hackster.io/SaadTiwana/embedded-diaries-how-to-use-rpi-hq-camera-with-jetson-e2063e](https://www.hackster.io/SaadTiwana/embedded-diaries-how-to-use-rpi-hq-camera-with-jetson-e2063e)

        [https://www.arducam.com/docs/camera-for-jetson-nano/native-jetson-cameras-imx219-imx477/imx477/#4--driver-setup-](https://www.arducam.com/docs/camera-for-jetson-nano/native-jetson-cameras-imx219-imx477/imx477/#4--driver-setup-)

        [https://www.youtube.com/watch?v=uIycS8989fg&list=PLDUciJ0PTc46YJeh6B11hWzI_UQE90nIM&index=13](https://www.youtube.com/watch?v=uIycS8989fg&list=PLDUciJ0PTc46YJeh6B11hWzI_UQE90nIM&index=13)

        [https://github.com/JetsonHacksNano/CSI-Camera](https://github.com/JetsonHacksNano/CSI-Camera)

    2. RidgeRun

        [https://developer.ridgerun.com/wiki/index.php?title=Raspberry_Pi_HQ_camera_IMX477_Linux_driver_for_Jetson](https://developer.ridgerun.com/wiki/index.php?title=Raspberry_Pi_HQ_camera_IMX477_Linux_driver_for_Jetson)

        [https://github.com/RidgeRun/NVIDIA-Jetson-IMX477-RPIV3](https://github.com/RidgeRun/NVIDIA-Jetson-IMX477-RPIV3)

---

## 🐫 ArduCAM Driver (recommended)

- JetPack 4.5 (L4T 32.5)

    [https://developer.nvidia.com/jetpack-sdk-45-archive](https://developer.nvidia.com/jetpack-sdk-45-archive)

### 🌵 Download automatic installation script

[https://www.arducam.com/docs/camera-for-jetson-nano/native-jetson-cameras-imx219-imx477/imx477-how-to-install-the-driver/](https://www.arducam.com/docs/camera-for-jetson-nano/native-jetson-cameras-imx219-imx477/imx477-how-to-install-the-driver/)

```
$ cd ~

$ wget https://github.com/ArduCAM/MIPI_Camera/releases/download/v0.0.3/install_full.sh
```

### 🏜️ Install the Driver

```
$ chmod +x install_full.sh

$ ./install_full.sh -m imx477

reboot now?(y/n): y
```

### 🌞 Modify 'boot/extlinux/extlinux.conf'

```
LABEL primary
      MENU LABEL primary kernel
      LINUX /boot/arducam/Image
      INITRD /boot/initrd
      APPEND ${cbootargs} quiet root=/dev/mmcblk0p1 rw rootwait rootfstype=ext4 console=ttyS0,115200n8 console=tty0 fbcon=map:0 net.ifnames=0 

FDT /boot/arducam/tegra210-p3448-0000-p3449-0000-b00-imx477.dtb
```

### 🐯 Example

- Display 1920 x 1080

```
$ SENSOR_ID=0 # 0 for CAM0 and 1 for CAM1 ports

$ FRAMERATE=60 # Framerate can go from 2 to 60 for 1920x1080 mode

$ gst-launch-1.0 nvarguscamerasrc sensor-id=$SENSOR_ID ! "video/x-raw(memory:NVMM),width=1920,height=1080,framerate=$FRAMERATE/1" ! nvvidconv ! nvoverlaysink
```

## 🐪 RidgeRun (X)

- JetPack 4.5 (L4T 32.5)

    [https://developer.nvidia.com/jetpack-sdk-45-archive](https://developer.nvidia.com/jetpack-sdk-45-archive)

- DeepStream 5.0

### 🌵 Download debian packages

[https://drive.google.com/drive/folders/1E1bE57VlXqadLGg8sSw8KwW7AJRWjbmG](https://drive.google.com/drive/folders/1E1bE57VlXqadLGg8sSw8KwW7AJRWjbmG)

### 🏜️ Install debian packages

*** Check your debian packanges ***

```
$ sudo apt-get install --reinstall ./nvidia-l4t-kernel_4.9.140-tegra-32.4.3-20200625213809_arm64.deb

$ sudo apt-get install --reinstall ./nvidia-l4t-kernel-dtbs_4.9.140-tegra-32.4.3-20200625213809_arm64.deb 
```

### 🌞 Modify  '/boot/extlinux/extlinux.conf' file

- **Original**

```
LABEL primary
			MENU LABEL primary kernel
			LINUX /boot/Image
			INITRD /boot/initrd
			APPEND ${cbootargs} quiet root=PARTUUID=4a383752-8840-464a-9f05-a74f920d33e8 rw rootwait rootfstype=ext4 
console=ttyS0,115200n8 console=tty0 fbcon=map:0 net.ifnames=0 
sdhci_tegra.en_boot_part_access=1
```

- M**odified**

```
LABEL primary
      MENU LABEL primary kernel
      LINUX /boot/Image
      INITRD /boot/initrd
      APPEND ${cbootargs} quiet root=/dev/mmcblk0p1 rw rootwait rootfstype=ext4 
console=ttyS0,115200n8 console=tty0 fbcon=map:0 net.ifnames=0
      
FDT /boot/tegra210-p3448-0000-p3449-0000-b00.dtb
```

---

## 🦍 System requirements

- **Raspberry Pi high quality camera**

    IMX477 CMOS sensor

- **ArduCAM**

    [https://www.arducam.com/docs/camera-for-jetson-nano/native-jetson-cameras-imx219-imx477/](https://www.arducam.com/docs/camera-for-jetson-nano/native-jetson-cameras-imx219-imx477/)

- **Jetson NANO DEV kit B01**

    [https://docs.nvidia.com/jetson/l4t/index.html#page/Tegra Linux Driver Package Development Guide/multimedia.html#wwpID0E0KC0HA](https://docs.nvidia.com/jetson/l4t/index.html#page/Tegra%20Linux%20Driver%20Package%20Development%20Guide/multimedia.html#wwpID0E0KC0HA)

    [https://docs.nvidia.com/jetson/l4t-multimedia/index.html](https://docs.nvidia.com/jetson/l4t-multimedia/index.html)

    [https://docs.nvidia.com/deeplearning/tensorrt/install-guide/index.html#overview](https://docs.nvidia.com/deeplearning/tensorrt/install-guide/index.html#overview)

    [https://docs.nvidia.com/jetson/l4t/pdf/Jetson_Linux_Driver_Package_Release_Notes_R32.5_GA.pdf](https://docs.nvidia.com/jetson/l4t/pdf/Jetson_Linux_Driver_Package_Release_Notes_R32.5_GA.pdf)

    Module             P3448-0002

    Carrier Board    P3449-0000

    JetPack 4.5 (L4T 32.5) - Ubuntu 18.04 (Kernel version - 4.9)

    TensorRT 7.1.3

    cuDNN 10.2

    CUDA 10.2

    GStreamer 1.0

    [https://developer.download.nvidia.com/embedded/L4T/r32-2_Release_v1.0/Accelerated_GStreamer_User_Guide.pdf?OxN3TJdJREdCnajmLQH1XNo-mbaExhOTkhJMG_X0XHAMm1rdlsXEtApN_wAoT8kQhaLFHFnpx6zyJx1iKOsDef9k9AuSmxIXwGVChHBV-kz0fRccZAnzkjo9ZI3imNEUFhxvBmU60rlagHNxLtdF3XpRYomcoiS13MERERwDXNRY1hE](https://developer.download.nvidia.com/embedded/L4T/r32-2_Release_v1.0/Accelerated_GStreamer_User_Guide.pdf?OxN3TJdJREdCnajmLQH1XNo-mbaExhOTkhJMG_X0XHAMm1rdlsXEtApN_wAoT8kQhaLFHFnpx6zyJx1iKOsDef9k9AuSmxIXwGVChHBV-kz0fRccZAnzkjo9ZI3imNEUFhxvBmU60rlagHNxLtdF3XpRYomcoiS13MERERwDXNRY1hE)

    VPI 1.0

    OpenCV 4.1.1

    Visionworks 1.6

    NVIDIA Nsight Systems 2020.5

    NVIDIA Nsight Graphics 2020.5

    DeepStream 5.0

    [https://docs.nvidia.com/jetson/l4t-multimedia/group__V4L2Argus.html](https://docs.nvidia.com/jetson/l4t-multimedia/group__V4L2Argus.html)

    [Jetson_driver_info.pdf](IMX477%20CMOS%20sensor%20setting%20bcd93b6f4660445b843d0ce8579b0bd7/Jetson_driver_info.pdf)

## 🐵 Architecture

- **Jetson Board Support Package architecture**

![IMX477%20CMOS%20sensor%20setting%20bcd93b6f4660445b843d0ce8579b0bd7/Untitled.png](IMX477%20CMOS%20sensor%20setting%20bcd93b6f4660445b843d0ce8579b0bd7/Untitled.png)

- **Camera architecture**

![IMX477%20CMOS%20sensor%20setting%20bcd93b6f4660445b843d0ce8579b0bd7/Untitled%201.png](IMX477%20CMOS%20sensor%20setting%20bcd93b6f4660445b843d0ce8579b0bd7/Untitled%201.png)

---

## 🔥 Overclocking

***** Don't even try if you don't have a cooling fan *****

```cpp
$ sudo nvpmodel -m 0

$ sudo jetson_clocks
```

---

*by The Legendary Dragon 🐉*