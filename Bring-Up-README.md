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

TODO: Panel Self Refresh  

## Intel p_state (trade power for slightly lower IO latency)  

TODO: p_state hw io wait  

## Fingerprint not working  

TODO: Broadcom fingerprint sensor  
