## Install Tensorflow GPU on Ubuntu 18.04 LTS

This repository shows how to install Tensorflow GPU 2.3 on Ubuntu 18.04 LTS.  
Here is working combination of Tensorflow, python version and Nvidia CUDA driver that is found after trial and error.

### Environment
- Linux version and CPU infomation  
```
$ date
Sat Jul  3 19:29:08 KST 2021
$ cat /etc/lsb-release 
DISTRIB_DESCRIPTION="Ubuntu 18.04.5 LTS"
$ cat /proc/cpuinfo |grep "model name"
model name : Intel(R) Core(TM) i5-10400F CPU @ 2.90GHz
```
- Graphic card information  
`NVIDIA GeForce RTX 2080 with 8 GB Memory`

- Prerequest  
Do not install any Nvidia driver by yourself before this tutorial.
By following instructions a proper driver will be installed.  
This tutorial assumes that it just gets finished Ubuntu Linux installation.

### Instruction step

* Setup Nvidia repository
```
$ sudo dpkg -i cuda-repo-ubuntu1804_10.0.130-1_amd64.deb
Selecting previously unselected package cuda-repo-ubuntu1804.
...
The public CUDA GPG key does not appear to be installed.
$ sudo apt-key adv --fetch-keys http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/7fa2af80.pub
$ sudo apt update
$ sudo apt upgrade
$ sudo shutdown -r now
```

* Inatall Nvidia cuda 10.2
```
$ sudo apt-get -y install cuda-10-2
```

* Inatall he NVIDIA Deep Neural Network library(cuDNN 7)
```
$ sudo dpkg -i nvidia-machine-learning-repo-ubuntu1804_1.0.0-1_amd64.deb
$ sudo apt update
$ sudo apt install -y libcudnn7 libcudnn7-dev libnccl2 libc-ares-dev
$ sudo shutdown -r now
```

* See NVIDIA driver properly setup
```
$ nvidia-smi 
Sat Jul  3 19:37:20 2021       
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 470.42.01    Driver Version: 470.42.01    CUDA Version: 11.4     |
|-------------------------------+----------------------+----------------------+
| GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|                               |                      |               MIG M. |
|===============================+======================+======================|
|   0  NVIDIA GeForce ...  On   | 00000000:01:00.0  On |                  N/A |
| 25%   36C    P8    16W / 250W |   7876MiB /  7973MiB |      4%      Default |
|                               |                      |                  N/A |
+-------------------------------+----------------------+----------------------+
                                                                               
+-----------------------------------------------------------------------------+
| Processes:                                                                  |
|  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
|        ID   ID                                                   Usage      |
|=============================================================================|
|    0   N/A  N/A       990      G   /usr/lib/xorg/Xorg                149MiB |
|    0   N/A  N/A      1146      G   /usr/bin/gnome-shell               30MiB |
|    0   N/A  N/A      1981      G   /usr/lib/firefox/firefox          109MiB |
|    0   N/A  N/A      2369      C   ...rflowgpu2.3/bin/python3.7     7581MiB |
+-----------------------------------------------------------------------------+
```

* Add following line in `$HOME/.bashrc` file
```
export LD_LIBRARY_PATH=/usr/local/cuda/targets/x86_64-linux/lib:/usr/local/cuda/lib64:$LD_LIBRARY_PATH
```

* Fix dynamic library loading error  
```
# fix: Could not load dynamic library 'libcublas.so.10'; dlerror: libcublas.so.10: cannot open shared object file: No such file or directory
$ cd /usr/local/cuda-10.2/targets/x86_64-linux/lib/
$ sudo ln -s libcudart.so.10.2 libcudart.so.10.1
```

* Install python 3.7
```
$ sudo apt update
$ sudo apt install software-properties-common
$ sudo add-apt-repository ppa:deadsnakes/ppa
$ sudo apt update
$ sudo apt install -y python3.7
$ sudo apt install -y python3.7-dev
$ sudo apt install -y python3.7-venv
$ python3.7 --version
Python 3.7.10
```

* Setup python venv
```
$ cd ~
$ mkdir venv
$ python3.7 -m venv ./venv/tensorflowgpu2.3
$ source ./venv/tensorflowgpu2.3/bin/activate
$ pip install --upgrade pip
```

* Install tensorflow-gpu package
```
$ pip install tensorflow-gpu==2.3.0
$ pip install keras==2.4.0
```

* Check Tensorflow GPU is reay
```
$ python
>>> import tensorflow as tf
>>> print("Num GPUs Available: ", len(tf.config.list_physical_devices('GPU')))
print("Num GPUs Available: ", len(tf.config.list_physical_devices('GPU')))
```
