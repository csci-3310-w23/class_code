# Working with Kernel Modules

## Commands for Kernel Modules

1. In order to list all the kernel modules lsmod is used
	`sudo lsmod`

2. In order to get info about a specific kernel module, modinfo is used
	`sudo modinfo lp`

3. In order to remove a specific kernel module, rmmod is used
	`sudo rmmod lp`

4. In order to insert a specific kernel module, insmod is used
	`sudo insmod lp`

`sudo modprobe lp` can be used to load kernel module with dependencies.

## Creating your own Kernel Module

A Kernel module can be created using C code
```
#include<linux/init.h>
#include<linux/module.h>
#include<linux/kernel.h>


MODULE_LICENSE("GPL");

static int __init my_init_function(void) 
{ 
	printk(KERN_ALERT "This is my first kernel module\n");
	return 0;
} 
 
static void __exit my_exit_function(void) 
{ 
    printk(KERN_ALERT "Exiting from my first kernel module\n");
} 

module_init(my_init_function);
module_exit(my_exit_function);
```

In order to compile above file, a makefile is required, e.g., Makefile
```
CONFIG_MODULE_SIG=n
obj-m += hello_kernel.o

all:
	make -C /lib/modules/$(shell uname -r)/build M=$(PWD) modules
clean:
	make -C /lib/modules/$(shell uname -r)/build M=$(PWD) clean
```
7. to make the file, use `make` command at terminal

8. Use `sudo insmod hello_kernel.ko` to add kernel module to kernel. Use `sudo dmesg` to see messages on kernel console

9. Use `tail /var/log/syslog` to see last 10 entries in syslog

10. use `sudo rmmod hello_kernel` to remove module from the kernel.
