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
