# Program Management Subsystem

The Program Management Subsystem is responsible for loading, executing, and managing programs (processes) in memory. It deals with how programs become processes and how they interact with the OS.

1. Process creation and deletion.
2. Sending and raising the process.
3. Mechanism for communication between multiple process.
4. Provides mechanism for synchronization between process.<br>

```
    compilation (gcc)   ->  Executable file (a.out) or Binary file
```

- a.out file is stored in Hard Disk, when it is executed it gets loaded into RAM. The memory segments gets loaded into the User space and the PCB of the program or process gets loaded into the Kernel space.

- RAM is divided into two parts.

1. User space
2. Kernel space

<img width="800" height="583" alt="image" src="https://github.com/user-attachments/assets/35dd541a-1ea1-4572-b179-597cb28e8fa9" />


#### What is Memory segments of a program?

#### What is Memory sectioin of a program?

#### Memory Layout of a program?

Memory segments of a program refer to the distinct sections of memory allocated during program execution—namely the Text, Data, BSS, Heap, and Stack segments, each serving a specific role such as storing code, variables, or managing function calls and dynamic memory.

<img width="324" height="486" alt="image" src="https://github.com/user-attachments/assets/2df6f8f4-2dbe-49b4-9ac0-65b79cbc26c2" />


- Stack and Heap are created only when program gets loaded to userspace of RAM.
- Memory created using DMA calls is stored in Heap. <br>

#### Stack Memory :

- When a funtion is invoked a block of memory is created in stack is called Stack Memory.

#### Stack Area :

- When a new Thread is created a block of memory is created in stack is called Stack Area.

#### PCB

- For every process, the operating system creates a block of memory in kernel space called the Process Control Block (PCB).

### PCB contains

1. Process Identifier (PID)
2. Parent Process Identifier (PPID)
   - Every process is created from another process. That another process is called Parent Process.
3. File Descriptor table (FD table)
   - It contains the information of the files that are being opened by the programmer. There are many files in kernel but it does not maintain all the info these files.
4. Signal Disposition Table / Signal Behaviour Table / Disposition of Signal
   - refers to a data structure that maintains how a process handles various signals.
   - Every process in an OS (especially Unix-like systems) can receive signals — asynchronous notifications sent to a process to notify it of events (e.g., SIGINT, SIGTERM, SIGKILL, etc.).
   - When we are hanging in infinite loop, then we press the key to come out of the loop
     - ctrl + c -> Terminate
     - ctrl + z -> Suspend
     - These combination when used in kernel space generate the signal.
   - Improper handling of pointers causes crash during runtime of execution and program crashes with segmentation fualt. Then this process is aborted using signaling mechanism.
5. Page Table
   - In operating systems that use virtual memory, a page table is a data structure used to map virtual addresses (used by a process) to physical addresses (actual locations in RAM).
   - While the page table itself is not literally part of the Process Control Block (PCB), the PCB holds a reference (or pointer) to the page table of the process.

## Process

- A process consists of memory segments in userspace and PCB in kernel space, which together represent the complete exectution environment of a program.

- Process = memory segment in userspace + kernel space PCB
- There are so many process in the system. They have their own memory segment and corresponding PCB in kernel space.
  <br>

<img width="701" height="386" alt="image" src="https://github.com/user-attachments/assets/d33d6099-759d-4783-8c3c-25805b2e716b" />

<br>

## Running Queue

- PCB are maintained as a node in linked list and the list is called the Running Queue.
- Process Management subsystem maintains or handles this Running Queue
- The list of PCB's are handled in process management subsystem.

## Ways of creating the process
1) From the executable file process can be created by using the command ./a.out , where a.out is the executable file obtained after compilation.


<img width="724" height="206" alt="aout" src="https://github.com/user-attachments/assets/7c07fc1a-2bb8-44ae-bd43-8548a145ce89" />

2) fork() System call
### System Call
   - It is used in user space to send request in sub-system in kernel space. 
   - Every system call has an equivalent call in kernel space which starts with SYS_
   - Example:
   ```      
            fork()      user space
         _________________________

            sys_fork()  kernel space
   ```
   - there are altogethere 320+ system calls, we can add more
