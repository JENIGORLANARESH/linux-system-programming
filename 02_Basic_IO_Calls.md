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

**Question :**  How a user space application sends request to hardware?<br>
**Answer :** An application can never send request directly to hardware. It uses system calls(i.e basic io calls) to interact with the drivers.

**Question :** How do you make sure that only request to particular driver is sent?<br>
**Answer :** Every driver has unique device files. And the application has to use basic i/o on device file to send request to particular driver. <br>
And particualr driver initiates the corresponding hardware. This concept is applicable for window, android etc. as they all have user space as well as kernel space

serial port -> /drive/ttyso ttysl<br>
parallel port -> /dev/parpat<br>
HardDisk -> <br>
    - /dev/sao sdao , sdal <br>
    - /dev/sab , sac<br>

