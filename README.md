# How-to-install-old-versions-of-GCC-and-VOT-toolkit-for-visual-tracking
1- Download latest version of VOT [here] [https://github.com/votchallenge/vot-toolkit]
- Matlab 2019a alwys crashs so best practice to use Matlab  2017a or 2018a.
- Matlab 2017a uses GCC 4.9 while Matlab-2019a uses GCC-6.3
2- Install Matlab let say 2018a version
3- Install GCC-6.3

# How to install GCC-6.3 or any older version

Installing GCC has steps
   1- Prerequisites
   2- Downloading the source
   3- Configuration
   4- Building
   
# Prerequisites

Most Prerequisites packages are listed [her] https://gcc.gnu.org/install/prerequisites.html

However in my case  (Ubuntu oean 19.10) I needed to install only 
flex,
bison and
Binutils as follows
    
    
    sudo apt-get update
    sudo apt-get install flex
    sudo apt-get install bison

