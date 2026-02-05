# linux-debian-mint  

Notes on resolving hardware-software incompatibilities  
This is a WIP document that guides through and records the difficulties of getting linux running on a regular personal desktop or laptop.  

# Video Acceleration on Chromium Browsers - Intel or AMD i/GPU  

Video acceleration is broken on linux when using an intel or AMD processor. This is a fairly well documented problem and a quick google search will yield results on how to remediate the problem. The method I chose is to add a launch flags on chromium based browser (chrome, brave, etc.)  
```
--enable-features=VaapiVideoEncoder,VaapiVideoDecoder
```

## AV1 Video Acceleration broken on Intel GPU

AV1 videos from youtube do not get accelerated via the video pipeline on any browser.  
**Cause: AV1 requires film grain which is not available in the intel-media-va-driver package**  
To resolve this, install the non-free (some portion of the code is closed source). I like to use all my hardware accelerator even if they are not open source.  
```
sudo apt-get install intel-media-va-driver-non-free
```

To enable AVC/HEVC/VP9/AV1 low power encoding bitrate control (only for cpus older than 12th gen):
```
echo "options i915 enable_guc=2" > sudo tee -a /etc/modprobe.d/i915.conf
```

Add the following to your /etc/environment if you want all apps to use the va-api. Not required for chromium based browser.  
```
export LIBVA_DRIVERS_PATH=/usr/lib/x86_64-linux-gnu/dri/
export LIBVA_DRIVER_NAME=iHD
```

## Video Acceleration broken on Nvidia GPU

Nvidia SUCKS!!  
Instead of writing my own quick notes I will guide you to https://ubuntuhandbook.org/index.php/2024/01/firefox-vaapi-nvidia/.  
PPA: https://launchpad.net/~ubuntuhandbook1/+archive/ubuntu/nvidia-vaapi  
```
sudo add-apt-repository ppa:ubuntuhandbook1/nvidia-vaapi

sudo apt update

sudo apt install nvidia-vaapi-driver
```

Because vaapi driver uses NVDEC on the backend it will put GPU in high power mode. To bring it back to low power mode add the following to your /etc/environment  
```
export CUDA_DISABLE_PERF_BOOST=1
```

# Laptop Related Issues  

# Dell Latitude 7430  

## Screen too slow, mouse cursor jumping, keyboard strokes not displaying quickly, or general lag in screen  

For Intel gpus there can be screen flickering. To fix this add the following to the boot parameters:

```
i915.enable_psr=0
```
You can use terminal or if using linux mint you can use system administration tool.

## Intel p_state (trade power for slightly lower IO latency)  

TODO: p_state hw io wait  
Need to enable hwp_dynamic_boost at each boot. Best way to do is with systemd service.

'''
sudo nano /etc/systemd/system/hwp-dynamic-boost.service
'''

Then paste the following:

```
[Unit]
Description=Enable HWP Dynamic Boost
After=multi-user.target

[Service]
Type=oneshot
ExecStart=/bin/sh -c 'echo 1 > /sys/devices/system/cpu/intel_pstate/hwp_dynamic_boost'
RemainAfterExit=yes

[Install]
WantedBy=multi-user.target
```

Enable the service with 
```
sudo systemctl enable hwp-dynamic-boost.service
sudo systemctl start hwp-dynamic-boost.service
```
You should now have intel_pstate hwp dynamic boost enabled.

## Fingerprint not working  

Fingerprint is a tricky one. The dell latitude 7430 uses a broadcom fingerprint sensor. All of the drivers are provided by dell controlvault drivers for linux.

There are many discussion forums on dell community but none of them will work with linux kernel 6.14 - 6.17 because the links they provide are older.

Use the latest brcm_linux_fp package from either one of the following:

https://packages.broadcom.com/ui/repos/tree/General/dell-controlvault-drivers/brcm_linux_fp_6.4.334_6.4.054.0.tgz

https://packages.broadcom.com/artifactory/dell-controlvault-drivers/

Install this package and reboot, you should have your fingerprint working now. Use the UI in Linux mint to enroll fingerprint (or use cli).
