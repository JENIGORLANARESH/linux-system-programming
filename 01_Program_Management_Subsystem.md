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

1. From the executable file process can be created by using the command ./a.out , where a.out is the executable file obtained after compilation.

<img width="724" height="206" alt="aout" src="https://github.com/user-attachments/assets/7c07fc1a-2bb8-44ae-bd43-8548a145ce89" />

2. fork() System call

### System Call

- It is used in user space to send request in sub-system in kernel space.
- Every system call has an equivalent call in kernel space which starts with SYS\_
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

- Example :

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

  1.  Process identifier (PID)
  2.  Parent process identifier (PPID)
  3.  File discriptor table
  4.  Signal disposition table
  5.  Page table

* Some members of parent process PCB are copied to the child process PCB.

#### QA) Which members of Parent process PCB are copied to child process PCB?

<img width="709" height="349" alt="parent_child_copy" src="https://github.com/user-attachments/assets/5c731fce-1775-411c-8c72-dcbf53bac36a" />

- The contents of File Discriptor table are inherited from parent process
- For this new child process we create some additional memory in user space
- the contents of parent process memory segmets are copied as it is to child process memory segments
- The child process is an exact replica of parent process
- from a sigle program we can create multiple process
  <br>

- When fork() is invoked, the following things happens:

  - new PCB gets created in kernel space for child process
  - For child process, some additional memory in user space gets created

- By using the conditional statement after fork(), we ensure parent process and the child process will execure different blocks of codes.
- If conditional statements are not present then parent and child process will execute the same block of code
- To this coditional statement we pass return value of fork()
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

- For above child process execution is not going to start from the main() function, it starts from after the fork()
- fork() returns twice, once in parent process and once in child process.
  - fork() returns the pid of child process in parent process
  - fork() returns 0 in the child process
- So in above if() block is ex aecuted in child process and else block is executed in parent process
  <br>

- Using switch()

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

- CLA
  - ps -cf :- for visualizing the process running in the system.
  - top :- for visualizing the process running in the system.
- GUI

  - Viven 2003 1495 0:80:23 /home/desktop/...
  -            pid    ppid   time stamp of created process    name of the file

- Difference between ps and top?
  - ps : snapshot of the process
  - top : process information get updated after each 1 second
    <br>

#### init() process

- the first process after linux boot or OS boot.

#### Orphan process and Demon process(Zombie process)

- Orphan process : 1st process terminates even before child process, is called orphan process
- Exmaple:

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

- Once the main function terminates, then the parent process will be terminated. The memory segments and PCB get loaded from user space and kernel space respectively.

- What is the PPID of orphan process?
  - The PPID of the orphan process is 1, it takes the PID of the init process, init process is the first process of OS Boot.
    <br>

#### exit (vs) return

- The return takes return to the location where function is invoked.
- Whereas exit terminates the entire function.
- Example :

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

- return 5 (or) exit(5)
  - They are sent to the parent process. Because parent process needs to know the exit status of child process.
  - in exit, 5 is only takes 1 byte
- Kernel by default submits the status of child process to parent process. And it is done by using command "wait(&status)"

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

- Child process:

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

- Inside the child wait() should not execute, so it is necessary to use exit()
- Parent process wait() comes out of the blocking state only after child process is terminated
- When exit() is called, the memory segments of child process from user space are offloaded.
- The exit() code is stored in PCB of child process
- Kernel after submitting the exit code to parent process, it offloads the PCB of child process from kernel space

   <br>
   
   <img width="343" height="210" alt="image" src="https://github.com/user-attachments/assets/7adfe6b1-a142-473c-b178-59bd27f8cf8c" />
      
   * If parent process doesn't use wait() it cannot get access to exit code from child process
   * If no wait() was there, then we cannot get the exit code of child process. Although the memory segments will be erased but PCB contents will be there.

<br>

#### Zombie Process

- A zombie (Demon or defunct) process is a terminated child process whose execution is complete but whose Process Control Block (PCB) still exists in the system’s process table because the parent has not yet read its exit status using wait() or waitpid().

<br>

