# Crown Jewel 1

Hack the Box Link: [CrownJewel-1](https://app.hackthebox.com/sherlocks/CrownJewel-1?tab=play_sherlock)

1. Attackers can abuse the vssadmin utility to create volume shadow snapshots and then extract sensitive files like NTDS.dit to bypass security mechanisms. Identify the time when the Volume Shadow Copy service entered a running state.
*Answer*: 2024-05-14 03:42:16
* Look at event ID 7036 within the System event logs to find the state set to running for the Volume Shadow Service. It presents itself within the log as ```Name: Volume Shadow Copy | Volume Shadow```. 

2. When a volume shadow snapshot is created, the Volume shadow copy service validates the privileges using the Machine account and enumerates User groups. Find the two user groups the volume shadow copy process queries and the machine account that did it.
*Answer*: Administrators, Backup Operators, DC01$
* Identify the VSSVC.exe execution (caller process) within the Security.evtx event log that occurs directly after the initial state change for question 1. The logs show up as event id 4799 with "A security-enabled local group membership was enumerated" with time created of 2024-05-14 03:42:16. The log also shows the various groups within the log as: ```Target: Builtin\Backup Operators (S-1-5-32-551)``` or ```Target: Builtin\Backup Operators (S-1-5-32-551)```. 

3. Identify the Process ID (in Decimal) of the volume shadow copy service process.
*Answer*: 4496
* Identify the VSSVC.exe execution (caller process) within the Security.evtx event log that occurs directly after the initial state change for question 1. The log contains with PID within ```CallerProcessId: 0x1190```. 

4. Find the assigned Volume ID/GUID value to the Shadow copy snapshot when it was mounted.
*Answer*: {06c4a997-cca8-11ed-a90f-000c295644f9}
* Look within the \Microsoft-Windows-NTFS.evtx logs for the events with Dismount Reason as User request. Event ID 303 should show the dismount. It will also show up with device name ```\\Device\\HarddiskVolumeShadowCopy1```. The time created of the event is 2024-05-14 03:44:22. I was not able to parse the event logs using EvtxECmd but the volume ID presents itself within ```{"Data":[{"@Name":"VolumeCorrelationId","#text":"06c4a997-cca8-11ed-a90f-000c295644f9"}``` in the log data. 

5. Identify the full path of the dumped NTDS database on disk.
*Answer*: C:\Users\Administrator\Documents\backup_sync_Dc\Ntds.dit
* Analyze the $MFT, search for filename of ntds.dit. The file was created at location C:\Users\Administrator\Documents\backup_sync_Dc on 2024-05-14 03:44:22. 

6. When was newly dumped ntds.dit created on disk?
*Answer*: 2024-05-14 03:44:22
* Use steps in 5. 

7. A registry hive was also dumped alongside the NTDS database. Which registry hive was dumped and what is its file size in bytes?
*Answer*: SYSTEM, 17563648
* Analyze the $MFT for actions directly after the Ntds.dit download. The next action has a file creation of SYSTEM to the C:\Users\Administrator\Documents\backup_sync_Dc folder path at 2024-05-14 03:44:22. 

