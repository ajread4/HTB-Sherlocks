# Operation Blackout 2025: Ghost Thread

Hack the Box Link: [here](https://app.hackthebox.com/sherlocks/Operation%2520Blackout%25202025%253A%2520Ghost%2520Thread?tab=play_sherlock)

1. What process injection technique did the attacker use?
*Answer*: Thread Local Storage 
* Reviewing the IDA Pro output of the inject.exe, the main function is called```public TlsCallback_0``` and the API calls contain VirtualAllocEx, WriteProcessMemory and CreateRemoteThread. 
2. Which Win32 API was used to take snapshots of all processes and threads on the system?
*Answer*: CreateToolhelp32Snapshot
* View the inject.exe module and find that the initial API calls start with a snapshot function and then proceed to do string commpares to find the eventual notepad.exe process. The API looks like ```CreateToolhelp32Snapshot ( TH32CS_SNAPPROCESS, 0 )```. 

3. Which process is the attacker's binary attempting to locate for payload injection?
*Answer*: notepad.exe 
* Looking within the API Monitor that had to be downloaded, the inject.exe module is string comparing all processes with ```Notepad.exe``` using lstringcmpIA. The calls look like ```lstrcmpiA ( "Notepad.exe", "Notepad.exe" )```. 

4. What is the process ID of the identified process?
*Answer*: 16224
* The dwProcessID parameter within the OpenProcess API call contains the PID. The call looks like ```OpenProcess ( PROCESS_CREATE_THREAD | PROCESS_QUERY_INFORMATION | PROCESS_VM_OPERATION | PROCESS_VM_READ | PROCESS_VM_WRITE, FALSE, 16224 )```. 

5. What is the size of the shellcode?
*Answer*: 511
* The dwSize or nSize paramters within the VirtualAllocEx or WriteProcessMemory API calls represent the size. The calls look like ```VirtualAllocEx ( 0x0000000000000268, NULL, 511, MEM_COMMIT, PAGE_EXECUTE_READ )``` and ```WriteProcessMemory ( 0x0000000000000268, 0x00000206583d0000, 0x00007ff746571000, 511, NULL )```. 

6. Which Win32 API was used to execute the injected payload in the identified process?
*Answer*: CreateRemoteThread
* Outside research allowed me to figure out that this is commonly used for execution and also a key indicator that this is Thread Local Storage process injection. The call looks like ```CreateRemoteThread ( 0x0000000000000268, NULL, 0, 0x00000206583d0000, NULL, 0, NULL )```. 


7. The injection method used by the attacker executes before the main() function is called. Which Win32 API is responsible for terminating the program before main() runs?
*Answer*: ExitProcess
* Looking through the IDA Pro output for the inject.exe, can see the last API call right before return as ```call    cs:ExitProcess```. IDA Pro shows a pretty good flow on how the executable is supposed to run. 