- By using ps -af command we can be able to see the child process.
- By sending a signal we can terminate a process.

      ```
                  kill -9 1000
          signal number_|    |_ PID of process
      ```

  - Zombie process cannot be terminated by kill command.
  - The exit code of child process can be found in the argument of wait().

<br>

<img width="350" height="131" alt="image" src="https://github.com/user-attachments/assets/41e5c299-65c8-47f5-a0df-08acec8a2022" />

- By using the bitwise operator 1 Byte of exit is stored in either 1st, 2nd, 3rd, 4th byte of the stat
- WEXITSTATUS(stat) : It is a predefined macro which extracts 1 byte of exit code from the 4 byte variable

- Driver is never going to initiate an IO request
- Only Applications can initiate a IO request
- You can only have single driver for accessing single device
- We cannot have multiple drivers accessing a single device
- Multiple applications can send request to single driver, and then to single device
  <img width="672" height="317" alt="image" src="https://github.com/user-attachments/assets/626784e8-c972-44a4-b210-4f9d60bf4bf6" />

#### Q) Which specific set of system calls are used by the application to send request to driver?

- Basic I/O calls

* Hardware can initiate a request in the form of hardware interrupts

### Need of fork()

1. To create multiple applications from a single application
2. Shell program internally uses fork() + exec() family system call
   - There are different types of shells are there
     1. bash shell
     2. c-shell
     3. bomb shell
3. In client and server programming we use fork() system call

<img width="660" height="180" alt="image" src="https://github.com/user-attachments/assets/e2e21fa1-481b-4767-964b-5faf2ea58fdb" />

#### Note:

- wait() return pid of child process that has been terminated
- pid = wait(&stat);
- 1 byte contains exit code from child process
- Remaining 3 bytes contains the information about normal/abnormal termination of child process
- In case of abnormal termination the signal used to terminate the specific child process is specified

* Variants of wait() system call : - wait() : Locks until child process terminates - waitpid() : It takes 3 arguments and it comes to unblocking state when any specific child terminates
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

- Number of fork() call = 3
- Number of outputs = 2 power 3 = 8
- fork() will be printed 8 times

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

- from a single process we have created 3 processes
- The parent process and two child process execute independent of each other

#### vfork()

- vfork() is always combined with exec() family of system calls.
- To understand vfork() we need to understand the virtual memory which requires further concept pagening technique and address translation.

<br>

# Virtual Memory

- Paging Technique
- Address Translations

#### Why dont we run program within Hard-disk

#### Why do we copy program to RAM for execution

1.  Access speed of Hard Disk is slower when compared to RAM
2.  We can access only block by block data from Hard Disk. But we can do Byte by Byte access from RAM.

- RAM is split into byte by byte locations.
- Eack byte of RAM have unique address
- We can store these addresses using pointers

<br>

- Memory in CPU is in the form of registers, an we use register name to refer to the CPU register.

#### How do you copy data from RAM to CPU registers?

- By using the CPU intructions LDA i.e Load

#### How do you copy data from CPU register to RAM?

- By using the CPU instruction STR i.e store

<img width="707" height="336" alt="image" src="https://github.com/user-attachments/assets/cf66c12b-d442-4ea1-a8fd-84914dca075f" />

<br>

# Virtual Memory

- Suppose we have four process already loaded into RAM and we want to load one more process

<img width="404" height="278" alt="image" src="https://github.com/user-attachments/assets/0f987ca0-bcdf-47d4-a560-62f6efe9234b" />

### If you want to run a new process in RAM

#### Option 1:

- Wait until one of the process terminates
  - we dont know how much time a process will take to terminate, it depends upon processor. so not fissible

#### Option 2:

- Terminate one of the process in between
  - No fissible

#### Option 3:

- Try to find out segment of these process which are not used for longer duration of time.

  - It can be done

- Inorder to find out the segments which are not used for longer duration in a process we need to follow some algorithms.

- Step 1 : Hard Disk is consists of two segments i.e SWAP AREA segment(in Linux) or BACKING STORE(in windows) and File System.
- Segments of process which are not used for longer duration is moved to Swap Area of Hard Disk

#### NOTE:

- It is not mandatory to have all segments in RAM for executions. Some of the process segments may be there in Swap Area and still we can execute the process

<img width="526" height="277" alt="image" src="https://github.com/user-attachments/assets/af1045f9-ca56-491a-abba-a4ace24b8dea" />

- The empty spaces that has been generated are not continuous memory locations. It is because some of the memory segments of process which does not lost for long duration of time has been shifted to swap area of hard disk.

- Since we have empty spaces load the new process. But empty new spaces are not continuous.
- The new process segements has to be splitted and load to the non continuous or empty memory locations.

#### Note:

- To run a program, memory segments need not be continuous memory locations. It also can be non contiguous.

* PROBLEM : We have 2 issues in this design of process
  1.  Keeping track of segments of process copied to the swap area fo hard disk.
  2.  Keeping track of segments of a new process which are present in non coniguous memory locations.
* To overcome these two issues we use Paging Technique
* Note : Ram size is limited
  <br>

# Paging Technique

- Here we dont load the process directly into RAM.
- So before loading to RAM, we divide the process into equal parts known as Pages or Virtual page.
- Pages are numbered storing from zero(0).
- The virtual memory scheme splits the memory used by the process into equal size parts called as Pages or Virtual Pages.
- Size of each page can be 2KB, 4KB, 8KB.
- By default in Linux page size is 4KB. In solaris OS page size is 8KB.
- You can configure linux page to 2KB or 8KB, but default is 4KB.
- Visualize that a page is a 4kb block of memory of a process

<img width="226" height="232" alt="image" src="https://github.com/user-attachments/assets/db5f0859-2dfd-4df6-a0dc-77816f67f9ed" />

#### Physical Frames

- RAM is also divided into equal size parts called as Physical Frames or Page Frames
- Physical frame size should be eqaul to that of pages size.
- The number of pages within a process are not fixed, they changes.

<img width="275" height="225" alt="image" src="https://github.com/user-attachments/assets/94712f9c-2c51-4931-9455-571f8a40098e" />

#### Why number of pages within a process are not fixed?

1.  When heap and stack grows, the number of pages within a process increases
2.  When a new shared memory is created the number of pages increases
3.  When we create a new block of memory using memory mapping call "mmapp()"

#### Why do we require shared memory?

- By default multiple process cannot share data(i.e send and receive). so its required

#### How does multiple process can share data?

- By using IPC(Inter Process Communication) mechanism
- Inter Process Communication (IPC)

  - Different types of IPC mechanism are given below

  1. Pipes
  2. Named pipes(FIFO)
  3. Message Memory
  4. Shared Memory
  5. Semaphores

- Multiple process must need IPC mechanism to share the data (send or receive)
- Physical frames are numbered starting from zero
- Initially all the pages are copied continuously
- Over a period of time during execution the pages goes into uncontinuous physical frames due to swap invasion.

- Page Table : Page table is present inside the PCB. Page number are used as indexed to this page table.
- Page table contain physical frame numbers.
- We have another name for this info known as "Page Mapping Info"
- Page table contains information about which page are copied to which physical frame.

<img width="757" height="631" alt="image" src="https://github.com/user-attachments/assets/2ffc16ee-ce89-445a-a96a-743e1b718a74" />

- Each process have their own page table.
- In above the pages of process-1 and pages of process-2 are copied into completly different physical frames (or) Pages of multiple process are copied into different physical frames.
- Page table of process 1 and page table of process 2 are pointing to completly different physical frames.
- If you want to create some empty space to load new process, try to findout pages which are not used for longer duration, that can be done with the help of page replacement algorithm.
- Pages which are not used for longer duration they are copied to swap area.

#### Explain a scenario when pages of process1 and pages of process2 are copied into same physical frames

#### (or)

#### Explain a scenario where pages of multiple process are sharing the same physical frames

#### (or)

#### Can the page table of multiple process point to same physical frames?

There are 3 scenarios

1. During child process creation, parent process and child process pages are copied into same physical frames. <br>
   (or) <br>
   Pages of parent and child process are sharing same physical frame
2. When multiple process share data using shared memory IPC mechanism
3. When multiple process share memory thats created using Memory Mapping Call "mmap()"