* fork() system call can be used to create a child process.
* The process from where the fork() is invoked is called "Parent Process".
* Once we call fork() control immediately jumps to kernel space or invoking an equivalent funtion in kernel space starting with sys_fork() is actually sending request to kernel space subsystem.
<br>

#### Note : A process which invokes fork system call is called parent process and the new process gets created is called the child process.


<img width="523" height="526" alt="parent_child_process" src="https://github.com/user-attachments/assets/afbfd3b9-f306-4029-b886-238d8cbf5bba" />

* Example : 
```
      main()
      {
         int id;
         id = fork();      // create a new child process
         if(){

         }else{

         }
      }
```

   - once you invoke fork() the control jumps to kernel space
   - sending request to kernel space
   - you are invoking an equivalent function in kernel space it starts with sys_fork()
   - fork() actually sending request to kernel space process management subsystem
   - a new PCB get created for child process

* Parent process PCB has
   1) Process identifier (PID)
   2) Parent process identifier (PPID)
   3) File discriptor table
   4) Signal disposition table
   5) Page table

* Some members of parent process PCB are copied to the child process PCB.

#### QA) Which members of Parent process PCB are copied to child process PCB?


<img width="709" height="349" alt="parent_child_copy" src="https://github.com/user-attachments/assets/5c731fce-1775-411c-8c72-dcbf53bac36a" />

* The contents of File Discriptor table are inherited from parent process
* For this new child process we create some additional memory in user space
* the contents of parent process memory segmets are copied as it is to child process memory segments
* The child process is an exact replica of parent process
* from a sigle program we can create multiple process
<br>

* When fork() is invoked, the following things happens:
   - new PCB gets created in kernel space for child process
   - For child process, some additional memory in user space gets created

* By using the conditional statement after fork(), we ensure parent process and the child process will execure different blocks of codes.
* If conditional statements are not present then parent and child process will execute the same block of code
* To this coditional statement we pass return value of fork()
<br>

```
      // parent process
      main()
      {
         pid = fork();
         if(pid == 0){              // Not executed
            printf(". . . ");
         }
         else{                      // Executed
            printf("...");
         }
      }
```
```
      // child process
      main()
      {
         pid = fork();
         if(pid == 0)
         {
            printf("...");          // executed
         }
         else
         {
            printf("....");         // not executed
         }
      }
```
* For above child process execution is not going to start from the main() function, it starts from after the fork()
* fork() returns twice, once in parent process and once in child process.
   -  fork() returns the pid of child process in parent process
   -  fork() returns 0 in the child process
* So in above if() block is ex aecuted in child process and else block is executed in parent process
<br>

* Using switch()

```
      // parent process
      switch(fd){
         case -1:          // not executed
               error;
         case 0:           // not executed
               break;
         default:          // excecuted
               break;
      }
```
```
      // child process
      switch(fd){
         case -1:          // not executed
               error;
         case 0:           //  executed
               break;
         default:          // not excecuted
               break;
      }
```
<br>

* CLA
   - ps -cf :- for visualizing the process running in the system.
   - top    :- for visualizing the process running in the system.
* GUI
   - Viven     2003    1495   0:80:23                          /home/desktop/...
   -            pid    ppid   time stamp of created process    name of the file

* Difference between ps and top?
   - ps : snapshot of the process
   - top : process information get updated after each 1 second
<br>

#### init() process
   - the first process after linux boot or OS boot.

#### Orphan process and Demon process(Zombie process)
* Orphan process : 1st process terminates even before child process, is called orphan process
* Exmaple:

```
         int main() {
            if (fork() == 0) {  // Child
               sleep(3);
               printf("Child: parent pid = %d\n", getppid());
            } else {            // Parent
               exit(0);
            }
         }
```

* Once the main function terminates, then the parent process will be terminated. The memory segments and PCB get loaded from user space and kernel space respectively.

* What is the PPID of orphan process?
   - The PPID of the orphan process is 1, it takes the PID of the init process, init process is the first process of OS Boot.
<br>

