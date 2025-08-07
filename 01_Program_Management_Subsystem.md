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
   ```      fork()      user space
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