#### Program

```
  int x = 10;
  main()
  {
     id = fork();
     if(id == 0)
     {
        x = 20;
        printf("Child process");
        exit(5);
     }
     id = wait(&stat);
     printf("%d", WEXITSTATUS(stat));
     printf("%d", x);                    // output : 10
  }
```

<img width="643" height="598" alt="page_table" src="https://github.com/user-attachments/assets/7548d56a-8e15-46ff-81d4-00d040f81276" />

- In recent operating system the memory segments for child process is not created immediatly, It is created after some time of execution.
- During initial stage of execution child process uses the memory segments of parent process.

<img width="388" height="318" alt="image" src="https://github.com/user-attachments/assets/ef73eed3-bce7-4b28-ad17-714bde6738d9" />

#### How does the child process uses the memory segments of parent process?

- The memory segments of a process are divided into pages, and these pages are are accessed by both parent process and child process.
- While CPU executing these instructions, they operate on information that present in stack, heap, data, bss segments.
- Page table of parent process and page table of child process are poiting to same physical frames.
- Parent process and child process are using the pages stored in same physical frames
- Until when the child process, parent process shares the pages stored in same physical frames.
- As long as parent process and child process does the "READ OPERATION" they will share these pages.
- Once parent or child does "WRITE OPERATION" seperate pages gets created.

<img width="556" height="588" alt="image" src="https://github.com/user-attachments/assets/b709161e-61af-4502-9d4b-a9103db7a5b8" />

<br>

#### How does parent process and child process keeps tracks of which instructions they are executing?

- By storing some information in contexual area.
- Parent process and child process have to keep track of which instructions they are executing.

#### How parent child process after fork() executes different blocks of code?

- Based on the return value of fork() being passed to conditional statement.

* consider following example

```
   parent process
   int x = 10;
   main()                                 // start
   {
      id = fork();
      if(id == 0)
      {
         x = 20;
         printf("child");
         exit(5);
      }
      id = wait(&stat);
      printf("%d", WEXITSTATUS(stat));
      printf("%d", x);                       // output : 10
   }
```

```
   child process
   int x = 10;
   main()
   {
      id = fork();
      if(id == 0)                            // start
      {
         x = 20;         //  4 bytes data segement -> page2 -> physical frame3 -> updates to physical frame5 after write operation
         printf("child");
         exit(5);
      }
      id = wait(&stat);                      ❌
      printf("%d", WEXITSTATUS(stat));       ❌
      printf("%d", x);                       ❌
   }
```

<img width="1766" height="1479" alt="paging" src="https://github.com/user-attachments/assets/27c2b332-9267-49d9-af89-450a4c3432e9" />

<br>

#### Write Operation to pages (parent/child process)

1. Child process is suspended
2. Write on copy / copy on write
   1. Pages on write operation is performed are duplicated
   2. Duplicate pages is copied into freely available physical frames
   3. Pages correspoding entry in page table of child process modified.
3. child process resumed
4. Write operation performed

#### How parent and child process executes different blocks of code after fork()?

- Based on the return value of fork() being passed to conditional statements.

- NOTE : Till READ operation both parent and child shares same physical frame, once they perform WRITE operation on the RAM, physical frames doesnot remain common anymore.

#### Explain WRITE operation to pages shared to parent and child process?

1. Child process is suspended.
2. Write-on-copy or Copy-on-write (COW) technique
   - page-2 on which WRITE operation to be performed is duplicated
   - duplicated page-2 is copied into freely available physical frames
     - In above memory for x = 20, is again created and page-2 is placed in freely available 5th physical frame. And the page-2 corresponding entry in page table of child process is to be modified.
3. Child process resumes
4. WRITE operation is performed and the 5th physical frame of RAM is updated.

<img width="549" height="494" alt="image" src="https://github.com/user-attachments/assets/06eae97e-6a20-4982-a627-3e638a69a37b" />

- NOTE : when parent and child tries to do write operation, write on copy or copy on write (COW) technique is applied.
- In case of vfork(), COW technique in not applied but in fork() COW technique is applied.

<br>

# Address Translation