#### exit (vs) return
   * The return takes return to the location where function is invoked.
   * Whereas exit terminates the entire function.
   * Example : 
   ```
      main()
      {  ....
         add();
         ....
         return 5;
      }
      add()
      {
         ....
         return ();  // returns the value
      }
   ```
   * return 5 (or) exit(5)
      - They are sent to the parent process. Because parent process needs to know the exit status of child process.
      - in exit, 5 is only takes 1 byte
   * Kernel by default submits the status of child process to parent process. And it is done by using command "wait(&status)"

#### In Parent process can you access to the exit or return value of child process?
#### In parent process how do you block to get the id of child?

      * Using wait() blocking call
      * wait() comes out of blocking state only when the child process get terminated
<br>
      
* Parent process:

```
            main()
            {
               int stat;
            ✅id = fork();
               if(id == 0)
               {
                  printf("Child process");
                  exit(0);
               }
               else{
            ✅   printf("Parent process");
               }
            ✅wait(&stat);
            ✅printf("%d", WEXITSTATUS(stat));
            }

```


* Child process:

```
            main()
            {
               int stat;
               id = fork();
            ✅if(id == 0)
               {
                  printf("Child process");
                  exit(0);
               }
            ❌else{
                  printf("Parent process");
               }
            ❌wait(&stat);
            ❌printf("%d", WEXITSTATUS(stat));
            }
```

<br>

      * Inside the child wait() should not execute, so it is necessary to use exit()
      * Parent process wait() comes out of the blocking state only after child process is terminated
      * When exit() is called, the memory segments of child process from user space are offloaded.
      * The exit() code is stored in PCB of child process
      * Kernel after submitting the exit code to parent process, it offloads the PCB of child process from kernel space
      <br>
      <img width="643" height="510" alt="image" src="https://github.com/user-attachments/assets/7adfe6b1-a142-473c-b178-59bd27f8cf8c" />
      
      * If parent process doesn't use wait() it cannot get access to exit code from child process
      * If no wait() was there, then we cannot get the exit code of child process. Although the memory segments will be erased but PCB contents will be there.
<br>

   #### Zombie Process
      - A zombie (Demon or defunct) process is a terminated child process whose execution is complete but whose Process Control Block (PCB) still exists in the system’s process table because the parent has not yet read its exit status using wait() or waitpid().

<br>

      * By using ps -af command we can be able to see the child process.
      * By sending a signal we can terminate a process.

         ```
                     kill -9 1000
             signal number_|    |_ PID of process
         ```

      * Zombie process cannot be terminated by kill command.
      * The exit code of child process can be found in the argument of wait().

<br>

<img width="350" height="131" alt="image" src="https://github.com/user-attachments/assets/41e5c299-65c8-47f5-a0df-08acec8a2022" />

   * By using the bitwise operator 1 Byte of exit is stored in either 1st, 2nd, 3rd, 4th byte of the stat
   * WEXITSTATUS(stat) : It is a predefined macro which extracts 1 byte of exit code from the 4 byte variable

* Driver is never going to initiate an IO request
* Only Applications can initiate a IO request
* You can only have single driver for accessing single device
* We cannot have multiple drivers accessing a single device
* Multiple applications can send request to single driver, and then to single device
<img width="672" height="317" alt="image" src="https://github.com/user-attachments/assets/626784e8-c972-44a4-b210-4f9d60bf4bf6" />

#### Q) Which specific set of system calls are used by the application to send request to driver?
   - Basic I/O calls
* Hardware can initiate a request in the form of hardware interrupts

### Need of fork()

1) To create multiple applications from a single application
2) Shell program internally uses fork() + exec() family system call
   - There are different types of shells are there
      1. bash shell
      2. c-shell
      3. bomb shell
3) In client and server programming we use fork() system call

<img width="660" height="180" alt="image" src="https://github.com/user-attachments/assets/e2e21fa1-481b-4767-964b-5faf2ea58fdb" />

#### Note:
   - wait() return pid of child process that has been terminated
   - pid = wait(&stat);
   - 1 byte contains exit code from child process
   - Remaining 3 bytes contains the information about normal/abnormal termination of child process
   - In case of abnormal termination the signal used to terminate the specific child process is specified
   * Variants of wait() system call :
      - wait() : Locks until child process terminates
      - waitpid() : It takes 3 arguments and it comes to unblocking state when any specific child terminates
