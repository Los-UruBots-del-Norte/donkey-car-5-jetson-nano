# donkey-car-5-jetson-nano
This is a guide to prepare Donkey Car 5.x.x for Jetson Nano, tested on 5.2.1-dev, Jetson Nano, Ubuntu 20.04, JetPack 4.6.
Instead of doing all of this painful process, you could try using the ready-to-use image with Ubuntu 20, TensorFlow, Opencv and TensorRT preinstalled of (https://github.com/Qengineering/Jetson-Nano-Ubuntu-20-image) and then follow the steps for building python 3.11, although I don't know yet how well this works together. To be researched...
# Instructions
preparation: apt update && apt upgrade
## 1. Building python 3.11 (~2-3h)
Since python can't be installed normally through repositories, we need to build it by ourselves following this guide: https://jetsonhacks.com/2023/06/12/upgrade-python-on-jetson-nano-tutorial/

## 2. Installing DonkeyCar (~1h)
You can follow the normal DonkeyCar Installation guide here,
but when creating the venv you have to be careful to setup the venv with the fresh python3.11 build with this command:`$ virtualenv --python="/usr/bin/python3.11" "/path/to/new/virtualenv/"`
Then you can continue the user guide.

## 3. Installing Tensorflow with CUDA support (~30min)
That's easy: pip install tensorflow[and-cuda]

## 4. Installing OpenCV (~6h)
By default, OpenCV does not deliver CUDA support, so we have to built it by ourselves. Caution, this process takes about 6h and looks like it freezes at the end, but it only takes time.
To build, a build script of https://github.com/mdegans/nano_build_opencv has to be used, but a few things are changed in our version:
- line 106: turned off python2 build, we don't need that in our case
- line 110: CUDA_ARCH_BIN targets now only 5.3, because at least the old 8.7 at the end of the list was causing problems.
DO NOT RUN THIS SCRIPT AS SUDO
After installation, create a symlink of the freshly build opencv into your activated venv. The installed opencv will be probably in `/usr/local/lib/python3.11/site-packages/cv2`. The target location is in `<YOUR_VENV_PATH>/lib/python3.11/site-packages/cv2`.
Now your venv is good to go.

## 5. Before using DonkeyCar manage.py
There might occur an error with opencv and tensorflow about that there is not enough memory or whatever.
To prevent errors like that, you need to export an env variable before, like in the following:
`export LD_PRELOAD=/usr/lib/aarch64-linux-gnu/libnvinfer.so.8:/usr/lib/aarch64-linux-gnu/libgomp.s:<YOUR_VENV_PATH>/lib/python3.11/site-packages/tensorflow_cpu_aws.libs/libgomp-cc9055c7.so.1.0.0`
To not forget that, I recommend to append it to `~/.bashrc`.

