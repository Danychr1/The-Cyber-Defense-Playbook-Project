## Linux Environment Setup and Learning Approach
  - In the next part of our Cyber-Defense-Playbook-Project, we are going to focus on Linux, emphasizing hands-on labs to learn by doing.
  - The course approaches Linux from the perspective of investigating a compromised system, making the learning process more engaging than a basic command tutorial.
  - While many Linux distributions exist (e.g., Debian, Ubuntu, Fedora, Kali, Red Hat), mastering Bash (the Bourne-Again Shell) is critical because it ensures compatibility across most distributions.
  - Learning Bash provides the flexibility to effectively use any Linux distribution.

Here is a Venn diagram showing different Linux distributions and the central role of Bash:
<img width="891" height="852" alt="Linux Distributions and Bash" src="https://github.com/user-attachments/assets/e5566d91-4dd4-4740-be0f-498cfd1b05d5" />

## Linux Directory Structure Explained
* The root directory (/) forms the base of the Linux file system hierarchy, containing various directories and files.
* /bin: Stores essential binary executable files, which are commands that users can run.
    * The $PATH variable defines directories where the system searches for commands, and the which command can show a ### command's executable path.
    
* /boot: Contains files necessary for the Linux kernel and the boot manager, Grub. 
    * Linux updates often retain previous kernel versions, allowing users to select an older kernel if a new one causes issues.
      
* /dev: Represents devices as files, adhering to the Unix philosophy that "everything is a file."
    * Examples include terminals (/dev/pts/1), CPU, hard drives, and even /dev/urandom for generating random data.
    * Improper interaction, such as catting /dev/urandom, can crash a terminal session.
      
* /etc: Centralizes configuration files for nearly all programs and services on the system, which simplifies management compared to Windows.
  
* /home: Contains individual home directories for each user account on the system.
  
* /lib: Holds shared libraries of functionality that programs use to operate correctly.
    * The operating system dynamically links these libraries when programs require their functions.
      
* /media and /mnt: Used for mounting external or remote file systems. /mnt is the traditional mount point, while /media was introduced by developers.
  
* /opt: Typically hosts self-contained applications that bundle all their binaries, libraries, and configurations into a single directory.
  
* /proc: A virtual file system existing only in memory at runtime, containing information about running processes identified by Process IDs (PIDs).
  
* /root: The home directory specifically for the root user, accessible only with root privileges.
  
* /sbin: Contains system binaries or service binaries, used for system administration and maintenance tasks.
  
* /snap: A package manager for installing applications, although the proliferation of various package managers on Linux can be frustrating.
  
* /var: Stores variable data, most notably system log files.
    * Historically, /var was often on a separate drive to prevent continuous writes from log files from crashing the entire system upon disk failure.
      
* /usr: Contains user-accessible programs, libraries, and documentation, including /usr/bin, /usr/sbin, and /usr/games.