<br>

   ```
      main()
      {
         fork();
         fork();
         fork();
         printf("fork()");
      }
   ```
   * Number of fork() call = 3
   * Number of outputs = 2 power 3 = 8
   * fork() will be printed 8 times

<br>

```
   Parent process
   main()
   {
      int stat;
      int chpid1, chpid2;
      
      chpid1 = fork();              // first child process
      if(chpid1 == 0)
      {
         printf("1st child process");
         sleep(50);
         exit(5);                   // it is mandatory to have exit or return at end of if block
      }

      chpid2 = fork();              // second child process
      if(chpid2 == 0)
      {
         printf("2nd child process");
         sleep(30);
         return 4;
      }

      pid = wait(&stat);                     // second child process termination pid = 2002
      printf("%d", WEXITSTATUS(stat));       // 4

      pid = wait(&stat);                     // first child process termination  pid = 2001
      printf("%d", WEXITSTATUS(stat));       // 8
   }
```

<br>

```
   1st child process
   main()
   {
      int stat;
      int chpid1, chpid2;
      
      chpid1 = fork();              
      if(chpid1 == 0)               // start
      {
         printf("1st child process");     ✅
         sleep(50);                       ✅
         exit(5);                         ✅
      }
                                          ❌all below code wont execute
      chpid2 = fork();              
      if(chpid2 == 0)
      {
         printf("2nd child process");
         sleep(30);
         return 4;
      }

      pid = wait(&stat);                     
      printf("%d", WEXITSTATUS(stat));       

      pid = wait(&stat);                     
      printf("%d", WEXITSTATUS(stat));       
   }
```
<br>

```
   1st child process
   main()
   {
      int stat;
      int chpid1, chpid2;
      
      chpid1 = fork();              
      if(chpid1 == 0)                     ❌this if code wont execute
      {
         printf("1st child process");     
         sleep(50);                       
         exit(5);                         
      }
                                          
      chpid2 = fork();              
      if(chpid2 == 0)                     // start
      {
         printf("2nd child process");     ✅
         sleep(30);                       ✅
         return 4;                        ✅
      }  
                                          ❌below code wont execute
      pid = wait(&stat);                     
      printf("%d", WEXITSTATUS(stat));       

      pid = wait(&stat);                     
      printf("%d", WEXITSTATUS(stat));       
   }

```
<br>

* from a single process we have created 3 processes
* The parent process and two child process execute independent of each other
#### vfork()
   * vfork() is always combined with exec() family of system calls.
   * To understand vfork() we need to understand the virtual memory which requires further concept pagening technique and address translation.

<br>

# Virtual Memory
   - Paging Technique
   - Address Translations

#### Why dont we run program within Hard-disk
#### Why do we copy program to RAM for execution
   1) Access speed of Hard Disk is slower when compared to RAM
   2) We can access only block by block data from Hard Disk. But we can do Byte by Byte access from RAM.

* RAM is split into byte by byte locations.
* Eack byte of RAM have unique address
* We can store these addresses using pointers

<br>

* Memory in CPU is in the form of registers, an we use register name to refer to the CPU register.
#### How do you copy data from RAM to CPU registers?
   * By using the CPU intructions LDA i.e Load
#### How do you copy data from CPU register to RAM?
   * By using the CPU instruction STR i.e store

<img width="707" height="336" alt="image" src="https://github.com/user-attachments/assets/cf66c12b-d442-4ea1-a8fd-84914dca075f" />

<br>

# Virtual Memory
   * Suppose we have four process already loaded into RAM and we want to load one more process

<img width="404" height="278" alt="image" src="https://github.com/user-attachments/assets/0f987ca0-bcdf-47d4-a560-62f6efe9234b" />

### If you want to run a new process in RAM

#### Option 1:
   * Wait until one of the process terminates
      - we dont know how much time a process will take to terminate, it depends upon processor. so not fissible
#### Option 2:
   * Terminate one of the process in between
      - No fissible
#### Option 3:
   * Try to find out segment of these process which are not used for longer duration of time.
      - It can be done

