# Install the deeptime/vampnet package on Ubuntu 16
Install the deeptime package vampnet for deep learning of Markov state models (including nvidia drivers + tensorflow gpu install) on Ubuntu 16.
The instructions are based on instructions of William Falcon for the installation of GPU-powered tensorflow.
(https://github.com/williamFalcon/tensorflow-gpu-install-ubuntu-16.04)

Following these instructions you will have:

1. Cuda 9.0 drivers installed (including nvidia gpu drivers for your cuda enables nvidia gpu).
2. Miniconda with the latest python (currently 3.7)
3. A conda environment with python 3.6.    
4. The latest stable tensorflow version with gpu support.
5. vampnet

---   
### Step 0: Blacklist noveau     
Before you begin, you may need to disable the nouveau opensource ubuntu nvidia driver.

**Modify modprobe.d file**
1. Boot the linux system. When you reach the login prompt, press ctrl+alt+F1 to get to a terminal screen. Login via this terminal screen.

2. Create a file: /etc/modprobe.d/nouveau-blacklist.conf e.g. by 
```
sudo touch /etc/modprobe.d/nouveau-blacklist.conf
```
3.  Put the following in the above file...
```
blacklist nouveau
options nouveau modeset=0
```
4. Regenerate the kernel initramfs
```
sudo update-initramfs -u
```
5. reboot system   
```bash
reboot
```   
    
6. On reboot, verify that noveau drivers are not loaded   
```
lsmod | grep nouveau
```

If `nouveau` drivers are still loaded DON'T proceed with the installation guide and troubleshoot why it's still loaded.    
 
7. Uninstall every nvidia related software:   
```bash 
sudo apt-get update
sudo apt-get purge nvidia*  
sudo reboot   
```   

---   
## Installation steps     


0. update apt-get and upgrade  
``` bash 
sudo apt-get update
sudo apt-get upgrade
```
   
1. Install apt-get deps  
``` bash
sudo apt-get install openjdk-8-jdk git python-dev python3-dev python-numpy python3-numpy build-essential python-pip python3-pip python-virtualenv swig python-wheel libcurl3-dev curl   
```

2. install nvidia drivers 
``` bash
# The 16.04 installer works with 16.10.
# download drivers
curl -O http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/cuda-repo-ubuntu1604_9.0.176-1_amd64.deb

# download key to allow installation
sudo apt-key adv --fetch-keys http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/7fa2af80.pub

# install actual package
sudo dpkg -i ./cuda-repo-ubuntu1604_9.0.176-1_amd64.deb

#  install cuda (but it'll prompt to install other deps, so we try to install twice with a dep update in between)
sudo apt-get update
sudo apt-get install cuda-9-0   
```    

2a. reboot Ubuntu
``` bash
sudo reboot
```    

2b. check nvidia driver install 
``` bash
nvidia-smi   

# you should see a list of gpus printed    
# if not, the previous steps failed.   
``` 

2c. update and upgrade again
``` bash 
sudo apt-get update
sudo apt-get upgrade
```

3. Install cudnn 

``` bash
wget https://s3.amazonaws.com/open-source-william-falcon/cudnn-9.0-linux-x64-v7.3.1.20.tgz
sudo tar -xzvf cudnn-9.0-linux-x64-v7.3.1.20.tgz
sudo cp cuda/include/cudnn.h /usr/local/cuda/include
sudo cp cuda/lib64/libcudnn* /usr/local/cuda/lib64
sudo chmod a+r /usr/local/cuda/include/cudnn.h /usr/local/cuda/lib64/libcudnn*
```    

4. Add these lines to end of ~/.bashrc:   
``` bash
export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:/usr/local/cuda/lib64:/usr/local/cuda/extras/CUPTI/lib64"
export CUDA_HOME=/usr/local/cuda
export PATH="$PATH:/usr/local/cuda/bin"
```   

4a. Reload bashrc     
``` bash 
source ~/.bashrc
```   

5. Install miniconda   
``` bash
wget https://repo.continuum.io/miniconda/Miniconda3-latest-Linux-x86_64.sh
bash Miniconda3-latest-Linux-x86_64.sh   

# press s to skip terms   

# Do you approve the license terms? [yes|no]
# yes

# Miniconda3 will now be installed into this location:
# accept the location

# Do you wish the installer to prepend the Miniconda3 install location
# to PATH in your /home/ghost/.bashrc ? [yes|no]
# yes    

```   

5a. Reload bashrc     
``` bash 
source ~/.bashrc
```   

6. Create python 3.6 conda env to install tf   
``` bash
conda create -n vampnet python=3.6

# press y a few times 
```   

7. Activate env   
``` bash
source activate vampnet  
```

8. update pip (might already be up to date, but just in case...)
```
pip install --upgrade pip
```

9. Install stable tensorflow with GPU support for python 3.6    
``` bash
pip install --upgrade tensorflow-gpu
``` 

10. Test tf install   

``` bash
python -c "import tensorflow as tf; tf.enable_eager_execution(); print(tf.reduce_sum(tf.random_normal([1000, 1000])))"
```

11. Clone deeptime repository into a directory of your choice
``` bash
cd 'your-directory'
git clone https://github.com/markovmodel/deeptime.git
```

12. cd into the deeptime/vampnet subfolder and install vampnet
``` bash
cd deeptime/vampnet
python setup.py test
python setup.py install
```

13. If you want to run the alanine dipeptide example, you'll also need to install the mdshare package to the download of the alanine trajectory files:
``` bash
conda install mdshare -c conda-forge
```

14. Since the examples are jupyter notebooks, so the jupyter package is needed to run them:
``` bash
conda install jupyter
```
