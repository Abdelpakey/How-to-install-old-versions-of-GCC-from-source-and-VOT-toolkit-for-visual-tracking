# How-to-install-old-versions-of-GCC-and-VOT-toolkit-for-visual-tracking
1- Download latest version of VOT [here] (https://github.com/votchallenge/vot-toolkit)
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
   
# 1- Prerequisites

Most Prerequisites packages are listed [her] (https://gcc.gnu.org/install/prerequisites.html)

However in my case  (Ubuntu oean 19.10) I needed to install only 
flex,
bison and
Binutils as follows
    
    
    sudo apt-get update
    sudo apt-get install flex
    sudo apt-get install bison
    
For Binutils as I read it'd be installed ins this path

      $ sudo mkdir -p /opt/cross
      $ sudo chown YURNAME /opt/cross
Check your linux architecture using:

     $ lscpu or  $ arch    
      wget http://mirror.sergal.org/gnu/binutils/binutils-2.33.1.tar.xz
      $ mkdir build-binutils
      $ cd build-binutils 
      $ ../binutils-binutils-2.33.1/configure --prefix=/opt/cross --target=x86-64-pc-linux-gnu --disable-multilib
      $ make -j 22 
      $ sudo make install
      $ cd ..
   
# 2- Downloading the source

This is the official mirro website chose the best server for you [her](https://gcc.gnu.org/mirrors.html), the parent link is from [here](http://www.gnu.org/prep/ftp.html)
In may case I selcted this [one](http://mirror.sergal.org/gnu/)
Download [GCC-6.3](http://mirror.sergal.org/gnu/gcc/gcc-6.3.0/gcc-6.3.0.tar.gz) manually or using:

      $ wget http://mirror.sergal.org/gnu/gcc/gcc-6.3.0/gcc-6.3.0.tar.gz
   
Then

      $ sudo apt-get update
      $ sudo apt-get install build-essential
      $ tar -xzf gcc-6.3.0.tar.gz
      $ cd gcc-6.3.0
      $ contrib/download_prerequisites
      $ cd ~
      $ mkdir build630
      $ cd build630
   
 # 3- Configuration   
   
    $ PWD/../gcc-6.3.0/configure --prefix=/usr/bin/gcc-6.3 --enable-languages=c,c++ --disable-multilib
   
# 4- Building

      $ make -j 22
      $ sudo make install
   
HERE IS THE TRICKY PART:

1- gcc-6.3.0 shipped with old #libsanitizer folder so this will be producing some errors during the compilation. All you can do is delete this folder and copy #libsanitizer from latest version such as gcc-9.2.0 of course after you download it as before and decopmress it then paste it in gcc-6.3.0

2- There are 2 more  files you have to modify :

   - ubsan.c located in /gcc-6.3.0/gcc as indicated by the patch [here](https://github.com/gcc-mirror/gcc/commit/c0c52589c6a7265e8fc6b77706a83d22aa1ef0ce#diff-5f4702564fc0b717cb4c82d7970a3ad4)

To  fix it do this:

        Delete line 1474 -  || xloc.file == '\0' || xloc.file[0] == '\xff'   
        Add  line 1474 +  || xloc.file[0] == '\0' || xloc.file[0] == '\xff'
     
   - md-unwind-support.h loacted in /build630/x86-64-pc-linux-gnu/md-unwind-support.h   
   Source for this solution [here](https://github.com/openwrt/packages/issues/7202)    
  Fix1:@line 61:

         if (*(unsigned char *)(pc+0) == 0x48
               && *(unsigned long long *)(pc+1) == RT_SIGRETURN_SYSCALL)
             {
                     // struct ucontext *uc_ = context->cfa;
                  ucontext_t *uc_ = context->cfa;
                     /* The void * cast is necessary to avoid an aliasing warning.
                        The aliasing warning is correct, but should not be a problem
                        because it does not alias anything.  */
                     sc = (struct sigcontext *) (void *) &uc_->uc_mcontext;
             }
Fix2:@line144

          {
            struct rt_sigframe {
         int sig;
         siginfo_t *pinfo;
         void *puc;
         siginfo_t info;
         // struct ucontext uc;
         ucontext_t uc;
            } *rt_ = context->cfa;

Finally, select the gcc, g++ you want after adding the installed gcc to the system

      $ sudo update-alternatives --install /usr/bin/g++ g++ /usr/bin/gcc-6.3/bin/g++ 16
      $ sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-6.3/bin/gcc 16
      
Configure your version 

       $ sudo update-alternatives --config gcc 
       $ sudo update-alternatives --config g++
       
if you want to delete a version

      $ sudo update-alternatives --remove gcc /usr/bin/gcc-6.3/bin/gcc
       
      
      
All you need to compile VOT toolkit is just replacing the vot.py, that comes with vot version 7-0-2,  by older version from 2018. That's it.

Sources for this article:

1- https://solarianprogrammer.com/2016/10/07/building-gcc-ubuntu-linux/

2- http://mirror.sergal.org/gnu/

3- https://preshing.com/20141119/how-to-build-a-gcc-cross-compiler/

4- https://ccm.net/faq/30635-how-to-install-flex-and-bison-under-ubuntu

5- https://gcc.gnu.org/install/

6- https://github.com/gcc-mirror/gcc

7- https://unix.stackexchange.com/questions/410723/how-to-install-a-specific-version-of-gcc-in-kali-linux

8- https://github.com/gcc-mirror/gcc/releases?after=gcc-7_2_0-release

9- https://archerfmy.github.io/2017/04/12/How-to-switch-your-gcc-g-version-in-ubuntu/

10- https://github.com/Abdelpakey/Install-CUDA--cuDNN-Tensorflow-on-Ubuntu-16

11- https://gcc.gnu.org/viewcvs/gcc/trunk/libgcc/config/i386/linux-unwind.h?r1=249731&r2=249730&pathrev=249731 


# Enjoy :)      
       
 
   

