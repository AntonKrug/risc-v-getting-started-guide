Running 64-bit RISC-V Linux on Microchip PolarFire SoC
======================================================

Microchip's `PolarFire SoC <https://www.microsemi.com/product-directory/soc-fpgas/5498-polarfire-soc-fpga>`_ is a Linux capable SoC.

It's a very good starting point if you want to get Linux running on a physical chip/board.


Prerequisites
-------------

Building Linux for the PolarFire SoC requires at least 7GB of RAM (more for multi-threaded builds), 20GB of HHD space and installed prerequisites. Recommended distributions are: Debian 9, RHEL/CentOS 7, Ubuntu 16.

Find instructions for Linux below:

.. tabs::

   .. tab:: RHEL/Centos

      .. code-block:: bash

         sudo yum update -y

         sudo yum install -y gcc-c++ ncurses-devel openssl-devel zlib-devel dtc \
                             autoconf automake autotools-dev bc bison build-essential curl flex \
                             gawk gdisk git gperf libgmp-dev libmpc-dev libmpfr-dev libtool \
                             patchutils python screen texinfo unzip patch wget vim-common

   .. tab:: Ubuntu/Debian

      .. code-block:: bash
         
         sudo apt-get update

         sudo apt-get install -y gcc cpp libncurses5-dev libssl-dev zlib1g-dev zlib1g device-tree-compiler \
                                 autoconf automake autotools-dev bc bison build-essential curl flex gawk \ 
                                 gdisk git gperf libgmp-dev libmpc-dev libmpfr-dev libtool patchutils python \
                                 screen texinfo unzip patch wget vim-common


Each distribution requires slightly different steps:

.. tabs::

   .. tab:: Ubuntu 14/16/17/18

      .. code-block:: bash

         sudo apt-get install -y linux-firmware


   .. tab:: Centos 6 (not recommended)
      .. code-block:: bash
         # Centos requires GCC 4.8
         sudo  wget http://people.centos.org/tru/devtools-2/devtools-2.repo -O /etc/yum.repos.d/devtools-2.repo
         sudo yum update
         sudo yum install -y devtoolset-2-gcc devtoolset-2-binutils devtoolset-2-gcc-c++

         # AMD radeon firmware
         sudo yum install -y xorg-x11-drv-ati-firmware

         #devtoolset-2 provides sudo wrapper which breaks current sudo, remove it
         # https://unix.stackexchange.com/questions/192809/sudo-i-returns-an-error
         # https://bugzilla.redhat.com/show_bug.cgi?id=1319936

         sudo rm /opt/rh/devtoolset-2/root/usr/bin/sudo 

         #Checking if the GCC 4.8 got installed
         sudo source scl_source enable devtoolset-2
         gcc --version

         #Make sure the GCC 4.8 is default and not the GCC 4.4"
         sudo echo "source scl_source enable devtoolset-2" >> /etc/bashrc

         # Device tree compiler is missing in default centos 6 repository, adding epel repository
         wget http://dl.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
         sudo rpm -ivh epel-release-6-8.noarch.rpm
         sudo yum update
         sudo yum install -y dtc


   .. tab:: Debian 8 (not recommended)
      .. code-block:: bash
         # Adding stretch backports for jessie
         sudo echo "deb http://http.debian.net/debian jessie-backports main contrib non-free" >> /etc/apt/sources.list.d/backports.list
         sudo apt-get update

         # Installing amd firmware
         sudo apt-get install -y firmware-amd-graphics


   .. tab:: Debian 9
      .. code-block:: bash
         # Adding non-free stretch repository to the apt sources
         sudo echo "deb http://deb.debian.org/debian stretch main non-free" >> /etc/apt/sources.list.d/nonfree.list
         sudo echo "deb-src http://deb.debian.org/debian stretch main non-free" >> /etc/apt/sources.list.d/nonfree.list
         sudo apt-get update

         #Installing amd firmware
         sudo apt-get install -y firmware-amd-graphics





Getting the sources
-------------------

Clone the official Microchip PolarFire SoC SDK repository (these steps will take a while):

.. code-block:: bash
    git clone https://github.com/Microsemi-SoC-IP/mpfs-linux-sdk.git
    cd mpfs-linux-sdk
    git submodule update --init --recursive


Building
--------

Simply run (it will take a while):

.. code-block:: bash
    # Make will download files from FTP site, in case the FTP will timeout run the command again
    make all 


