# Lab 7: System Calls and Processes

You need to complete [LAB 6](./LAB6.html)  in order
to do this lab.

**Note:** this lab runs over two weeks (Week 22 and 23).

**Remote Access:** If you cannot run the lab on your local machine, you may want to use the Linux
Lab Machine remotely. To do so follow [the online instructions](https://uob.sharepoint.com/sites/itservices/SitePages/fits-engineering-linux-x2go.aspx).
If you experience difficulty contact IT service.

## Fill our survey

<iframe width="640px" height= "480px" src= "https://forms.office.com/Pages/ResponsePage.aspx?id=MH_ksn3NTkql2rGM8aQVG5N9pWWUNd5Khd6GR62JgsZUMEZKRUhXRklNT1VKMTJaV0taWkFZUlhPSC4u&embed=true" frameborder= "0" marginwidth= "0" marginheight= "0" style= "border: none; max-width:100%; max-height:100vh" allowfullscreen webkitallowfullscreen mozallowfullscreen msallowfullscreen> </iframe>

## Organization

You should keep the students' pair formed at the start of [Lab 5](./LAB5.html).
This lab will be extremely challenging and we are incorporating a huge element
of peer learning. There is two parts to this lab: **design** and **implementation**.
Both are extremely important.

Within your TA group find another pair of students. You should schedule time
(**on your own**, this does not appear in your time table) to
review and discuss each other work. The goal is to: help each other, be critical
but friendly. You will get the most out of this lab if you do all of it seriously
including the peer reviews.

We make the following suggestions on when to meet:

|         | Lab (Tuesday)         | Review (Friday)                          |
|---------|-----------------------|------------------------------------------|
| Week 22 | Code Reading & Design | Review design document                   |
| Week 23 | Start implementation  | Review Menu Change & File System Support |
| Week 24 | N/A                   | Review Process Support                   |

We also invite you to have discussions with your TA during the scheduled lab
time.

In short, **work and learn together!**

## Introduction

In this assignment you will add process and system call support to your
OS/161 kernel.

Currently no support exists for running user processes—​the tests you have run up
to this point have run in the kernel as kernel threads. By the time you finish
LAB 7 you will have the ability to launch a simple shell and enter a
somewhat-familiar UNIX environment. Indeed, future tests will be run as user
processes, not from the kernel menu.

In contrast to [LAB 6](./LAB6.html), LAB 7 requires a great deal more thought
and planning. You will be writing a lot more code. We are not giving you many
examples. We have not designed your data structures for you. You will need
to determine what needs to be implemented, where to put the code required,
how the data structures are designed and implemented, and how everything fits
together. We just care that your system calls meet the interface specification.

As a final piece of advice, you will produce code bases that are
large, complex, and potentially very difficult to debug. You do not want to
introduce bugs into your kernel because they will be very hard to remove. Our
advice—​slow down, design, think, design again, discuss with your partner,
and slow down again. Then write some code.

## Lab objectives

In this lab, you must implement the interface between user-mode programs—​or user
land—​and the kernel. As usual, we provide part of the code you will need.
Your job is to identify, design and build the missing pieces.

The first step is to read and understand the parts of the system that we have
written for you. Our code can run one user-level C program at a time as long as
it doesn’t want to do anything but shut the system down. We have provided sample
user programs that do this (`sbin/{reboot,halt,poweroff`), as well as others that
make use of features you will be adding in this and future assignments.

So far, all the code you have written for OS/161 has only been run within, and
only been used by, the operating system kernel. In a real operating system, the
kernel’s main function is to provide support for user-level programs. Most such
support is accessed via system calls.

However, before looking at how to implement system calls, we first walk you
through how the kernel gets to run. Remember, that the kernel is just like most
programs except that it is the first program that gets to run.

## Design documents

The design document is a very important part of this lab. You should discuss it
in depth with your partner, discuss among the tuples within your TA groups, and
with your TA. While the lab are not assessed this year, doing this exercise properly
is extremely important to get the most out of this unit.

A design document should clearly reflect the development of your solution, not
merely explain what you programmed. If you try to code first and design later,
or even if you design hastily and rush into coding, you will most certainly end
up confused and frustrated. Don’t do it! Work with your partner to plan
everything you will do. Don’t even think about coding until you can precisely
explain to each other what problems you need to solve and how the pieces relate
to each other.

Note that it can often be hard to write (or talk) about new software design—​you
are facing problems that you have not seen before, and therefore even finding
terminology to describe your ideas can be difficult. There is no magic solution
to this problem, but it gets easier with practice. The important thing is to go
ahead and try. Always try to describe your ideas and designs to your partner.
In order to reach an understanding, you may have to invent terminology and
notation. This is fine, just be sure to document it in your design. If you do
this, by the time you have completed your design, you will find that you have
the ability to efficiently discuss problems that you have never seen before.

Your design document can be as long as you like. It should include both English
definitions and explanations of core functions and interfaces as well as
pseudocode and function definitions where appropriate. We suggest that you use a
markup language to format your design nicely (e.g. an MD file in a github repo).

The contents of your design document should include (but not be limited to):
1. A description of each new piece of functionality you need to add for Lab 7.
2. A list and brief description of any new data structures you will have to add to the system.
3. Indications of what, if any, existing code you may model your solution off of.
4. A description of how accesses to shared state will be synchronized, if necessary.

## (Re)watch Videos

<iframe width="640" height="360" src="https://web.microsoftstream.com/embed/video/d9879d11-4174-4df8-accc-6e927277ab09?autoplay=false&amp;showinfo=true" allowfullscreen style="border:none;"></iframe>

<iframe width="640" height="360" src="https://web.microsoftstream.com/embed/video/4bf750d6-6201-4196-9abd-f9865b72fd4d?autoplay=false&amp;showinfo=true" allowfullscreen style="border:none;"></iframe>

<iframe width="640" height="360" src="https://web.microsoftstream.com/embed/video/560e7b66-2dfa-41bd-b155-2b92cf27b940?autoplay=false&amp;showinfo=true" allowfullscreen style="border:none;"></iframe>

<iframe width="640" height="360" src="https://web.microsoftstream.com/embed/video/4dd69f84-d864-4c1a-a72c-9515b7ec2678?autoplay=false&amp;showinfo=true" allowfullscreen style="border:none;"></iframe>

**Note:** if you forgot some of the concepts, you my want to (re)watch the
associated video series (or have look in the textbook).

## Code reading

To help you get started designing, we have provided the following questions as
a guide for reading through the code.

We recommend that you and your partner look over and answer the code reading
questions together. You may want to start by reviewing the questions separately,
but then meet to talk over your answers and look at the code together. Once you
have done this, you should be ready to discuss strategy for designing your code
for this assignment. A big part of this lab is figuring out what to do, not just
how to do it.

### Existing Process Support

The key files that are responsible for the loading and running of user-level
programs are `loadelf.c`, `runprogram.c`, and `uio.c`, although you may want to add
more of your own during this assignment. Understanding these files is the key
to getting started with the implementation Note that to answer some of the
questions, you will have to look in other files.

#### `kern/syscall/loadelf.c`

This file contains the functions responsible for loading an [ELF 4](https://linuxhint.com/understanding_elf_file_format/){:target="_blank"} executable
from the file system and into virtual memory space. Of course, at this point
this virtual memory space does not provide what is normally meant by virtual
memory—​although there is translation between virtual and physical addresses,
there is no mechanism for providing more memory than exists physically.
Don’t worry about it.

#### `kern/syscall/runprogram.c`

This file contains only one function, `runprogram`, which is responsible for
running a program from the kernel menu. It is a good base for writing the
`execv` system call, but only a base—​when writing your design doc, you should
determine what more is required for `execv` that `runprogram()` does not
concern itself with. Additionally, once you have designed your process system,
`runprogram` should be altered to start processes properly within this framework.
For example, a program started by runprogram should have the standard file
descriptors available while it’s running.

#### `kern/lib/uio.c`

This file contains functions for moving data between kernel and user space.
Knowing when and how to cross this boundary is critical to properly implementing
user programs, so this is a good file to read very carefully. You should also
examine the code in `kern/vm/copyinout.c`.

#### Questions

1. What are the ELF magic numbers?
2. What is the difference between `UIO_USERISPACE` and `UIO_USERSPACE`? When should one use `UIO_SYSSPACE` instead?
3. Why can the `struct uio` that is used to read in a segment be allocated on the stack in `load_segment`? Or, put another way, where does the memory read actually go?
4. In `runprogram` why is it important to call `vfs_close` before going to user mode?
5. What function forces the processor to switch into user mode? Is this function machine dependent?
6. In what files are `copyin`, `copyout`, and `memmove` defined? Why are `copyin` and `copyout` necessary? (as opposed to just using `memmove`)
7. What is the purpose of `userptr_t`?

### `kern/arch/mips` Traps and System Calls

Exceptions are the key to operating systems. They are the mechanism that enables
the operating system to regain control of execution and therefore do its job.
You can think of exceptions as the interface between the processor and the
operating system. When the OS boots, it installs an exception handler, usually
carefully crafted assembly code, at a specific address in memory. When the
processor raises an exception, it invokes this, which sets up a trap `frame<S`
and calls into the operating system. Since `_exception` is such an overloaded term
in computer science, operating system lingo for an exception is a trap, when
the OS traps execution. Interrupts are exceptions, and more significantly for
this assignment, so are system calls. Specifically,
`kern/arch/mips/syscall/syscall.c` handles traps that happen to be system calls.
Understanding this code is key, so we highly recommend reading through it carefully.

#### `locore/trap.c`

`mips_trap` is the key function for returning control to the operating system.
This is the C function that gets called by the assembly exception handler.
`enter_new_process` is the key function for returning control to user programs.
`kill_curthread` is the function for handling broken user programs. When the
processor is in user mode and hits something it can’t handle (e.g. ​a bad instruction
or divide-by-zero) ​it raises an exception. There’s no way to
recover from this, so the OS needs to kill off the process.
Part of this assignment is writing a useful version of this function.

#### `syscall/syscall.c`

`syscall` is the function that delegates the actual work of a system call to
the kernel function that implements it. Notice that reboot is the only case
currently handled. You will also find a function, `enter_forked_process`, which
is a stub where you will place your code to implement the fork system call.
It should get called from `sys_fork`.

#### Questions

1. What is the numerical value of the exception code for a MIPS system call?
2. How many bytes is an instruction in MIPS? Try to answer this by reading `syscall` carefully, not by looking somewhere else.
3. Why do you probably want to change the implementation of `kill_curthread`?
4. What would be required to implement a system call that took more than 4 arguments?

### Support Code for User Programs

As important as the kernel implementation of system calls is the user space code
that allows C programs to use them. This code can be found under the `userland/`
directory at the top of your OS/161 source tree. Note that you can complete this
lab without modifying user-level code, and you should not break any
interface conventions already present—don’t swap the order of the `argc` and
`argv *` arguments to main, for example. However, it is useful to understand
how this code works and how it implements the other side of the system call
interface.

#### `userland/lib/crt0/mips/`

This is the user program startup code. There’s only one file in here,
`mips-crt0.S`, which contains the MIPS assembly code that receives control
first when a user-level program is started. It calls the user program’s main.
This is the code that your execv implementation will be interfacing to, so be
sure to check what values it expects to appear in what registers and so forth.

#### `userland/lib/libc/`

This is the user-level C library. There’s obviously a lot of code here.
We don’t expect you to read it all, although it may be instructive in the long
run to do so. For present purposes you need only look at the code that
implements the user-level side of system calls, which we detail below.

#### `userland/lib/libc/unix/errno.c`

This is where the global variable errno is defined.

#### `userland/lib/libc/arch/mips/syscalls-mips.S`

This file contains the machine-dependent code necessary for implementing the
user-level side of MIPS system calls.

#### `build/userland/lib/libc/syscalls.S`

This file is created from `syscalls-mips.S` at compile time and is the actual
file assembled into the C library. The actual names of the system calls are
placed in this file using a script called `syscalls/gensyscalls.sh` that reads
them from the kernel’s header files. This avoids having to make a second list
of the system calls. In a real system, typically each system call stub is
placed in its own source file, to allow selectively linking them in.
OS/161 puts them all together to simplify the build.

#### Questions

1. What is the purpose of the SYSCALL macro?
2. What is the MIPS instruction that actually triggers a system call? (Answer this by reading the source in this directory, not looking somewhere else.)
3. OS/161 supports 64 bit values, `lseek` takes and returns a 64 bit offset value. Thus, `lseek` takes a 32 bit file handle (`arg0`), a 64 bit offset (`arg1`), a 32 bit whence (`arg3`), and needs to return a 64 bit offset. In `void syscall(struct trapframe *tf)` where will you find each of the three arguments (in which registers) and how will you return the 64 bit offset?

## Implementation

The full range of system calls that we think you might want is listed in
`kern/include/kern/syscall.h`. For this assignment you should implement:

Process support: `getpid`, `fork`, `execv`, `waitpid`, and `_exit`.

File system support: `open`, `read`, `write`, `lseek`, `close`, `dup2`, `chdir`, and `__getcwd`.

It’s crucial that your system calls handle all error conditions gracefully
without crashing your kernel. You should consult the OS/161 man pages included
in the distribution under `man/syscall` and understand fully the system calls
that you must implement. You must return the error codes as described in
the man pages.

Additionally, your system calls must return the correct value (in case of success)
or error code (in case of failure) as specified in the man pages.

The file `userland/include/unistd.h` contains the user-level interface
definition of the system calls that you will be writing for OS/161. This
interface is different from that of the kernel functions that you will define
to implement these calls. You need to design this interface and put it in
`kern/include/syscall.h`. As you discovered in [Lab 5](./LAB5.html), the integer
codes for the calls are defined in `kern/include/kern/syscall.h`.

You need to think about a variety of issues associated with implementing system
calls. Perhaps the most obvious one: can two different user-level processes
(or user-level threads, if you choose to implement them) find themselves
running a system call at the same time? Be sure to argue for or against this,
and explain your final decision in your design document.
You may need some of the synchronization primitives you implemented during
[LAB 6](./LAB6.html).

### Kernel Menu Changes

A small but important part of LAB 7 is improving the relationship between the
kernel menu thread and the thread that it creates that will run your shell
(via s) or user programs (via p). Currently the menu thread does not coordinate
with that it forks to go to user space. This means that the kernel menu will
immediately return to the top of its loop, redraw the prompt, and begin trying
to read terminal input.

If the user program that was launched is also trying to read from the terminal
(like `/bin/shell`), it will compete with the kernel menu thread for input.
If you find yourself having to type `//bbiinn//ttrruuee` to run `/bin/true`,
this what is happening. If the user program is just generating output, you
will notice that the kernel prompt is printed and interleaved with its output.

Given that this makes it impossible to correctly interact with the shell ​fixing
this problem is a part of LAB 7. There are multiple approaches that will work.
If you consider this while designing `sys_wait` and `sys_exit`, it is possible
to reuse that approach without duplicating any code. Alternatively, you can
fix this problem before implementing any of your system calls by using some of
the synchronization primitives you became familiar with during [LAB 6](./LAB6.html).

### File System Support

For any given process, the first file descriptors (0, 1, and 2) are considered
to be standard input (`stdin`), standard output (`stdout`), and standard error
(`stderr`). These file descriptors should start out attached to the
console device (`"con:"`), but your implementation must allow programs to use
dup2 to change them to point elsewhere.

Although these system calls may seem to be tied to the file system, in fact,
they are really about manipulation of file descriptors, or process-specific
file system state. A large part of this assignment is designing and
implementing a system to track this state. Some of this information—​such as
the current working directory—​is specific only to the process, but other
information—​such as file offset—​is specific to the process and file descriptor.
Don’t rush this design. Think carefully about the state you need to maintain,
how to organize it, and when and how it has to change.

Note that there is a system call `__getcwd` and then a library routine `getcwd`.
Once you’ve written the system call, the library routine should function
correctly.

### Process Support

Process support for Lab 7 divides into the easy (`getpid`) and the not-so-easy:
`fork`, `execv`, `waitpid` and `_exit`. These system calls are probably the most
difficult part of the assignment, but also the most rewarding.

#### getpid

A PID, or process ID, is a unique number that identifies a process. The
implementation of `getpid` is not terribly challenging, but process ID allocation
and reclamation are the important concepts that you must implement. It is not
OK for your system to crash because over the lifetime of its execution you’ve
used up all the PIDs. Design your PID system, implement all the tasks associated
with PID maintenance, and only then implement `getpid`.

#### fork

`fork` is the mechanism for creating new processes. It should make a copy of
the invoking process and make sure that the parent and child processes each
observe the correct return value (that is, 0 for the child and the newly
created PID for the parent). You will want to think carefully through the
design of fork and consider it together with `execv` to make sure that each
system call is performing the correct functionality. `fork` is also likely to
be a chance for you to use one of the synchronization primitives you have
implemented previously (see [LAB 6](./LAB6.html)).

#### execv

`execv`, although merely a system call, is really the heart of this assignment.
It is responsible for taking newly created processes and make them execute
something different than what the parent is executing. It must replace the
existing address space with a brand new one for the new executable, created
by calling `as_create` in the current dumbvm system, and then run it. While this
is similar to starting a process straight out of the kernel, as `runprogram` does,
it’s not quite that simple. Remember that this call is coming out of user space,
into the kernel, and then returning back to user space. You must manage the memory
that travels across these boundaries very carefully. Also, notice that `runprogram`
doesn’t take an argument vector, but this must of course be handled correctly
by `execv`.

#### waitpid

Although it may seem simple at first, `waitpid` requires a fair bit of design.
Read the specification carefully to understand the semantics, and consider
these semantics from the ground up in your design. You may also wish to consult
the UNIX man page, though keep in mind that you do not need to implement
all the things UNIX `waitpid` supports, nor is the UNIX parent/child model of
waiting the only valid or viable possibility.

#### _exit

The implementation of exit is intimately connected to the implementation of
`waitpid`: They are essentially two halves of the same mechanism. Most of the
time, the code for `_exit` will be simple and the code for `waitpid` relatively
complicated, but it’s perfectly viable to design it the other way around as
well. If you find both are becoming extremely complicated, it may be a sign
that you should rethink your design. `waitpid`/`_exit` is another chance to use
your synchronization primitives (see [LAB 6](./LAB6.html)).

#### kill_curthread

Feel free to write `kill_curthread` in as simple a manner as possible. Just keep
in mind that essentially nothing about the current thread’s user space state
can be trusted if it has suffered a fatal exception. It must be taken off
the processor in as judicious a manner as possible, but without returning
execution to the user level.

### Error Handling

The man pages in the OS/161 distribution contain a description of the error
return values that you must return. If there are conditions that can happen
that are not listed in the man page, return the most appropriate error code
from `kern/include/kern/errno.h`. If none seem particularly appropriate, consider
adding a new one. If you’re adding an error code for a condition for which Unix
has a standard error code symbol, use the same symbol if possible. If not, feel
free to make up your own, but note that error codes should always begin with `E`,
should not be `EOF`, etc. Consult Unix man pages to learn about Unix error codes.
On Linux systems man `errno` will do the trick.

Note that if you add an error code to `kern/include/kern/errno.h`, you need to
add a corresponding error message to the file `kern/include/kern/errmsg.h`.

### kern/proc/proc.c

This file contains functions that allow process management. Functions
`proc_create` and `proc_destroy` are essentially the entry points into process
creation and destruction. Keep in mind that you will have to add code to
these functions to manage any additional fields that you add to the process
structure.

Since a process can contain multiple threads, the functions `proc_addthread`
and `proc_remthread` are provided to enable tracking. Since we are not going
to implement multi-threading, you will not need to modify these functions,
but going through the code and understanding how the link between a process and
its threads is established would be beneficial (though optional).

The functions `proc_getas` and `proc_setas` are essentially the getter and
setter for the address space that a process has been assigned. You will find
an example of how to use `proc_setas` by looking into the `runprogram` function.

Finally, In OS/161, there is also a process created for the kernel which tracks
all the kernel threads that are created. This process is created by calling
`proc_bootstrap`. Try to track down when and where this happens.

## Testing using the shell

In `userland/bin/sh` you will find a simple shell that will allow you to test
your new system call interfaces. When executed, the shell prints a prompt, and
allows you to type simple commands to run other programs. Each command and its
argument list (an array of character pointers) is passed to the `execv()` system
call, after calling `fork()` to get a new thread for its execution. The shell
also allows you to run a job in the background using `&`. You can exit the shell
by typing `exit`.

Under OS/161, once you have the system calls for this assignment, you should be
able to use the shell to execute the following user programs from the bin
directory: `cat`, `cp`, `false`, `pwd`, and `true`. You will also find several
of the programs in the `userland/testbin/` directory helpful.

In particular:
1. **Does your console work?** Use `/testbin/consoletest` to determine this. Once you have implemented the write system call, you should have a working console. Note that you should not need to implement open to write to the console.
2. **Do your `open` and `close` syscalls work?** Test this using `userland/testbin/opentest` and `userland/testbin/closetest`.
3. **Do your `read` and `write` syscalls work?** Test these using `userland/testbin/readwritetest` and `userland/testbin/fileonlytest`.
3. **Does your `lseek` syscall work?** Test this using `userland/testbin/fileonlytest` and `userland/testbin/sparsefile`.
4. **Does your `dup2` syscall work?** Test this using `userland/testbin/redirect`.
5. **Does your `fork` syscall work?** Use `userland/testbin/forktest` to test this.
6. **Does your `execv` syscall work?** Use `userland/testbin/argtest`, `userland/testbin/add`, `userland/testbin/factorial` and `userland/testbin/bigexec` to test this.
7. **Do your `waitpid`/`_exit` syscalls work?** We will use `userland/testbin/forktest`, to test this. A lot of other tests internally use `fork`, `waitpid` and `_exit`. You will need to implement `waitpid` and `_exit` to pass these.
8. **Does your `getpid` syscall work?** Almost all of the tests described above use `getpid` in some way. You will need `getpid` to work to pass these.
9. **`/testbin/badcall`:** Tests whether your system call interface can handle invalid arguments.
10. **`/testbin/crash`:** Tests whether your system can gracefully recover from user programs attempting to perform malicious actions.
11. **`/testbin/forktest`:** Runs multiple iterations of `userland/testbin/forktest` to check for race conditions.

Note that you **must** pass `userland/testbin/consoletest` before you can run most of the other tests.
This ensures that your kernel menu is not competing with the test output.

## Design Considerations

Here are some additional questions and thoughts to aid in writing your design
document. They are not, by any means, meant to be a comprehensive list of all
the issues you will want to consider.

Your system must allow user programs to receive arguments from the command line.
For example, you should be able to run the following program:
```
char  *filename = "/bin/cp";
char  *args[4];
pid_t  pid;

args[0] = "cp";
args[1] = "file1";
args[2] = "file2";
args[3] = NULL;

pid = fork();

if (pid == 0) {
	execv(filename, args);
}
```

The code snippet above loads the executable file `bin/cp`, installs it
as a new process, and executes it. The new process will then find `file1` on the
disk and copy it to `file2`.

Passing arguments from one user program, through the kernel, into another user
program, is a bit of a chore. What form does this take in C? This is rather
tricky, and there are many ways to be led astray. You will probably find that
very detailed pictures and several walkthroughs will be most helpful. This piece
of code, in particular, is impossible to write correctly without being
carefully designed beforehand.

Some other questions to consider:
1. What primitive operations exist to support the transfer of data to and from kernel space? Do you want to implement more on top of these?
2. When implementing exec, how will you determine:
	* the stack pointer initial value
	* the initial register contents
	* the return value
	* whether you can execute the program at all?
3. You will need to bullet-proof the OS/161 kernel from user program errors. There should be nothing a user program can do to crash the operating system, with the exception of explicitly asking the system to halt.
4. What new data structures will you need to manage multiple processes?
5. What relationships do these new structures have with the rest of the system?
6. How will you manage file accesses? When the shell invokes the cat command, and the `cat` command starts to read `file1`, what will happen if another program also tries to read `file1`? What would you like to happen? (hint think about concurrency and synschronization in your design document)

## Acknowledgement

This lab was developed thanks to material available at [Harvard 0S/161](http://os161.eecs.harvard.edu/),
[ops-class](https://ops-class.org/) and [OS/161 at UBC](https://sites.google.com/site/os161ubc/home).
