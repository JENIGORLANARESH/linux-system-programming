# Basic I/O Calls

* Basic I/O calls are also called as **Universal I/O Calls**
* **Need** : to access files present in Physical memory or Virtual memory
* **Types of files** : there are 3 types of files present in Linux OS

1. **Normal Files** 
    1. Textual files
        - .txt , .c , .s
        - to access textual files we have text editors.
        1. CLI text editors
            - vi
            - vim
        2. GUI
            - editplus
            - gedit
    2. Binary files
        - .o , a.out , ls , ps , .mp3 , .mp4 , .jpg , .img
        - for accessing binary we have seperate applications for each of these
        - .o , a.out -> objdump
        - .mp3 -> mp3player
        - .mp4 -> VLC player
        - .jpg -> imageviewer

2. **Special Files**

    - contains IPC objects, named pipes and fifos.
    - Multiple process cannot directly shares data, they need ipc mechanisms
    - IPC Mechanisms
    1. Pipes
    2. Named pipes/FIFOs
    3. Message Queues
    4. Shared Memory
    5. Semaphores

3. **Device Files**
    - In Linux user space, every device file is seen as device nodes. They are present in /dev directory.
    - Device files are also called as **Device Nodes**
<br>

#### Question :  How a user space application sends request to hardware?<br>
**Answer :** An application can never send request directly to hardware. It uses system calls(i.e basic io calls) to interact with the drivers.

#### Question : How do you make sure that only request to particular driver is sent?<br>
**Answer :** Every driver has unique device files. And the application has to use basic i/o on device file to send request to particular driver. <br>
And particualr driver initiates the corresponding hardware. This concept is applicable for window, android etc. as they all have user space as well as kernel space

### Serial Port

* Serial ports are needed for the serial communication.
* serial port uses the device file ttsD present in /dev directory.
* parallel port is used in printers.
* parallel port -> /dev/parpot
* Hard Disk 
    - /dev/sd0
    - /dev/sd0
    - /dev/sd1
    - if there exits partition in hard disk
    - /dev/sab -> 2nd hard disk
    - /dev/sac -> 3rd hard disk

**Note :** Every device will have corresponding device file in /dev directory. Whenever we think about any device immediately look for corresponding file in /dev directory.

* From linux user space every device is seen as device file.
* Application uses basic i/o calls to send request to driver, from there to hardware.
<br>
<br>

Bsic I/O calls are called as Universal i/o calls.<br>
Bacause it is used to access the normal files, special files and device files.<br>
* Basic i/o calls are:
    - open()
    - close()
    - read()
    - write()
    - ioctl()
    - fcntl()

**NOTE :** We cannot perform read and write operation until unless we open the file.<br>
<br>

## open() System call:
    - open( "file.txt" , O_RDWR , 0640);

* file.txt -> name of the file
* 0_RDWR -> mode
    - O_RDONLY -> read only
    - O_WRONLY -> write only
    - O_RDWR -> read write
* 0640 -> permission in octal form
<br>

* Permission is needed when we are creating the file.
* Integer value can be expressed in 
    - Decimal -> used for general calculation
    - octal -> for applying permission to files of various system resource
    - Hexadecimal -> for address (physical/virtual)

* Basic io calls send request to file management sub system.

<img width="350" height="657" alt="image" src="https://github.com/user-attachments/assets/ba5742c5-b72a-46f5-8466-6ffbbfb755e2" />

* open() invokes an equivalent function in kernel sys_open().<br>

**Block Device :** It sends a request to device to search for a file named file.txt. Kernel is going to search block device for a file named file.txt

**Types of Devices**

1. Character Devices
    - character driver uses character driver.
    - approx 80% devices are character devices
2. Block Devices
    - Block devices uses Block Drivers. e.g: HardDisk, USB, pendrive.
    - It also called as Mass Storage Device
3. Network Device
    - Network device uses Network Drivers.
    - e.g: LAN prot.
    - The alternative name of Network Device is NIC(Network Interface Calls)

* open() call invokes sys_open() which sends request to the driver to search the file name "file.txt" in the hard disk. The kernel is going to search the file name file.txt
* When the kernel finds the file.txt file, it has inode corresponding to it(i.e file.txt)

<img width="408" height="151" alt="image" src="https://github.com/user-attachments/assets/7dc0e174-6c98-4f31-b867-bcf7a5557271" />

* Information about file is stored in inode object.
* inode object type is **struct inode**.
* File information contains the information about the file, whereas file data contains the contents of the file.
* Hard Disk is divided into 2 parts.
    1. Blocks
        - collection of sectors are called blocks
    2. Sectors
        - by default 512 bytes

* File data gets stored in the sector which is a part of a blocks.
* File information is stored in the inode object, and it is also stored in the sectors of Hard disk.
<br>

#### Kernel uses which object to represent a file?
**Ans :** inode object

