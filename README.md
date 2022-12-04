# CMPE283-Assignment-2 <br>

## Modify the CPUID emulation code in KVM to report back additional information when special CPUID leaf nodes are requested. 

 ### Implementation of CPUID leaf nodes 0x4FFFFFFC and 0x4FFFFFFD <hr style="border:2px solid gray">

 
 ### Question 2: 
 
 #### Steps used to complete this assignment : 


Following are the development steps I have followed to develop the solution for assignment-2:  Adding leaf nodes 0x4ffffffc and 0x4ffffffd to the CPUID emulation code in KVM.

* Fork the Linux Kernel  https://github.com/torvalds/linux.git  

* Checkout the linux kernel code from my github repository 
                       git clone https://github.com/hegdebhavya/linux.git 

 The new kernel code that is checked-out here is **6.1.0-rc6+**

![1_Linux_repo_Checkout](https://user-images.githubusercontent.com/85700971/205465167-2a3b5eb1-6a2f-4c2b-b249-a8bfbdc8ad08.png)


 The current Linux Kernel version is as below,

![2_Current_Liux_Version](https://user-images.githubusercontent.com/85700971/205465173-c2a1e955-7a6d-4165-b528-a1751448835f.png)




* We now install all the libraries and tools we would need to build the new Linux Kernel.

```
sudo apt install gcc make bison flex pkg-config 
sudo apt-get install qtbase5-dev qtchooser qt5-qmake qtbase5-dev-tools g++ libelf-dev

```

* Next we make the config file for our kernel build 

```
make xconfig
```

* I have selected the default options for xconfig. Once this command is executed we start the make process
 ``` 
 make
 ```

* Once the make is complete, 
```
sudo make modules_install
sudo make install
```
* Next, we configure the grub loader, to check the exact kernel version name you can navigate to the /lib/modules directory and get the version, with this you can run commands below, 


```

sudo update-initramfs -c -k 6.1.0-rc6+
sudo update-grub

```

![3_GRUB_config](https://user-images.githubusercontent.com/85700971/205465182-5cfcefa6-3828-4c55-884c-dbe554d54d67.png)




* Next, we reboot the system and run **uname -r** to check the new linux kernel version

![8_Linux_Kernel_New_version](https://user-images.githubusercontent.com/85700971/205470726-bf50c73c-5bbe-4e6c-872c-ad25e5df5504.png)



We observe that the Linux version has been upgraded to **6.1.0-rc6+** from the previous **5.15.0-53-generic**.

  *  Next, I have modified the files /linux/arch/x86/kvm/cpuid.c and /linux/arch/x86/kvm/vmx/vmx.c to add the required variables and logic to add support for cpuid leaf nodes 0x4ffffffc and 0x4ffffffd. These changes are committed to the current GitHub repository and commits can be seen [b0f1c18]

* After the files are modified we build the modules again by running following commands

```
make modules
```
![10_makemodule](https://user-images.githubusercontent.com/85700971/205470767-bc6913fa-17de-4c68-8b07-55f699e6bfe9.png)

* Once make completes please install the modules using command below,
```
make INSTALL_MOD_STRIP=1 modules_install
```

* We then reload the kvm modules using following commands
```
sudo rmmod kvm_intel
sudo rmmod kvm
sudo modprobe kvm_intel
sudo modprobe kvm
```

![9_modulereload](https://user-images.githubusercontent.com/85700971/205470806-5e1e4330-1c56-4991-9b67-5644c654af18.png)


* To test the cpuid modifications we will now install virt-manager and run a 32-bit Ubuntu Virtual 
```
sudo apt install virt-manager
```

* Once virt-manager is installed we can launch the virtual machine manager by running command **virt-manager** create a new 32-bit  Ubuntu VM in my case I have tested with Ubuntu 

![4_innervm_specs](https://user-images.githubusercontent.com/85700971/205465206-057c6326-3cc9-4653-9743-cfff4e069701.png)

 We can see the 32-bit inner VM in the screenshot below,

![5_Screenshot_innervm](https://user-images.githubusercontent.com/85700971/205465239-e1420982-da06-4241-b63e-1d833caef6d1.png)



* Next, we can download a test program to test the cpuid leaf nodes but downloading cpuid from,
http://archive.ubuntu.com/ubuntu/pool/universe/c/cpuid/cpuid_20170122.orig.tar.gz
Once the archive is extracted we can navigate to the extracted directory and run the make command to build the program.

The check the Total number of Exits we can run the cpuid command with leadnode 0x4FFFFFFC with command below,

```

./cpuid -l 0x4ffffffc    

```

Here, the leaf node is specified with “-l” argument.

![6_Output_leaf_0x4ffffffc](https://user-images.githubusercontent.com/85700971/205465296-bd3aab18-b309-4be3-997c-1cb7cf7cc1ff.png)


We see the total number of exit counts increasing and the values are returned in the eax register. The leafnode and the increasing exit counts are highlighted in the screenshot above.

* To check the Total time taken to process these exits we can run the cpuid command with leadnode 0x4FFFFFFD with command below,

![7_Output_leaf_0x4ffffffd](https://user-images.githubusercontent.com/85700971/205465303-c84fbda4-26fe-44c5-9868-8975bfcefd26.png)



We see the total processing time for exits increasing and the values are returned in registers ebx and ecx. The leafnode and the increasing values returned are highlighted in the screenshot above. The high 32-bits of total time spent are returned in register ebx and the low 32-bit of the total time spent are returned in the register ecx.
