Download link :https://programming.engineering/product/p-1-chapter-2-operating-system-structures/

# P-1-Chapter-2-Operating-System-Structures
P-1 Chapter 2 Operating-System Structures
Programming Problems

2.24 In Section 2.3, we described a program that copies the contents of one file to a destination file. This program works by first prompting the user for the name of the source and destination files. Write this program using either the POSIX or Windows API. Be sure to include all necessary error checking, including ensuring that the source file exists.

Once you have correctly designed and tested the program, if you used a system that supports it, run the program using a utility that traces sys-tem calls. Linux systems provide the strace utility, and macOS systems use the dtruss command. (The dtruss command, which actually is a front end to dtrace, requires admin privileges, so it must be run using sudo.) These tools can be used as follows (assume that the name of the executable file is FileCopy:

Linux:

strace ./FileCopy

macOS:

sudo dtruss ./FileCopy

Since Windows systems do not provide such a tool, you will have to trace through the Windows version of this program using a debugger.

Programming Projects

Introduction to Linux Kernel Modules

In this project, you will learn how to create a kernel module and load it into the Linux kernel. You will then modify the kernel module so that it creates an entry in the /proc file system. The project can be completed using the Linux virtual

machine that is available with this text. Although you may use any text editor to write these C programs, you will have to use the terminal application to

compile the programs, and you will have to enter commands on the command line to manage the modules in the kernel.

As you’ll discover, the advantage of developing kernel modules is that it is a relatively easy method of interacting with the kernel, thus allowing you

to write programs that directly invoke kernel functions. It is important for you to keep in mind that you are indeed writing kernel code that directly interacts

with the kernel. That normally means that any errors in the code could crash the system! However, since you will be using a virtual machine, any failures will at worst only require rebooting the system.

Programming Projects

P-2

I. Kernel Modules Overview

The first part of this project involves following a series of steps for creating and inserting a module into the Linux kernel.

You can list all kernel modules that are currently loaded by entering the command

lsmod

This command will list the current kernel modules in three columns: name, size, and where the module is being used.

#include <linux/init.h>

#include <linux/kernel.h>

#include <linux/module.h>

/* This function is called when the module is loaded. */ int simple init(void)

{

printk(KERN INFO “Loading Kernel Module∖n”);

return 0;

}

/* This function is called when the module is removed. */ void simple exit(void)

{

printk(KERN INFO “Removing Kernel Module∖n”);

}

/* Macros for registering module entry and exit points. */

module init(simple init);

module exit(simple exit);

MODULE LICENSE(“GPL”);

MODULE DESCRIPTION(“Simple Module”);

MODULE AUTHOR(“SGG”);

Figure 2.21 Kernel module simple.c.

The program in Figure 2.21 (named simple.c and available with the source code for this text) illustrates a very basic kernel module that prints appropriate messages when it is loaded and unloaded.

The function simple init() is the module entry point, which represents

the function that is invoked when the module is loaded into the kernel. Simi-larly, the simple exit() function is the module exit point— the function that

is called when the module is removed from the kernel.

P-3 Chapter 2 Operating-System Structures

The module entry point function must return an integer value, with 0 representing success and any other value representing failure. The module exit point function returns void. Neither the module entry point nor the module exit point is passed any parameters. The two following macros are used for registering the module entry and exit points with the kernel:

module init(simple init)

module exit(simple exit)

Notice in the figure how the module entry and exit point functions make calls to the printk() function. printk() is the kernel equivalent of printf(), but its output is sent to a kernel log buffer whose contents can be read by the dmesg command. One difference between printf() and printk() is that printk() allows us to specify a priority flag, whose values are given in the

<linux/printk.h> include file. In this instance, the priority is KERN INFO, which is def ined as aninformational message.

The final lines — MODULE LICENSE(), MODULE DESCRIPTION(), and MOD-ULE AUTHOR()— represent details regarding the software license, description of the module, and author. For our purposes, we do not require this infor-mation, but we include it because it is standard practice in developing kernel modules.

This kernel module simple.c is compiled using the Makefile accom-panying the source code with this project. To compile the module, enter the following on the command line:

make

The compilation produces several files. The file simple.ko represents the compiled kernel module. The following step illustrates inserting this module into the Linux kernel.

II. Loading and Removing Kernel Modules

Kernel modules are loaded using the insmod command, which is run as fol-lows:

sudo insmod simple.ko

To check whether the module has loaded, enter the lsmod command and search for the module simple. Recall that the module entry point is invoked when the module is inserted into the kernel. To check the contents of this message in the kernel log buffer, enter the command

dmesg

You should see the message “Loading Module.“

Removing the kernel module involves invoking the rmmod command (notice that the .ko suffix is unnecessary):

(perform these steps in simple.c file)

Programming Projects

P-4

Be sure to check with the dmesg command to ensure the module has been removed.

Because the kernel log buffer can fill up quickly, it often makes sense to clear the buffer periodically. This can be accomplished as follows:

sudo dmesg -c

Proceed through the steps described above to create the kernel module and to load and unload the module. Be sure to check the contents of the kernel log buffer using dmesg to ensure that you have followed the steps properly.

As kernel modules are running within the kernel, it is possible to obtain values and call functions that are available only in the kernel and not to regular user applications. For example, the Linux include file <linux/hash.h> defines several hashing functions for use within the kernel. This file also defines the constant value GOLDEN RATIO PRIME (which is defined as an unsigned long). This value can be printed out as follows:

printk(KERN INFO “%lu∖n”, GOLDEN RATIO PRIME);

As another example, the include file <linux/gcd.h> defines the following function

unsigned long gcd(unsigned long a, unsigned b);

which returns the greatest common divisor of the parameters a and b.

Once you are able to correctly load and unload your module, complete the

following additional steps:

Print out the value of GOLDEN RATIO PRIME in the simple init() func-tion.

Print out the greatest common divisor of 3,300 and 24 in the sim-ple exit() function.

As compiler errors are not often helpful when performing kernel development, it is important to compile your program often by running make regularly. Be sure to load and remove the kernel module and check the kernel log buffer using dmesg to ensure that your changes to simple.c are working properly.

In Section 1.4.3, we described the role of the timer as well as the timer interrupt handler. In Linux, the rate at which the timer ticks (the tick rate) is the value HZ defined in <asm/param.h>. The value of HZ determines the frequency of the timer interrupt, and its value varies by machine type and architecture. For example, if the value of HZ is 100, a timer interrupt occurs 100 times per second, or every 10 milliseconds. Additionally, the kernel keeps track of the global variable jiffies, which maintains the number of timer interrupts that have occurred since the system was booted. The jiffies variable is declared in the file <linux/jiffies.h>. (Perform these steps in simple.c file)

Print out the values of jiffies and HZ in the simple init() function.

Print out the value of jiffies in the simple exit() function.

Save a screenshot of the dmesg result after inserting and removing simple.ko. Screenshot should be inserted under Part I (simple.c) in the answer_sheet file. Run ‘sudo dmesg -c’ before running ‘dmesg’. The result should contain these statements: “Loading Module”, [Value of golden ratio], [Value of HZ], [Value of jiffies when inserting module], “Removing Module”, [Value of GCD of 3300 and 24], [Value of jiffies at module removal]

P-5 Chapter 2 Operating-System Structures

Before proceeding to the next set of exercises, consider how you can use the different values of jiffies in simple init() and simple exit() to determine the number of seconds that have elapsed since the time the kernel module was loaded and then removed.

III. The /proc File System

The /proc file system is a“pseudo” file system that exists only in kernel mem-ory and is used primarily for querying various kernel and per-process statistics.

#include <linux/init.h>

#include <linux/kernel.h>

#include <linux/module.h>

#include <linux/proc fs.h>

#include <asm/uaccess.h>

#define BUFFER SIZE 128

#define PROC NAME “hello”

ssize t proc read(struct file *file, char user *usr buf, size t count, loff t *pos);

static struct file operations proc ops = {

.owner = THIS MODULE,

.read = proc read,

};

/* This function is called when the module is loaded. */ int proc init(void)

{

/* creates the /proc/hello entry */

proc create(PROC NAME, 0666, NULL, &proc ops);

return 0;

}

/* This function is called when the module is removed. */ void proc exit(void)

{

/* removes the /proc/hello entry */ remove proc entry(PROC NAME, NULL);

}

Figure 2.22 The /proc file-system kernel module, Part 1

This exercise involves designing kernel modules that create additional entries in the /proc file system involving both kernel statistics and information related

Programming Projects

P-6

to specific processes. The entire program is included in Figure 2.22 and Figure 2.23. You will need to change the Make file by replacing simple.o with hello.o. Run make again.

We begin by describing how to create a new entry in the /proc file sys-tem. The following program example (named hello.c and available with the source code for this text) creates a /proc entry named /proc/hello. If a user enters the command

cat /proc/hello

the infamous Hello World message is returned.

/* This function is called each time /proc/hello is read */ ssize t proc read(struct file *file, char user *usr buf,

size t count, loff t *pos)

{

int rv = 0;

char buffer[BUFFER SIZE];

static int completed = 0;

if (completed) {

completed = 0;

return 0;

}

completed = 1;

rv = sprintf(buffer, “Hello World∖n”);

/* copies kernel space buffer to user space usr buf */ copy to user(usr buf, buffer, rv);

return rv;

}

module init(proc init);

module exit(proc exit);

MODULE LICENSE(“GPL”);

MODULE DESCRIPTION(“Hello Module”);

MODULE AUTHOR(“SGG”);

Figure 2.23 The /proc file system kernel module, Part 2

In the module entry point proc init(), we create the new /proc/hello entry using the proc create() function. This function is passed proc ops, which contains a reference to a struct file operations. This struct initial-

P-7 Chapter 2 Operating-System Structures

izes the .owner and .read members. The value of .read is the name of the function proc read() that is to be called whenever /proc/hello is read.

Examining this proc read() function, we see that the string “Hello World∖n” is written to the variable buffer where buffer exists in kernel mem-ory. Since /proc/hello can be accessed from user space, we must copy the contents of buffer to user space using the kernel function copy to user(). This function copies the contents of kernel memory buffer to the variable usr buf, which exists in user space.

Each time the /proc/hello file is read, theproc read() function is called repeatedly until it returns 0, so there must be logic to ensure that this func-tion returns 0 once it has collected the data (in this case, the string “Hello World∖n”) that is to go into the corresponding /proc/hello file.

Finally, notice that the /proc/hello file is removed in the module exit point proc exit() using the function remove proc entry().

IV. Assignment

This assignment will involve designing two kernel modules:

Design a kernel module that creates a /proc file named/proc/jiffies that reports the current value of jiffies when the /proc/jiffies file is read, such as with the command

cat /proc/jiffies

Be sure to remove /proc/jiffies when the module is removed.

Design a kernel module that creates a proc file named/proc/seconds that reports the number of elapsed seconds since the kernel module was loaded. This will involve using the value of jiffies as well as the HZ rate. When a user enters the command

cat /proc/seconds

your kernel module will report the number of seconds that have elapsed since the kernel module was first loaded. Be sure to remove /proc/seconds when the module is removed.

Task ‘jiffies’ should be done in a new file called jiffies.c. It might be a good idea to take starting code from the given hello.c file for this task. After you are done with this task, attach a screenshot of the result under part II (jiffies) of the answer sheet.

A sample command structure for the screenshot would look like: sudo dmesg -c, make, sudo insmod jiffies.ko, cat /proc/jiffies, sudo rmmod jiffies, dmesg

Task ‘seconds’ should be done in a new file called seconds.c. It might be a good idea to take starting code from the given hello.c file for this task. After you are done with this task, attach a screenshot of the result under part III (seconds) of the answer sheet.

A sample command structure for the screenshot would look like: sudo dmesg -c, make, sudo insmod seconds.ko, cat /proc/seconds, cat /proc/seconds, cat /proc/seconds, sudo rmmod seconds, dmesg

The answer sheet should be renamed as answers_[roll no].pdf. For example, if your roll number is 21100115, then the file should be named ‘answers_21100115.pdf’. This answer sheet should contain a screenshot from each of the three tasks. Final submission should contain the following files: simple.c, jiffies.c, seconds.c, answers_[roll no].pdf

Compress the 4 files as zip. Rename the zipped file as “assignment1_[roll no].zip”. Submit only this zipped file on LMS.