#### From user space can we access the file information present in inode object?
**Ans :** We have multiple ways.

1. Using command from terminal application
    - $ls -l
    - shows the information about each and every file, and the entire information is coming from inode object.

<img width="767" height="275" alt="image" src="https://github.com/user-attachments/assets/c85f022b-61d1-4d74-bf9b-9ade4f6605a4" />

**NOTE :** Each file has corresponding inode present in the hard disk. Each inode object will have unique ID and it can be3 observed using ls -il.

2. Using Basic i/o calls from application
    - stat()
    - fstat()
<br>

#### Which system call is used to access file information?

**Ans :** stat() , fstat()

#### ls command internally uses which system call to access the file information?

**Ans :** stat() , fstat()

#### What happens after kernel finds its inode object?
**Ans :** <br>
    
* Inode object corresponding to file gets copied from hard disk to the kernel space of RAM.
* Kernel creates a new object called file object.
* The contents of file objects are :

<img width="475" height="277" alt="image" src="https://github.com/user-attachments/assets/5a3855f9-19da-48e4-9fb4-4a3ec43fee61" />

* It also contains the base address of inode object. 
* Kernel uses file object to represent the open files.
* Whenever the file is created the inode object corresponding to file is created.
* But file object is created only when we open the file.
* File Object base address is stored in FD table.

#### Difference between primary data and other members of file object?
* All other members are filled/or used/or accesssed by the kernel.
* Primary data is not used by kernel it is used by the drivers.
* Modes are present in file object. -> f_mode
* Permission get stored in struct inode.
* First 3 entries in FD table are already filled. They are stdin, stdout, stderr and it is inherited from parent process.
* The base address starts from 4th position indexed 3 onward in fd table.

<img width="740" height="203" alt="image" src="https://github.com/user-attachments/assets/28a83753-0795-48c8-9896-c7232fcc6897" />

#### When fd table is created?
**Ans :** Whenever a process is executed, PCB gets created and fd table is the  part of the PCB.<br>
fd table has nothing to do with open system call.

#### What is the size of fd table?
**Ans :** 0 to 1024

#### When file object is created?
**Ans :** When open() system call is inovoked, file object is created.

* sys_open() returns indexx to the table where new entry is created. The index to the fd table is called fd or file descriptor.

#### What will open() returns?
**Ans :** It returns the value called fd or file descriptor. fd is the index to the fd table which stores the new entries<br>
This fd is used in all sub sequent calls(i.e read, write etc)

#### When file is open() first time in your program what it returns?
**Ans :** 3 [as 0,1,2 is already inherited from parent process in fd table]<br>Once we get fd we can do read and write operation

## Write System Call
File name is accepted as an arguments by open system call. The remaining basic i/o calls uses fd as an arguments.

```
    syntax:
    char buf[4096];
    write( fd , buf , strlen(buf));
                |_base address
            |
            |
            |  invokes
    sys_write( fd , 0xa000 , 4096);
```
* From the base address how many address we want to occupy depends upon the third argument strlen(buf).

```
    write()
      |
      | invokes
    sys_write()
```

1. uses fd as an index to the fd table and it will access file object and inode object.
2. It uses file object and inode object while writing data into file present in hard disk.
3. The ASCII value of each character is written to file.
4. On error System call returns -1.
5. On sussess System call returns fd.

* write returns number of bytes copied/written from user space application to the file present in the hard disk.

#### In what cases open might return error?
**Ans :** system calls mostly gives errors if proper arguments are not passed.

*   O_TRUNC -> It will completely erases the whole data. It completely replaces old data with new data.
*   close(fd) -> clears the entry in fd table and deallocates the memory.
*   O_APPEND -> For adding the data at the end of other data.
*   O_CREAT -> Open() System call is used to open any file.
<br>
<br>

* Permissions are applicable only when you create a new file.
* open system call is not only useful for opening a file it also useful for creating a file.

```
    fd = Open("file.txt" , O_WRONLY | O_CREAT | O_TRUNC, 0640);
    fd = Open("file.txt" , O_WRONLY | O_CREAT | O_APPEND , 0640);
```

* For every process we hava a seperate PCB and for every PCB we have a seperate FD table.
* Strings present in file dont have NULL character.

## Read Operation

*   While reading we assume that file is already existing. If it does not exist then it will return -1.

```
    char buf[20];
    ret = read(fd , buf , 20);
    buf[ret] = '\0';        |
                            |
                            |
                    sys_read(3 , oxa000 , 20);
                -> it uses first argument fd as indexed to fd table
                    and obtains the file objects and inode objects.
```

#### Why read need access to file objects?
1. for checking Modes i.e. WRONLY, RDONLY OR RDWR
    if there is RDONLY, it will throw an error.
2. For checking the cursor position, which maintains the locations from where we are going to read or write data.

