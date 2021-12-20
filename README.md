# Core-Windows-Processes
## An overview of normal Windows processes

### System
1. PID -> 4
2. Image Path:  N/A
3. Parent Process:  None
4. Number of Instances:  One
5. User Account:  Local System
6. Start Time:  At boot time

Suspicious Activity:
1. A parent process (aside from System Idle Process (0))
2. Multiple instances of System. (Should only be 1 instance) 
3. A different PID. (Remember that the PID will always be PID 4)
4. Not running in Session 0

### smss.exe (Session Manager Subsystem) - Windows Session Manager
1. Creates new sessions
2. User-mode process
3. Starts the kernel mode and user mode of the Windows subsystem -> win32k.sys (kernel mode), winsrv.dll (user mode), and csrss.exe (user mode)
4. Starts -> csrss.exe (Windows subsystem) and wininit.exe in Session 0 for the operating system
          -> csrss.exe and winlogon.exe for Session 1, which is the user session
5. Image Path:  %SystemRoot%\System32\smss.exe
6. Parent Process:  System
7. Number of Instances:  One master instance and child instance per session. The child instance exits after creating the session.
8. User Account:  Local System
9. Start Time:  Within seconds of boot time for the master instance

Suspicious Activities:
1. A different parent process other than System(4)
2. Image path is different from C:\Windows\System32
3. More than 1 running process. (children self-terminate and exit after each new session)
4. User is not SYSTEM
5. Unexpected registry entries for Subsystem

### csrss.exe (Client Server Runtime Process)
1. Responsible for the Win32 console window and process thread creation and deletion
2. csrsrv.dll, basesrv.dll, and winsrv.dll are loaded
3. Makes the Windows API available to other processes, mapping drive letters, and handling the Windows shutdown process
4. Session 0 or Session 1
5. Image Path:  %SystemRoot%\System32\csrss.exe
6. Parent Process:  Created by an instance of smss.exe
7. Number of Instances:  Two or more
8. User Account:  Local System
9. Start Time:  Within seconds of boot time for the first 2 instances (for Session 0 and 1).  Start times for additional instances occur as new sessions are created, although often only Sessions 0 and 1 are created.

### wininit.exe
1. Launches services.exe (Service Control Manager), lsass.exe (Local Security Authority), and lsaiso.exe within Session 0
2. Image Path:  %SystemRoot%\System32\wininit.exe
3. Parent Process:  Created by an instance of smss.exe
4. Number of Instances:  One
5. User Account:  Local System
6. Start Time:  Within seconds of boot time

Suspicious Activities:
1. An actual parent process. (smss.exe calls this process and self-terminates)
2. Image file path other than C:\Windows\System32
3. Subtle misspellings to hide rogue process in plain sight
4. Multiple running instances
5. Not running as SYSTEM

### services.exe -> Service Control Manager (SCM)
1. Handle system services: loading services, interacting with services, starting/ending services, etc. 
2. Maintains a database that can be queried using a Windows built-in utility, 'sc.exe.' 
3. Parent to several other key processes: svchost.exe, spoolsv.exe, msmpeng.exe, dllhost.exe etc
4. Image Path:  %SystemRoot%\System32\services.exe
5. Parent Process:  wininit.exe
6. Number of Instances:  One
7. User Account:  Local System
8. Start Time:  Within seconds of boot time

Suspicious Activities:
1. A parent process other than wininit.exe
2. Image file path other than C:\Windows\System32
3. Subtle misspellings to hide rogue process in plain sight
4. Multiple running instances
5. Not running as SYSTEM

### svchost.exe -> Service Host (Host Process for Windows Services)
1. Responsible for hosting and managing Windows services
2. Services running in this process are implemented as DLLs
3. Image Path: %SystemRoot%\System32\svchost.exe
4. Parent Process: services.exe
5. Number of Instances: Many
6. User Account: Varies (SYSTEM, Network Service, Local Service) depending on the svchost.exe instance. In Windows 10 some instances can run as the logged-in user.
7. Start Time: Typically within seconds of boot time. Other instances can be started after boot

Suspicious Activities:
1. A parent process other than services.exe
2. Image file path other than C:\Windows\System32
3. Subtle misspellings to hide rogue process in plain sight
4. The absence of the -k parameter

### lsass.exe -> Local Security Authority Subsystem Service (LSASS)
1. Enforces the security policy on the system, vverifies users logging on to a Windows computer or server, handles password changes, and creates access tokens, writes to the Windows Security Log.
2. Creates security tokens for SAM (Security Account Manager), AD (Active Directory), and NETLOGON
3. Image Path:  %SystemRoot%\System32\lsass.exe
4. Parent Process:  wininit.exe
5. Number of Instances:  One
6. User Account:  Local System
7. Start Time:  Within seconds of boot time

Suspicious Activities:
1. A parent process other than wininit.exe
2. Image file path other than C:\Windows\System32
3. Subtle misspellings to hide rogue process in plain sight
4. Multiple running instances
5. Not running as SYSTEM

### winlogon.exe -> Windows Logon
1. Handles the Secure Attention Sequence (SAS) - the ALT+CTRL+DELETE key combination users press to enter their username & password
2. responsible for loading the user profile via userinit.exe
3. Image Path:  %SystemRoot%\System32\winlogon.exe
4. Parent Process:  Created by an instance of smss.exe that exits, so analysis tools usually do not provide the parent process name.
5. Number of Instances:  One or more
6. User Account:  Local System
7. Start Time:  Within seconds of boot time for the first instance (for Session 1).  Additional instances occur as new sessions are created, typically through Remote Desktop or Fast User Switching logons.

Suspicious Activities:
1. An actual parent process. (smss.exe calls this process and self-terminates)
2. Image file path other than C:\Windows\System32
3. Subtle misspellings to hide rogue process in plain sight
4. Not running as SYSTEM
5. Shell value in the registry other than explorer.exe

### explorer.exe -> Windows Explorer
1. Gives the user access to their folders and files, provides functionality to other features such as the Start Menu, Taskbar, etc. 
2. Many child processes
3. Image Path:  %SystemRoot%\explorer.exe
4. Parent Process:  Created by userinit.exe and exits
5. Number of Instances:  One or more per interactively logged-in user
6. User Account:  Logged-in user(s)
7. Start Time:  First instance when the first interactive user logon session begins

Suspicious Activities:
1. An actual parent process. (userinit.exe calls this process and exits)
2. Image file path other than C:\Windows
3. Running as an unknown user
4. Subtle misspellings to hide rogue process in plain sight
5. Outbound TCP/IP connections




