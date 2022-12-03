# CMPE283-Assignment-2 <br>

Modify the CPUID emulation code in KVM to report back additional information when special CPUID leaf nodes are requested.

 ### Implementation on  leafs 0x4FFFFFFC and 0x4FFFFFFD <br>
 
 #### Steps used to complete this assignment :
This is an individual submission.

Following are the development steps I have followed to develop the solution for assignment-2:  Adding leaf nodes 0x4ffffffc and 0x4ffffffd to the CPUID emulation code in KVM.

I have forked the Linux Kernel  https://github.com/torvalds/linux.git  to me GitHub account

Checkout the linux kernel code from my github repository 
                       git clone https://github.com/hegdebhavya/linux.git 

The new kernel code that is checked-out here is 6.1.0-rc6+

Fig: 1_Linux_Checkout_repo

The current Linux Kernel version is as below,

Fig: 2_Linux_version

We now install all the libraries and tools we would need to build the new Linux Kernel.

sudo apt install gcc make bison flex pkg-config 
sudo apt-get install qtbase5-dev qtchooser qt5-qmake qtbase5-dev-tools g++ libelf-dev

Next we make the config file for our kernel build 

make xconfig

I have selected the default options for xconfig. Once this command is executed we start the make process
 	make

Once the make is complete, 
sudo make modules_install
sudo make install

Next, we configure the grub loader, to check the exact kernel version name you can navigate to the /lib/modules directory and get the version, with this you can the commands below, 

Fig3_GRUB_config

sudo update-initramfs -c -k 6.1.0-rc6+
sudo update-grub

I have modified the files /linux/arch/x86/kvm/cpuid.c and /linux/arch/x86/kvm/vmx/vmx.c to add the required variables and logic to add support for cpuid leaf nodes 0x4ffffffc and 0x4ffffffd. These changes are committed to the current GitHub repository and commits can be seen here.

To test the cpuid modifications we will now install virt-manager and run a 32-bit Ubuntu Virtual 

sudo apt install virt-manager

Once virt-manager is installed we can create a new 32-bit  Ubuntu VM in my case I have tested with Ubuntu 

We can see the 32-bit inner VM in the screenshot below,


Next, we can download a test program to test the cpuid leaf nodes but downloading cpuid from,
http://archive.ubuntu.com/ubuntu/pool/universe/c/cpuid/cpuid_20170122.orig.tar.gz
Once the archive is extracted we can navigate to the extracted directory and run the make command to build the program.

The check the Total number of Exits we can run the cpuid command with leadnode 0x4FFFFFFC with command below,

./cpuid -l 0x4ffffffc    

Here, the leaf node is specified with “-l” argument.

We see the total number of exit counts increasing and the values are returned in the eax register. The leafnode and the increasing exit counts are highlighted in the screenshot above.

The check the Total time taken to process these exits we can run the cpuid command with leadnode 0x4FFFFFFD with command below,



We see the total processing time for exits increasing and the values are returned in registers ebx and ecx. The leafnode and the increasing values returned are highlighted in the screenshot above. The high 32-bits of total time spent are returned in register ebx and the low 32-bit of the total time spent are returned in the register ecx.