* When we use basic i/o calls cursor position cannot be seen graphically, instead cursor position is seen as a value in a file object.
* Initially the cursor position is at '0'. The position changes while reading and writing the data.

* cursor position by default changes position while reading data or writing data.
* sys_read copies bytes present in file to the address present in second argument.
* At that instant the data being copied to user space application directly.
* sys_read at the end returns a valuee i.e number of bytes copied from file present in hard disk to the user space application.
* When the read call completes successfully data is present in the array buf, and to avoid printing garbage value '\0' or null character is added at the end.

```
    buf[ret] = '\0';
    printf("%s\n", buf);
```

<img width="770" height="354" alt="image" src="https://github.com/user-attachments/assets/b40a625c-71f1-4fe3-b374-3280e19f6645" />

<br>

```
$ cp file.txt file2.txt
```
  1. Creates file2.txt
  2. Copy contents of file.txt to file2.txt

* Requirement: file.txt should be present with/without some data.
* Without reading or writing onto file we can change the cursor position by using lseek().

#### Can i open same file from multiple process?
**Ans :** YES.

<img width="618" height="601" alt="image" src="https://github.com/user-attachments/assets/b8fe40f5-5555-41a9-ab3e-3fbf32946461" />

* While opening multiple process , we will have multiple PCB, multiple file objects, but all the file object will point to the same inode object of file.

#### Kernel uses which object to represent file?
**Ans :** Inode Object

#### Kernel usee which object to represent open file?
**Ans :** File Object

#### Is there any limit to number of files that can be opened from the program?
**Ans :** Depends upon the size of fd table i.e depends how many entries we can create in fd table.

## Standard I/O calls VS Basic I/O calls

<img width="736" height="651" alt="image" src="https://github.com/user-attachments/assets/2a1c8f87-9620-4080-bb42-778a46272925" />

| S.N | Standard I/O calls | Basic I/O Calls |
|---|---|---|
| 1. | They are standard C library calls which internally invokes basic I/O calls. | They are system calls. |
| 2. | They operate on file pointer. OR operates on object of file typedef <br> **Advantage of typedef:** <br> • No need to mention <br> • Struct keyword We can directly use structure <br> Tag name `FILE *fp` <br> `fp=open()` [file pointer (The return value of fopen is stored in this fp)] | They operate on fd or file descriptor. |
| 3. | They can access only normal files namely textual file & binary files. <br> e.g., mp4 -> Video player <br> -> Video player is a pgm which internally uses all different calls like fopene() <br> • mp3 Internally uses all the Basic I/O Calls. <br> Thus, text editor also internally uses all standard I/O Calls. | They can access normal, special as well as Device files. <br> • Application can never send req. directly to the device. First we need to send request to the drivers then to drivers will initialise the devices. <br> • Application can uses basic I/O calls on device driver to Send request to driver. |

<br>

* Other than basic i/o and standard i/o calls Memory Mapping call can be used to access file.
    1. File Mapping Technique
    2. Anonymous file mapping technique


#### Q) Implement your own cp command
**Ans :**
* It can be done using 3 ways
    1. Basic i/o calls
    2. Standard i/o calls
    3. Memory Mapping
        - mmap()
        - file mapping techinque

```
    file1.txt -> src file (400 bytes)
    file2.txt -> des file

    char buf[100];
    int srfd, dsfd;
    srfd = open("file.txt" , O_RDONLY);
    dsfd = open("file.txt", O_WRONLY | O_CREAT , 640);
    while( (x = read(srfd , buf , 100)) > 0)
    {
        write(dsfd , buf, x);
    }
```

* Once you reach end of the file read returns 0. The above condition becomes false and loop gets terminated.

#### Passing file name as CLA
* For passing CLA we need to modify the main program of the c-program

```
    main(int argc, char *argv[])
    {
        // base address of CLA is passed to the 2nd argument
        srfd = open(argv[1], O_RDONLY);
        dsfd = open(argv[2] , O_WRONLY | O_CREAT, 640);
    }
```

**NOTE :** Size of file is present in the inode object of file. Inode is a object of type struct inode. for inode object number use command ls -il
* Once the file is opened the inode object of corresponding file get copied form hard disk to kernel space of RAM

#### How do userspace applications get accessed to the content of inode object present in Kernel space?

**Ans :** By using Basic i/o call, `stat` or `fstat()`

#### How do you access to kernel object / kernel data structure or information present in Kernel space?

* There are various ways
    1. Multiple system calls.
    2. Shell commands.
    3. proc virtual filesystem entry.

<img width="615" height="312" alt="image" src="https://github.com/user-attachments/assets/87368a44-e467-4eda-9215-c76ea11d111b" />

<img width="828" height="572" alt="image" src="https://github.com/user-attachments/assets/50b1a55c-2e8c-4684-8f36-25689e0c24a5" />

* Ini