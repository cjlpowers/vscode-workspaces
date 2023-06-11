


## Initial Setup

## Docker

### Install Docker

```shell
# dependencies
sudo apt update
sudo apt install apt-transport-https ca-certificates curl software-properties-common

# add docker official GPG key
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

# add the Docker repository to APT sources
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt update
apt-cache policy docker-ce

# finally install it
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin

# create the docker group
sudo groupadd docker

# add your user to the docker group
sudo usermod -aG docker $USER
```

### Verify Docker Install

```shell
docker run --rm hello-world
```
Should produce the following output:
```console
Hello from Docker!
This message shows that your installation appears to be working correctly.

```


## NVIDIA Drivers

### Install the NVIDIA Driver

Go to the [official NVIDIA driver page](https://www.nvidia.com/download/index.aspx?ref=blog.roboflow.com) and find your driver.

For example for the `RTX 3090` that is `NVIDIA-Linux-x86_64-525.116.04.run` at the time of writing.

```shell
sudo apt-get install nvidia-driver-525
```

Reboot to load the driver.

```shell
sudo reboot
```

### Verify the NVIDIA Driver install

```shell
nvidia-smi
```

Produces the following output
```console
Sat Jun 10 23:57:28 2023       
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 525.105.17   Driver Version: 525.105.17   CUDA Version: 12.0     |
|-------------------------------+----------------------+----------------------+
| GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|                               |                      |               MIG M. |
|===============================+======================+======================|
|   0  NVIDIA GeForce ...  Off  | 00000000:0B:00.0  On |                  N/A |
|  0%   52C    P8    19W / 350W |    866MiB / 24576MiB |      3%      Default |
|                               |                      |                  N/A |
+-------------------------------+----------------------+----------------------+
                                                                               
+-----------------------------------------------------------------------------+
| Processes:                                                                  |
|  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
|        ID   ID                                                   Usage      |
|=============================================================================|
|    0   N/A  N/A      1286      G   /usr/lib/xorg/Xorg                368MiB |
|    0   N/A  N/A      1559      G   /usr/bin/gnome-shell              132MiB |
|    0   N/A  N/A      3169      G   ...RendererForSitePerProcess      102MiB |
|    0   N/A  N/A      3655      G   ...407540100804671606,262144      259MiB |
+-----------------------------------------------------------------------------+
```

## Install NVIDIA Docker for GPU-Accelerated Containers

### Install the NVIDIA Container Toolkit

```shell
distribution=$(. /etc/os-release;echo $ID$VERSION_ID) \
      && curl -fsSL https://nvidia.github.io/libnvidia-container/gpgkey | sudo gpg --dearmor -o /usr/share/keyrings/nvidia-container-toolkit-keyring.gpg \
      && curl -s -L https://nvidia.github.io/libnvidia-container/$distribution/libnvidia-container.list | \
            sed 's#deb https://#deb [signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg] https://#g' | \
            sudo tee /etc/apt/sources.list.d/nvidia-container-toolkit.list
```

```shell
sudo apt-get update
sudo apt-get install -y nvidia-docker2
sudo systemctl restart docker
```

### Verify the NVIDIA Container Toolkit install

```shell
docker run --rm --gpus all nvidia/cuda:12.1.1-base-ubuntu22.04 nvidia-smi
```

Produces the following output:
```console
Unable to find image 'nvidia/cuda:12.1.1-base-ubuntu22.04' locally
12.1.1-base-ubuntu22.04: Pulling from nvidia/cuda
2ab09b027e7f: Pull complete 
c7fe10e29b4e: Pull complete 
7c59886ad6a3: Pull complete 
2eb31279c932: Pull complete 
2595064f316d: Pull complete 
Digest: sha256:767a915f43cd43b4b902615dfa4574f0aadf4484d7688b90d9d6e42a1d4df726
Status: Downloaded newer image for nvidia/cuda:12.1.1-base-ubuntu22.04
Sun Jun 11 05:04:59 2023       
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 525.105.17   Driver Version: 525.105.17   CUDA Version: 12.1     |
|-------------------------------+----------------------+----------------------+
| GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|                               |                      |               MIG M. |
|===============================+======================+======================|
|   0  NVIDIA GeForce ...  Off  | 00000000:0B:00.0  On |                  N/A |
|  0%   55C    P8    21W / 350W |    997MiB / 24576MiB |      0%      Default |
|                               |                      |                  N/A |
+-------------------------------+----------------------+----------------------+
                                                                               
+-----------------------------------------------------------------------------+
| Processes:                                                                  |
|  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
|        ID   ID                                                   Usage      |
|=============================================================================|
+-----------------------------------------------------------------------------+
```