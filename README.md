# Volatility3Writeup
Volatility Practical Investigations
Hey, what's up, cyberwarriors? Today, we're diving deep into the powerful memory forensics tool, Volatility, and a few helpful plugins to help us get started on our digital forensics journey! We'll be going over 18 different plugins, their use cases and how they can help us automate digital forensics by image. Awesome right? 

First things first volatility is huge, if you need any help you'll need google or in my case ChatGPT. Keep in mind ChatGPT only contains knowledge up to 2021, so keep things general and don't trust the syntax, because it has changed ALOT since then. The most helpful resource is going to be the volatility documentation. Listing plugins is your best friend. Here's some syntax:
python3 vol.py --help | grep -i windows.modscan
The only syntax you need to know to run a lot of the commands is:
python3 vol.py -f input your file and whatever plugin you wish to use. The above syntax is a good way to find that plugin. 
If you want to learn more about a specific plugin then I highly suggest using ChatGPT or google to give you an overview of that plugin. That's what I did personally for the modules to list their definitions in a simple manner.

----------------------------------------

imageinfo: Our starting point. It helps us identify the correct profile for the memory dump. 
Command: python3 vol.py -f <file> <OS.info>.
OS.info:
windows.info
linux.info
mac.info

----------------------------------------

Listing Processes:

pslist: Lists the processes running on the system. It's great for getting a snapshot of what was running. 
Command: python3 vol.py -f <file> windows.pslist.

----------------------------------------

psscan: Scans memory to find processes, including those unlinked and hidden from the process list. Great for spotting rootkits. Command: python3 vol.py -f <file> windows.psscan.

----------------------------------------

pstree: Visualizes the parent-child relationship between processes. Helpful to understand process lineage. 
Command: python3 vol.py -f <file> windows.pstree.

----------------------------------------

netstat: Lists network connections, useful for identifying potential command and control activity. 
Command: python3 vol.py -f <file> windows.netstat.

----------------------------------------

dlllist: Lists DLLs loaded by each process, useful for identifying malicious DLLs. 
Command: python3 vol.py -f <file> windows.dlllist.

----------------------------------------

malfind: Identifies potential code injection in processes. It can find hidden or injected DLLs. 
Command: python3 vol.py -f <file> windows.malfind.

----------------------------------------

Yarascan: Uses YARA rules to find specific patterns in memory. It's a key plugin for malware hunting. 
Command: python3 vol.py -f <file> windows.yarascan.

----------------------------------------

ssdt: Lists System Service Descriptor Table entries. Detects rootkits that hook system calls. 
Command: python3 vol.py -f <file> windows.ssdt.

----------------------------------------

modules: Lists loaded kernel modules. Helps spot malicious drivers. 
Command: python3 vol.py -f <file> windows.modules.

----------------------------------------

driverscan: Scans for driver objects in memory. Helps detect unsigned drivers and rootkits. 
Command: python3 vol.py -f <file> windows.driverscan.

----------------------------------------

modscan: Scans memory for kernel module objects. Finds unlinked modules. 
Command: python3 vol.py -f <file> windows.modscan.ModScan.

----------------------------------------

driverirp: Lists IRP handlers for driver objects. Helps detect rootkits hooking IRPs. 
Command: python3 vol.py -f <file> windows.driverirp.DriverIrp

----------------------------------------

callbacks: Lists registered callback routines. It's great for spotting malicious hooking. 
Command: python3 vol.py -f <file> windows.callbacks.Callbacks.

----------------------------------------

idt: Lists Interrupt Descriptor Table. Another way to find hooked interrupts by rootkits. 
Command: python3 vol.py -f <file> linux.check_idt.Check_idt.

----------------------------------------

apihooks: Detects hooked API functions. Great for finding in-memory patches. 
Command: ??? - Now deprecated AFAIK

----------------------------------------

moddump: Dumps a module from memory. Useful for extracting suspicious modules for further analysis. moddump is deprecated AFAIK and now has 3 separate plugins that can be used. 
Command: python3 vol.py -f <file> windows.cachedump.Cachedump
windows.dumpfiles.DumpFiles
   windows.hashdump.Hashdump
   windows.lsadump.Lsadump

----------------------------------------

handles: Lists open handles for each process. Helps identify resources used by processes. 
Command: python3 vol.py -f <file> windows.handles.Handles.

----------------------------------------

Case 1 - Bob! This isn't a horse!

----------------------------------------

(nihilobstat㉿NihilObstat)-[~/Tryhackme/Volatility/volatility3]
└─$ python3 vol.py -f 'Investigation-1.vmem' windows.info 
Volatility 3 Framework 2.5.0
Progress: 100.00 PDB scanning finished                                                
Variable Value

Kernel Base 0x804d7000
DTB 0x2fe000
Symbols file:///home/nihilobstat/Tryhackme/Volatility/volatility3/volatility3/symbols/windows/ntkrnlpa.pdb/30B5FB31AE7E4ACAABA750AA241FF331-1.json.xz
Is64Bit False
IsPAE True
layer_name 0 WindowsIntelPAE
memory_layer 1 FileLayer
KdDebuggerDataBlock 0x80545ae0
NTBuildLab 2600.xpsp.080413-2111
CSDVersion 3
KdVersionBlock 0x80545ab8
Major/Minor 15.2600
MachineType 332
KeNumberProcessors 1
SystemTime 2012-07-22 02:45:08
NtSystemRoot C:\WINDOWS
NtProductType NtProductWinNt
NtMajorVersion 5
NtMinorVersion 1
PE MajorOperatingSystemVersion 5
PE MinorOperatingSystemVersion 1
PE Machine 332
PE TimeDateStamp Sun Apr 13 18:31:06 2008
----------------------------------------
Question 1: What is the build version of the host machine in Case 001?
  Answer: 2600.xpsp.080413-2111
Question 2: At what time was the memory file acquired in Case 001?
  Answer: 2012-07-22 02:45:08

----------------------------------------

┌──(nihilobstat㉿NihilObstat)-[~/Tryhackme/Volatility/volatility3]
└─$ python3 vol.py -f 'Investigation-1.vmem' windows.pslist
Volatility 3 Framework 2.5.0
Progress: 100.00 PDB scanning finished             
PID PPID ImageFileName Offset(V) Threads Handles SessionId Wow64 CreateTime ExitTime File output

4 0 System 0x823c89c8 53 240 N/A False N/A N/A Disabled
368 4 smss.exe 0x822f1020 3 19 N/A False 2012-07-22 02:42:31.000000 N/A Disabled
584 368 csrss.exe 0x822a0598 9 326 0 False 2012-07-22 02:42:32.000000 N/A Disabled
608 368 winlogon.exe 0x82298700 23 519 0 False 2012-07-22 02:42:32.000000 N/A Disabled
652 608 services.exe 0x81e2ab28 16 243 0 False 2012-07-22 02:42:32.000000 N/A Disabled
664 608 lsass.exe 0x81e2a3b8 24 330 0 False 2012-07-22 02:42:32.000000 N/A Disabled
824 652 svchost.exe 0x82311360 20 194 0 False 2012-07-22 02:42:33.000000 N/A Disabled
908 652 svchost.exe 0x81e29ab8 9 226 0 False 2012-07-22 02:42:33.000000 N/A Disabled
1004 652 svchost.exe 0x823001d0 64 1118 0 False 2012-07-22 02:42:33.000000 N/A Disabled
1056 652 svchost.exe 0x821dfda0 5 60 0 False 2012-07-22 02:42:33.000000 N/A Disabled
1220 652 svchost.exe 0x82295650 15 197 0 False 2012-07-22 02:42:35.000000 N/A Disabled
1484 1464 explorer.exe 0x821dea70 17 415 0 False 2012-07-22 02:42:36.000000 N/A Disabled
1512 652 spoolsv.exe 0x81eb17b8 14 113 0 False 2012-07-22 02:42:36.000000 N/A Disabled
1640 1484 reader_sl.exe 0x81e7bda0 5 39 0 False 2012-07-22 02:42:36.000000 N/A Disabled
788 652 alg.exe 0x820e8da0 7 104 0 False 2012-07-22 02:43:01.000000 N/A Disabled
1136 1004 wuauclt.exe 0x821fcda0 8 173 0 False 2012-07-22 02:43:46.000000 N/A Disabled
1588 1004 wuauclt.exe 0x8205bda0 5 132 0 False 2012-07-22 02:44:01.000000 N/A Disabled
----------------------------------------
Question 3: What process can be considered suspicious in Case 001?
  Answer: reader_sl.exe - reader_sl instead of adobe reader process. It's similar, but not the same process the user intended to run. 

----------------------------------------

┌──(nihilobstat㉿NihilObstat)-[~/Tryhackme/Volatility/volatility3]
└─$ python3 vol.py -f 'Investigation-1.vmem' windows.pstree
Volatility 3 Framework 2.5.0
Progress: 100.00 PDB scanning finished             
PID PPID ImageFileName Offset(V) Threads Handles SessionId Wow64 CreateTime ExitTime

4 0 System 0x823c89c8 53 240 N/A False N/A N/A
* 368 4 smss.exe 0x822f1020 3 19 N/A False 2012-07-22 02:42:31.000000 N/A
** 584 368 csrss.exe 0x822a0598 9 326 0 False 2012-07-22 02:42:32.000000 N/A
** 608 368 winlogon.exe 0x82298700 23 519 0 False 2012-07-22 02:42:32.000000 N/A
*** 664 608 lsass.exe 0x81e2a3b8 24 330 0 False 2012-07-22 02:42:32.000000 N/A
*** 652 608 services.exe 0x81e2ab28 16 243 0 False 2012-07-22 02:42:32.000000 N/A
**** 1056 652 svchost.exe 0x821dfda0 5 60 0 False 2012-07-22 02:42:33.000000 N/A
**** 1220 652 svchost.exe 0x82295650 15 197 0 False 2012-07-22 02:42:35.000000 N/A
**** 1512 652 spoolsv.exe 0x81eb17b8 14 113 0 False 2012-07-22 02:42:36.000000 N/A
**** 908 652 svchost.exe 0x81e29ab8 9 226 0 False 2012-07-22 02:42:33.000000 N/A
**** 1004 652 svchost.exe 0x823001d0 64 1118 0 False 2012-07-22 02:42:33.000000 N/A
***** 1136 1004 wuauclt.exe 0x821fcda0 8 173 0 False 2012-07-22 02:43:46.000000 N/A
***** 1588 1004 wuauclt.exe 0x8205bda0 5 132 0 False 2012-07-22 02:44:01.000000 N/A
**** 788 652 alg.exe 0x820e8da0 7 104 0 False 2012-07-22 02:43:01.000000 N/A
**** 824 652 svchost.exe 0x82311360 20 194 0 False 2012-07-22 02:42:33.000000 N/A
1484 1464 explorer.exe 0x821dea70 17 415 0 False 2012-07-22 02:42:36.000000 N/A
* 1640 1484 reader_sl.exe 0x81e7bda0 5 39 0 False 2012-07-22 02:42:36.000000 N/A
----------------------------------------
Question 4: What is the parent process of the suspicious process in Case 001? 
  Answer: explorer.exe - The PPID for reader_sl.exe matches the PID of explorer.exe. 
Question 5: What is the PID of the suspicious process in Case 001?
  Answer: 1640
Question 6: What is the parent process PID in Case 001?
  Answer: 1484

----------------------------------------

┌──(nihilobstat㉿NihilObstat)-[~/Tryhackme/Volatility/volatility3]
└─$ python3 vol.py --help | grep memmap
   windows.memmap.Memmap
new syntax: python3 vol.py -f 'Investigation-1.vmem' -o /tmp/ windows.memmap.Memmap --pid 1640 --dump
┌──(nihilobstat㉿NihilObstat)-[~/Tryhackme/Volatility/volatility3]
└─$ strings /tmp/*.dmp | grep -i "user-agent"
User-Agent
User-Agent: Mozilla/5.0 (Windows; U; MSIE 7.0; Windows NT 6.0; en-US)
cs(User-Agent)
USER-AGENT:
User-Agent:
----------------------------------------
the -i is very important in this case, because grep will search for a pattern in each file or standard input using this flag. Without this flag we get the wrong output. 
Question 7: What user-agent was employed by the adversary in Case 001?
  Answer: Mozilla/5.0 (Windows; U; MSIE 7.0; Windows NT 6.0; en-US)

----------------------------------------

┌──(nihilobstat㉿NihilObstat)-[~/Tryhackme/Volatility/volatility3]
└─$ strings /tmp/*.dmp | grep -i "chase"
*chase.com*
*chase.com*
*chase.com*
action="https://mfasa.chase.com/auth/fcc/login" method="post" onsubmit="
*chaseonline.chase.com/MyAccounts.*
<!-- BEGIN Global Navigation table --><table cellspacing="0" cellpadding="0" border="0" class="fullwidth" summary="global navigation"><tr><td><a href="http://www.chase.com/" id="siteLogo"><img src="https://chaseonline.chase.com/images//ChaseNew.gif" alt="Chase Online Logo" style="margin: 17px 17px 17px 17px;"/></a></td><td class="globalnav"><a id="homelink" href="JavaScript:document.location.href='http://www.chase.com/';" class="globalnavlinks">Chase.com</a> </td>
<td class="spacerw25"> <iframe name="ifr1" id="ifr1" src="https://www.chase.com/online/Home/images/chaseNewlogo.gif" frameborder="0" width="1px" height="1px" style="display:none"></iframe></td>
<td class="steptexton" align="center" title="You are on step one of three. There is at least one page per step.">Instructions<img src="https://chaseonline.chase.com/images//spacer.gif" alt="You are on step one of three. There is at least one page per step.." width="1" height="1"/></td>
<td class="steptextoff" align="center" title="Step two of three has not been completed.">Credit Card confirmation<img src="https://chaseonline.chase.com/images//spacer.gif" alt="Step two of three has not been completed." width="1" height="1"/></td>
<td class="steptextoff" align="center" title="Step three of three has not been completed.">Identity confirmation<img src="https://chaseonline.chase.com/images//spacer.gif" alt="Step three of three has not been completed." width="1" height="1"/></td>
<span class="instrtexthead">Why have I reached this page? <img src="https://chaseonline.chase.com/content/ecpweb/sso/image/lock2.gif" alt="Your information is securely transmitted via https (SSL) 128-bit Encryption" title="Your information is securely transmitted via https (S S L) 128-bit Encryption"> </span><span class="instrtext">We take your security seriously. Please follow this brief two-step process to help us verify your identity and keep your account(s) safe. </span>
<td class="steptextoff" align="center" title="Step one of three has been completed.">Instructions<img src="https://chaseonline.chase.com/images//spacer.gif" alt="You are on step one of three. There is at least one page per step.." width="1" height="1"/></td>
<td class="steptexton" align="center" title="You are on step two of three. There is at least one page per step.">Credit Card confirmation<img src="https://chaseonline.chase.com/images//spacer.gif" alt="Step two of three has not been completed." width="1" height="1"/></td>
<td class="steptextoff" align="center" title="Step three of three has not been completed.">Identity confirmation<img src="https://chaseonline.chase.com/images//spacer.gif" alt="Step three of three has not been completed." width="1" height="1"/></td>
<span class="instrtexthead">Enter your card information <img src="https://chaseonline.chase.com/content/ecpweb/sso/image/lock2.gif" alt="Your information is securely transmitted via https (SSL) 128-bit Encryption" title="Your information is securely transmitted via https (SSL) 128-bit Encryption"> </span>
<td class="steptextoff" align="center" title="Step one of three has been completed.">Instructions<img src="https://chaseonline.chase.com/images//spacer.gif" alt="You are on step one of three. There is at least one page per step.." width="1" height="1"/></td>
<td class="steptextoff" align="center" title="Step two of three has been completed.">Credit Card confirmation<img src="https://chaseonline.chase.com/images//spacer.gif" alt="Step two of three has not been completed." width="1" height="1"/></td>
<td class="steptexton" align="center" title="You are on step three of three. There is at least one page per step.">Identity confirmation<img src="https://chaseonline.chase.com/images//spacer.gif" alt="Step three of three has not been completed." width="1" height="1"/></td>
<span class="instrtexthead">Confirm your personality <img src="https://chaseonline.chase.com/content/ecpweb/sso/image/lock2.gif" alt="Your information is securely transmitted via https (S S L) 128-bit Encryption" title="Your information is securely transmitted via https (S S L) 128-bit Encryption"> </span>
<!--Footer--><table border="0" cellspacing="0" cellpadding="0" class="fullwidth" summary="terms of use link and copyright"><tr><td class="spacerh10" colspan="3"> </td></tr><tr><td style="width:30%; vertical-align:top"> </td><td align="center" width="40%" valign="top"><span class="footertext"><a id="SecurityLink" href="JavaScript:document.location.href='http://www.chase.com//ccp/index.jsp?pg_name=ccpmapp/shared/assets/page/security_measures';" onBlur="window.status='';return true" onMouseOver="window.status='';return true" onFocus="window.status='';return true" onMouseOut="window.status='';return true">Security</a> | <!-- mp_trans_remove_start --><a id="TermsLink" href="JavaScript:document.location.href='http://www.chase.com//ccp/index.jsp?pg_name=ccpmapp/shared/assets/page/terms';" onBlur="window.status='';return true" onMouseOver="window.status='';return true" onFocus="window.status='';return true" onMouseOut="window.status='';return true">Terms of Use</a> <!-- mp_trans_remove_end --><!-- mp_trans_add<a id="TermsLink" href="JavaScript:document.location.href='https://www.chase.com/index.jsp?pg_name=ccpmapp/spanish/resources/page/terms';" onBlur="window.status='';return true" onMouseOver="window.status='';return true" onFocus="window.status='';return true" onMouseOut="window.status='';return true">Terms of Use</a> --></span></td><td style="text-align:center; width:30%; vertical-align:top"> </td></tr></table><div class="printable"><table border="0" cellspacing="0" cellpadding="0" class="fullwidth"><tr><td class="spacerh10"> </td></tr><tr><td align="center" class="footertext"> 
2011 JPMorgan Chase & Co.</td></tr><tr><td class="spacerh10"> </td></tr></table></div><!--END Footer-->
<iframe name="ifr2" id="ifr2" src="https://www.chase.com/online/Home/images/chaseNewlogo.gif" frameborder="0" width="1px" height="1px" style="display:none"></iframe>
<form id="ge93Zid02L5" name="ge93Zid02L5" action="https://www.chase.com/online/Home/images/chaseNewlogo.gif" target="ifr2" method="POST">
url: "https://chaseonline.chase.com/gw/secure/ena",
*chase.com*
*chase.com*
*chase.com*
*chase.com*
action="https://mfasa.chase.com/auth/fcc/login" method="post" onsubmit="
*chaseonline.chase.com/MyAccounts.*
<!-- BEGIN Global Navigation table --><table cellspacing="0" cellpadding="0" border="0" class="fullwidth" summary="global navigation"><tr><td><a href="http://www.chase.com/" id="siteLogo"><img src="https://chaseonline.chase.com/images//ChaseNew.gif" alt="Chase Online Logo" style="margin: 17px 17px 17px 17px;"/></a></td><td class="globalnav"><a id="homelink" href="JavaScript:document.location.href='http://www.chase.com/';" class="globalnavlinks">Chase.com</a> </td>
<td class="spacerw25"> <iframe name="ifr1" id="ifr1" src="https://www.chase.com/online/Home/images/chaseNewlogo.gif" frameborder="0" width="1px" height="1px" style="display:none"></iframe></td>
<td class="steptexton" align="center" title="You are on step one of three. There is at least one page per step.">Instructions<img src="https://chaseonline.chase.com/images//spacer.gif" alt="You are on step one of three. There is at least one page per step.." width="1" height="1"/></td>
<td class="steptextoff" align="center" title="Step two of three has not been completed.">Credit Card confirmation<img src="https://chaseonline.chase.com/images//spacer.gif" alt="Step two of three has not been completed." width="1" height="1"/></td>
<td class="steptextoff" align="center" title="Step three of three has not been completed.">Identity confirmation<img src="https://chaseonline.chase.com/images//spacer.gif" alt="Step three of three has not been completed." width="1" height="1"/></td>
<span class="instrtexthead">Why have I reached this page? <img src="https://chaseonline.chase.com/content/ecpweb/sso/image/lock2.gif" alt="Your information is securely transmitted via https (SSL) 128-bit Encryption" title="Your information is securely transmitted via https (S S L) 128-bit Encryption"> </span><span class="instrtext">We take your security seriously. Please follow this brief two-step process to help us verify your identity and keep your account(s) safe. </span>
<td class="steptextoff" align="center" title="Step one of three has been completed.">Instructions<img src="https://chaseonline.chase.com/images//spacer.gif" alt="You are on step one of three. There is at least one page per step.." width="1" height="1"/></td>
<td class="steptexton" align="center" title="You are on step two of three. There is at least one page per step.">Credit Card confirmation<img src="https://chaseonline.chase.com/images//spacer.gif" alt="Step two of three has not been completed." width="1" height="1"/></td>
<td class="steptextoff" align="center" title="Step three of three has not been completed.">Identity confirmation<img src="https://chaseonline.chase.com/images//spacer.gif" alt="Step three of three has not been completed." width="1" height="1"/></td>
<span class="instrtexthead">Enter your card information <img src="https://chaseonline.chase.com/content/ecpweb/sso/image/lock2.gif" alt="Your information is securely transmitted via https (SSL) 128-bit Encryption" title="Your information is securely transmitted via https (SSL) 128-bit Encryption"> </span>
<td class="steptextoff" align="center" title="Step one of three has been completed.">Instructions<img src="https://chaseonline.chase.com/images//spacer.gif" alt="You are on step one of three. There is at least one page per step.." width="1" height="1"/></td>
<td class="steptextoff" align="center" title="Step two of three has been completed.">Credit Card confirmation<img src="https://chaseonline.chase.com/images//spacer.gif" alt="Step two of three has not been completed." width="1" height="1"/></td>
<td class="steptexton" align="center" title="You are on step three of three. There is at least one page per step.">Identity confirmation<img src="https://chaseonline.chase.com/images//spacer.gif" alt="Step three of three has not been completed." width="1" height="1"/></td>
<span class="instrtexthead">Confirm your personality <img src="https://chaseonline.chase.com/content/ecpweb/sso/image/lock2.gif" alt="Your information is securely transmitted via https (S S L) 128-bit Encryption" title="Your information is securely transmitted via https (S S L) 128-bit Encryption"> </span>
<!--Footer--><table border="0" cellspacing="0" cellpadding="0" class="fullwidth" summary="terms of use link and copyright"><tr><td class="spacerh10" colspan="3"> </td></tr><tr><td style="width:30%; vertical-align:top"> </td><td align="center" width="40%" valign="top"><span class="footertext"><a id="SecurityLink" href="JavaScript:document.location.href='http://www.chase.com//ccp/index.jsp?pg_name=ccpmapp/shared/assets/page/security_measures';" onBlur="window.status='';return true" onMouseOver="window.status='';return true" onFocus="window.status='';return true" onMouseOut="window.status='';return true">Security</a> | <!-- mp_trans_remove_start --><a id="TermsLink" href="JavaScript:document.location.href='http://www.chase.com//ccp/index.jsp?pg_name=ccpmapp/shared/assets/page/terms';" onBlur="window.status='';return true" onMouseOver="window.status='';return true" onFocus="window.status='';return true" onMouseOut="window.status='';return true">Terms of Use</a> <!-- mp_trans_remove_end --><!-- mp_trans_add<a id="TermsLink" href="JavaScript:document.location.href='https://www.chase.com/index.jsp?pg_name=ccpmapp/spanish/resources/page/terms';" onBlur="window.status='';return true" onMouseOver="window.status='';return true" onFocus="window.status='';return true" onMouseOut="window.status='';return true">Terms of Use</a> --></span></td><td style="text-align:center; width:30%; vertical-align:top"> </td></tr></table><div class="printable"><table border="0" cellspacing="0" cellpadding="0" class="fullwidth"><tr><td class="spacerh10"> </td></tr><tr><td align="center" class="footertext"> 
2011 JPMorgan Chase & Co.</td></tr><tr><td class="spacerh10"> </td></tr></table></div><!--END Footer-->
<iframe name="ifr2" id="ifr2" src="https://www.chase.com/online/Home/images/chaseNewlogo.gif" frameborder="0" width="1px" height="1px" style="display:none"></iframe>
<form id="ge93Zid02L5" name="ge93Zid02L5" action="https://www.chase.com/online/Home/images/chaseNewlogo.gif" target="ifr2" method="POST">
url: "https://chaseonline.chase.com/gw/secure/ena",
*chase.com*
*chase.com*
*chase.com*
action="https://mfasa.chase.com/auth/fcc/login" method="post" onsubmit="
*chaseonline.chase.com/MyAccounts.*
<!-- BEGIN Global Navigation table --><table cellspacing="0" cellpadding="0" border="0" class="fullwidth" summary="global navigation"><tr><td><a href="http://www.chase.com/" id="siteLogo"><img src="https://chaseonline.chase.com/images//ChaseNew.gif" alt="Chase Online Logo" style="margin: 17px 17px 17px 17px;"/></a></td><td class="globalnav"><a id="homelink" href="JavaScript:document.location.href='http://www.chase.com/';" class="globalnavlinks">Chase.com</a> </td>
<td class="spacerw25"> <iframe name="ifr1" id="ifr1" src="https://www.chase.com/online/Home/images/chaseNewlogo.gif" frameborder="0" width="1px" height="1px" style="display:none"></iframe></td>
<td class="steptexton" align="center" title="You are on step one of three. There is at least one page per step.">Instructions<img src="https://chaseonline.chase.com/images//spacer.gif" alt="You are on step one of three. There is at least one page per step.." width="1" height="1"/></td>
<td class="steptextoff" align="center" title="Step two of three has not been completed.">Credit Card confirmation<img src="https://chaseonline.chase.com/images//spacer.gif" alt="Step two of three has not been completed." width="1" height="1"/></td>
<td class="steptextoff" align="center" title="Step three of three has not been completed.">Identity confirmation<img src="https://chaseonline.chase.com/images//spacer.gif" alt="Step three of three has not been completed." width="1" height="1"/></td>
<span class="instrtexthead">Why have I reached this page? <img src="https://chaseonline.chase.com/content/ecpweb/sso/image/lock2.gif" alt="Your information is securely transmitted via https (SSL) 128-bit Encryption" title="Your information is securely transmitted via https (S S L) 128-bit Encryption"> </span><span class="instrtext">We take your security seriously. Please follow this brief two-step process to help us verify your identity and keep your account(s) safe. </span>
<td class="steptextoff" align="center" title="Step one of three has been completed.">Instructions<img src="https://chaseonline.chase.com/images//spacer.gif" alt="You are on step one of three. There is at least one page per step.." width="1" height="1"/></td>
<td class="steptexton" align="center" title="You are on step two of three. There is at least one page per step.">Credit Card confirmation<img src="https://chaseonline.chase.com/images//spacer.gif" alt="Step two of three has not been completed." width="1" height="1"/></td>
<td class="steptextoff" align="center" title="Step three of three has not been completed.">Identity confirmation<img src="https://chaseonline.chase.com/images//spacer.gif" alt="Step three of three has not been completed." width="1" height="1"/></td>
<span class="instrtexthead">Enter your card information <img src="https://chaseonline.chase.com/content/ecpweb/sso/image/lock2.gif" alt="Your information is securely transmitted via https (SSL) 128-bit Encryption" title="Your information is securely transmitted via https (SSL) 128-bit Encryption"> </span>
<td class="steptextoff" align="center" title="Step one of three has been completed.">Instructions<img src="https://chaseonline.chase.com/images//spacer.gif" alt="You are on step one of three. There is at least one page per step.." width="1" height="1"/></td>
<td class="steptextoff" align="center" title="Step two of three has been completed.">Credit Card confirmation<img src="https://chaseonline.chase.com/images//spacer.gif" alt="Step two of three has not been completed." width="1" height="1"/></td>
<td class="steptexton" align="center" title="You are on step three of three. There is at least one page per step.">Identity confirmation<img src="https://chaseonline.chase.com/images//spacer.gif" alt="Step three of three has not been completed." width="1" height="1"/></td>
<span class="instrtexthead">Confirm your personality <img src="https://chaseonline.chase.com/content/ecpweb/sso/image/lock2.gif" alt="Your information is securely transmitted via https (S S L) 128-bit Encryption" title="Your information is securely transmitted via https (S S L) 128-bit Encryption"> </span>
<!--Footer--><table border="0" cellspacing="0" cellpadding="0" class="fullwidth" summary="terms of use link and copyright"><tr><td class="spacerh10" colspan="3"> </td></tr><tr><td style="width:30%; vertical-align:top"> </td><td align="center" width="40%" valign="top"><span class="footertext"><a id="SecurityLink" href="JavaScript:document.location.href='http://www.chase.com//ccp/index.jsp?pg_name=ccpmapp/shared/assets/page/security_measures';" onBlur="window.status='';return true" onMouseOver="window.status='';return true" onFocus="window.status='';return true" onMouseOut="window.status='';return true">Security</a> | <!-- mp_trans_remove_start --><a id="TermsLink" href="JavaScript:document.location.href='http://www.chase.com//ccp/index.jsp?pg_name=ccpmapp/shared/assets/page/terms';" onBlur="window.status='';return true" onMouseOver="window.status='';return true" onFocus="window.status='';return true" onMouseOut="window.status='';return true">Terms of Use</a> <!-- mp_trans_remove_end --><!-- mp_trans_add<a id="TermsLink" href="JavaScript:document.location.href='https://www.chase.com/index.jsp?pg_name=ccpmapp/spanish/resources/page/terms';" onBlur="window.status='';return true" onMouseOver="window.status='';return true" onFocus="window.status='';return true" onMouseOut="window.status='';return true">Terms of Use</a> --></span></td><td style="text-align:center; width:30%; vertical-align:top"> </td></tr></table><div class="printable"><table border="0" cellspacing="0" cellpadding="0" class="fullwidth"><tr><td class="spacerh10"> </td></tr><tr><td align="center" class="footertext"> 
2011 JPMorgan Chase & Co.</td></tr><tr><td class="spacerh10"> </td></tr></table></div><!--END Footer-->
<iframe name="ifr2" id="ifr2" src="https://www.chase.com/online/Home/images/chaseNewlogo.gif" frameborder="0" width="1px" height="1px" style="display:none"></iframe>
<form id="ge93Zid02L5" name="ge93Zid02L5" action="https://www.chase.com/online/Home/images/chaseNewlogo.gif" target="ifr2" method="POST">
url: "https://chaseonline.chase.com/gw/secure/ena",
----------------------------------------
Question 8: Was Chase Bank one of the suspicious bank domains found in Case 001? (Y/N)
    Answer: Y - I kind of dislike this question, because it takes the whole detective situation out of your hands. Looking back at the Splunk room, it's clear we could have had an opportunity to combine that skillset in this room to analyze http traffic against common IOCs.

----------------------------------------

┌──(nihilobstat㉿NihilObstat)-[~/Tryhackme/Volatility/volatility3]
└─$ python3 vol.py -f 'Investigation-2.raw' windows.info
Volatility 3 Framework 2.5.0
Progress: 100.00 PDB scanning finished                                                
Variable Value

Kernel Base 0x804d7000
DTB 0x39000
Symbols file:///home/nihilobstat/Tryhackme/Volatility/volatility3/volatility3/symbols/windows/ntoskrnl.pdb/423320282DB842E7BA2B0BFC86A84D75-2.json.xz
Is64Bit False
IsPAE False
layer_name 0 WindowsIntel
memory_layer 1 FileLayer
KdDebuggerDataBlock 0x8054cf60
NTBuildLab 2600.xpsp_sp3_qfe.130704-0421
CSDVersion 3
KdVersionBlock 0x8054cf38
Major/Minor 15.2600
MachineType 332
KeNumberProcessors 1
SystemTime 2017-05-12 21:26:32
NtSystemRoot C:\WINDOWS
NtProductType NtProductWinNt
NtMajorVersion 5
NtMinorVersion 1
PE MajorOperatingSystemVersion 5
PE MinorOperatingSystemVersion 1
PE Machine 332
PE TimeDateStamp Thu Jul 4 02:58:58 2013

┌──(nihilobstat㉿NihilObstat)-[~/Tryhackme/Volatility/volatility3]
└─$ python3 vol.py -f 'Investigation-2.raw' windows.pslist
Volatility 3 Framework 2.5.0
Progress: 100.00 PDB scanning finished             
PID PPID ImageFileName Offset(V) Threads Handles SessionId Wow64 CreateTime ExitTime File output

4 0 System 0x823c8830 51 244 N/A False N/A N/A Disabled
348 4 smss.exe 0x82169020 3 19 N/A False 2017-05-12 21:21:55.000000 N/A Disabled
596 348 csrss.exe 0x82161da0 12 352 0 False 2017-05-12 21:22:00.000000 N/A Disabled
620 348 winlogon.exe 0x8216e020 23 536 0 False 2017-05-12 21:22:01.000000 N/A Disabled
664 620 services.exe 0x821937f0 15 265 0 False 2017-05-12 21:22:01.000000 N/A Disabled
676 620 lsass.exe 0x82191658 23 353 0 False 2017-05-12 21:22:01.000000 N/A Disabled
836 664 svchost.exe 0x8221a2c0 19 211 0 False 2017-05-12 21:22:02.000000 N/A Disabled
904 664 svchost.exe 0x821b5230 9 227 0 False 2017-05-12 21:22:03.000000 N/A Disabled
1024 664 svchost.exe 0x821af7e8 79 1366 0 False 2017-05-12 21:22:03.000000 N/A Disabled
1084 664 svchost.exe 0x8203b7a8 6 72 0 False 2017-05-12 21:22:03.000000 N/A Disabled
1152 664 svchost.exe 0x821bea78 10 173 0 False 2017-05-12 21:22:06.000000 N/A Disabled
1484 664 spoolsv.exe 0x821e2da0 14 124 0 False 2017-05-12 21:22:09.000000 N/A Disabled
1636 1608 explorer.exe 0x821d9da0 11 331 0 False 2017-05-12 21:22:10.000000 N/A Disabled
1940 1636 tasksche.exe 0x82218da0 7 51 0 False 2017-05-12 21:22:14.000000 N/A Disabled
1956 1636 ctfmon.exe 0x82231da0 1 86 0 False 2017-05-12 21:22:14.000000 N/A Disabled
260 664 svchost.exe 0x81fb95d8 5 105 0 False 2017-05-12 21:22:18.000000 N/A Disabled
740 1940 @WanaDecryptor@ 0x81fde308 2 70 0 False 2017-05-12 21:22:22.000000 N/A Disabled
1768 1024 wuauclt.exe 0x81f747c0 7 132 0 False 2017-05-12 21:22:52.000000 N/A Disabled
544 664 alg.exe 0x82010020 6 101 0 False 2017-05-12 21:22:55.000000 N/A Disabled
1168 1024 wscntfy.exe 0x81fea8a0 1 37 0 False 2017-05-12 21:22:56.000000 N/A Disabled
----------------------------------------
Question 9: What suspicious process is running at PID 740 in Case 002?
  Answer: @WanaDecryptor@

----------------------------------------

┌──(nihilobstat㉿NihilObstat)-[~/Tryhackme/Volatility/volatility3]
└─$ python3 vol.py -f 'Investigation-2.raw' windows.dlllist --pid 740
Volatility 3 Framework 2.5.0
Progress: 100.00 PDB scanning finished             
PID Process Base Size Name Path LoadTime File output

740 @WanaDecryptor@ 0x400000 0x3d000 @WanaDecryptor@.exe C:\Intel\ivecuqmanpnirkt615\@WanaDecryptor@.exe N/A Disabled
740 @WanaDecryptor@ 0x7c900000 0xb2000 ntdll.dll C:\WINDOWS\system32\ntdll.dll N/A Disabled
740 @WanaDecryptor@ 0x7c800000 0xf6000 kernel32.dll C:\WINDOWS\system32\kernel32.dll N/A Disabled
740 @WanaDecryptor@ 0x73dd0000 0xf2000 MFC42.DLL C:\WINDOWS\system32\MFC42.DLL N/A Disabled
740 @WanaDecryptor@ 0x77c10000 0x58000 msvcrt.dll C:\WINDOWS\system32\msvcrt.dll N/A Disabled
740 @WanaDecryptor@ 0x77f10000 0x49000 GDI32.dll C:\WINDOWS\system32\GDI32.dll N/A Disabled
740 @WanaDecryptor@ 0x7e410000 0x91000 USER32.dll C:\WINDOWS\system32\USER32.dll N/A Disabled
740 @WanaDecryptor@ 0x77dd0000 0x9b000 ADVAPI32.dll C:\WINDOWS\system32\ADVAPI32.dll N/A Disabled
740 @WanaDecryptor@ 0x77e70000 0x93000 RPCRT4.dll C:\WINDOWS\system32\RPCRT4.dll N/A Disabled
740 @WanaDecryptor@ 0x77fe0000 0x11000 Secur32.dll C:\WINDOWS\system32\Secur32.dll N/A Disabled
740 @WanaDecryptor@ 0x7c9c0000 0x818000 SHELL32.dll C:\WINDOWS\system32\SHELL32.dll N/A Disabled
740 @WanaDecryptor@ 0x77f60000 0x76000 SHLWAPI.dll C:\WINDOWS\system32\SHLWAPI.dll N/A Disabled
740 @WanaDecryptor@ 0x773d0000 0x103000 COMCTL32.dll C:\WINDOWS\WinSxS\X86_Microsoft.Windows.Common-Controls_6595b64144ccf1df_6.0.2600.6028_x-ww_61e65202\COMCTL32.dll N/A Disabled
740 @WanaDecryptor@ 0x77120000 0x8b000 OLEAUT32.dll C:\WINDOWS\system32\OLEAUT32.dll N/A Disabled
740 @WanaDecryptor@ 0x774e0000 0x13e000 ole32.dll C:\WINDOWS\system32\ole32.dll N/A Disabled
740 @WanaDecryptor@ 0x78130000 0x134000 urlmon.dll C:\WINDOWS\system32\urlmon.dll N/A Disabled
740 @WanaDecryptor@ 0x3dfd0000 0x1ec000 iertutil.dll C:\WINDOWS\system32\iertutil.dll N/A Disabled
740 @WanaDecryptor@ 0x76080000 0x65000 MSVCP60.dll C:\WINDOWS\system32\MSVCP60.dll N/A Disabled
740 @WanaDecryptor@ 0x71ab0000 0x17000 WS2_32.dll C:\WINDOWS\system32\WS2_32.dll N/A Disabled
740 @WanaDecryptor@ 0x71aa0000 0x8000 WS2HELP.dll C:\WINDOWS\system32\WS2HELP.dll N/A Disabled
740 @WanaDecryptor@ 0x3d930000 0xe7000 WININET.dll C:\WINDOWS\system32\WININET.dll N/A Disabled
740 @WanaDecryptor@ 0x340000 0x9000 Normaliz.dll C:\WINDOWS\system32\Normaliz.dll N/A Disabled
740 @WanaDecryptor@ 0x76390000 0x1d000 IMM32.DLL C:\WINDOWS\system32\IMM32.DLL N/A Disabled
740 @WanaDecryptor@ 0x629c0000 0x9000 LPK.DLL C:\WINDOWS\system32\LPK.DLL N/A Disabled
740 @WanaDecryptor@ 0x74d90000 0x6b000 USP10.dll C:\WINDOWS\system32\USP10.dll N/A Disabled
740 @WanaDecryptor@ 0x732e0000 0x5000 RICHED32.DLL C:\WINDOWS\system32\RICHED32.DLL N/A Disabled
740 @WanaDecryptor@ 0x74e30000 0x6d000 RICHED20.dll C:\WINDOWS\system32\RICHED20.dll N/A Disabled
740 @WanaDecryptor@ 0x5ad70000 0x38000 uxtheme.dll C:\WINDOWS\system32\uxtheme.dll N/A Disabled
740 @WanaDecryptor@ 0x74720000 0x4c000 MSCTF.dll C:\WINDOWS\system32\MSCTF.dll N/A Disabled
740 @WanaDecryptor@ 0x755c0000 0x2e000 msctfime.ime C:\WINDOWS\system32\msctfime.ime N/A Disabled
740 @WanaDecryptor@ 0x769c0000 0xb4000 USERENV.dll C:\WINDOWS\system32\USERENV.dll N/A Disabled
740 @WanaDecryptor@ 0xea0000 0x29000 msls31.dll C:\WINDOWS\system32\msls31.dll N/A Disabled
----------------------------------------
Question 10: What is the full path of the suspicious binary in PID 740 in Case 002?
Answer: C:\Intel\ivecuqmanpnirkt615\@WanaDecryptor@.exe

----------------------------------------

┌──(nihilobstat㉿NihilObstat)-[~/Tryhackme/Volatility/volatility3]
└─$ python3 vol.py -f 'Investigation-2.raw' windows.pstree
Volatility 3 Framework 2.5.0
Progress: 100.00 PDB scanning finished             
PID PPID ImageFileName Offset(V) Threads Handles SessionId Wow64 CreateTime ExitTime

4 0 System 0x823c8830 51 244 N/A False N/A N/A
* 348 4 smss.exe 0x82169020 3 19 N/A False 2017-05-12 21:21:55.000000 N/A
** 620 348 winlogon.exe 0x8216e020 23 536 0 False 2017-05-12 21:22:01.000000 N/A
*** 664 620 services.exe 0x821937f0 15 265 0 False 2017-05-12 21:22:01.000000 N/A
**** 1024 664 svchost.exe 0x821af7e8 79 1366 0 False 2017-05-12 21:22:03.000000 N/A
***** 1768 1024 wuauclt.exe 0x81f747c0 7 132 0 False 2017-05-12 21:22:52.000000 N/A
***** 1168 1024 wscntfy.exe 0x81fea8a0 1 37 0 False 2017-05-12 21:22:56.000000 N/A
**** 1152 664 svchost.exe 0x821bea78 10 173 0 False 2017-05-12 21:22:06.000000 N/A
**** 544 664 alg.exe 0x82010020 6 101 0 False 2017-05-12 21:22:55.000000 N/A
**** 836 664 svchost.exe 0x8221a2c0 19 211 0 False 2017-05-12 21:22:02.000000 N/A
**** 260 664 svchost.exe 0x81fb95d8 5 105 0 False 2017-05-12 21:22:18.000000 N/A
**** 904 664 svchost.exe 0x821b5230 9 227 0 False 2017-05-12 21:22:03.000000 N/A
**** 1484 664 spoolsv.exe 0x821e2da0 14 124 0 False 2017-05-12 21:22:09.000000 N/A
**** 1084 664 svchost.exe 0x8203b7a8 6 72 0 False 2017-05-12 21:22:03.000000 N/A
*** 676 620 lsass.exe 0x82191658 23 353 0 False 2017-05-12 21:22:01.000000 N/A
** 596 348 csrss.exe 0x82161da0 12 352 0 False 2017-05-12 21:22:00.000000 N/A
1636 1608 explorer.exe 0x821d9da0 11 331 0 False 2017-05-12 21:22:10.000000 N/A
* 1956 1636 ctfmon.exe 0x82231da0 1 86 0 False 2017-05-12 21:22:14.000000 N/A
* 1940 1636 tasksche.exe 0x82218da0 7 51 0 False 2017-05-12 21:22:14.000000 N/A
** 740 1940 @WanaDecryptor@ 0x81fde308 2 70 0 False 2017-05-12 21:22:22.000000 N/A
----------------------------------------  
Question 11: What is the parent process of PID 740 in Case 002?
Answer: tasksche.exe - OUR BOIIII
Question 12: What is the suspicious parent process PID connected to the decryptor in Case 002?
  Answer: 1940
Question 13: From our current information, what malware is present on the system in Case 002?
  Answer: Wannacry

----------------------------------------

┌──(nihilobstat㉿NihilObstat)-[~/Tryhackme/Volatility/volatility3]
└─$ python3 vol.py -f 'Investigation-2.raw' windows.dlllist --pid 740
Volatility 3 Framework 2.5.0
Progress: 100.00 PDB scanning finished             
PID Process Base Size Name Path LoadTime File output

740 @WanaDecryptor@ 0x400000 0x3d000 @WanaDecryptor@.exe C:\Intel\ivecuqmanpnirkt615\@WanaDecryptor@.exe N/A Disabled
740 @WanaDecryptor@ 0x7c900000 0xb2000 ntdll.dll C:\WINDOWS\system32\ntdll.dll N/A Disabled
740 @WanaDecryptor@ 0x7c800000 0xf6000 kernel32.dll C:\WINDOWS\system32\kernel32.dll N/A Disabled
740 @WanaDecryptor@ 0x73dd0000 0xf2000 MFC42.DLL C:\WINDOWS\system32\MFC42.DLL N/A Disabled
740 @WanaDecryptor@ 0x77c10000 0x58000 msvcrt.dll C:\WINDOWS\system32\msvcrt.dll N/A Disabled
740 @WanaDecryptor@ 0x77f10000 0x49000 GDI32.dll C:\WINDOWS\system32\GDI32.dll N/A Disabled
740 @WanaDecryptor@ 0x7e410000 0x91000 USER32.dll C:\WINDOWS\system32\USER32.dll N/A Disabled
740 @WanaDecryptor@ 0x77dd0000 0x9b000 ADVAPI32.dll C:\WINDOWS\system32\ADVAPI32.dll N/A Disabled
740 @WanaDecryptor@ 0x77e70000 0x93000 RPCRT4.dll C:\WINDOWS\system32\RPCRT4.dll N/A Disabled
740 @WanaDecryptor@ 0x77fe0000 0x11000 Secur32.dll C:\WINDOWS\system32\Secur32.dll N/A Disabled
740 @WanaDecryptor@ 0x7c9c0000 0x818000 SHELL32.dll C:\WINDOWS\system32\SHELL32.dll N/A Disabled
740 @WanaDecryptor@ 0x77f60000 0x76000 SHLWAPI.dll C:\WINDOWS\system32\SHLWAPI.dll N/A Disabled
740 @WanaDecryptor@ 0x773d0000 0x103000 COMCTL32.dll C:\WINDOWS\WinSxS\X86_Microsoft.Windows.Common-Controls_6595b64144ccf1df_6.0.2600.6028_x-ww_61e65202\COMCTL32.dll N/A Disabled
740 @WanaDecryptor@ 0x77120000 0x8b000 OLEAUT32.dll C:\WINDOWS\system32\OLEAUT32.dll N/A Disabled
740 @WanaDecryptor@ 0x774e0000 0x13e000 ole32.dll C:\WINDOWS\system32\ole32.dll N/A Disabled
740 @WanaDecryptor@ 0x78130000 0x134000 urlmon.dll C:\WINDOWS\system32\urlmon.dll N/A Disabled
740 @WanaDecryptor@ 0x3dfd0000 0x1ec000 iertutil.dll C:\WINDOWS\system32\iertutil.dll N/A Disabled
740 @WanaDecryptor@ 0x76080000 0x65000 MSVCP60.dll C:\WINDOWS\system32\MSVCP60.dll N/A Disabled
740 @WanaDecryptor@ 0x71ab0000 0x17000 WS2_32.dll C:\WINDOWS\system32\WS2_32.dll N/A Disabled
740 @WanaDecryptor@ 0x71aa0000 0x8000 WS2HELP.dll C:\WINDOWS\system32\WS2HELP.dll N/A Disabled
740 @WanaDecryptor@ 0x3d930000 0xe7000 WININET.dll C:\WINDOWS\system32\WININET.dll N/A Disabled
740 @WanaDecryptor@ 0x340000 0x9000 Normaliz.dll C:\WINDOWS\system32\Normaliz.dll N/A Disabled
740 @WanaDecryptor@ 0x76390000 0x1d000 IMM32.DLL C:\WINDOWS\system32\IMM32.DLL N/A Disabled
740 @WanaDecryptor@ 0x629c0000 0x9000 LPK.DLL C:\WINDOWS\system32\LPK.DLL N/A Disabled
740 @WanaDecryptor@ 0x74d90000 0x6b000 USP10.dll C:\WINDOWS\system32\USP10.dll N/A Disabled
740 @WanaDecryptor@ 0x732e0000 0x5000 RICHED32.DLL C:\WINDOWS\system32\RICHED32.DLL N/A Disabled
740 @WanaDecryptor@ 0x74e30000 0x6d000 RICHED20.dll C:\WINDOWS\system32\RICHED20.dll N/A Disabled
740 @WanaDecryptor@ 0x5ad70000 0x38000 uxtheme.dll C:\WINDOWS\system32\uxtheme.dll N/A Disabled
740 @WanaDecryptor@ 0x74720000 0x4c000 MSCTF.dll C:\WINDOWS\system32\MSCTF.dll N/A Disabled
740 @WanaDecryptor@ 0x755c0000 0x2e000 msctfime.ime C:\WINDOWS\system32\msctfime.ime N/A Disabled
740 @WanaDecryptor@ 0x769c0000 0xb4000 USERENV.dll C:\WINDOWS\system32\USERENV.dll N/A Disabled
740 @WanaDecryptor@ 0xea0000 0x29000 msls31.dll C:\WINDOWS\system32\msls31.dll N/A Disabled
----------------------------------------
This answer is easy with Chat GPT, but remember to always verify your information:
WannaCry, the notorious ransomware that shook the cybersecurity world in 2017, uses the Microsoft Windows Socket 2.0 (Winsock) API for network communications. Therefore, it would be loading the corresponding DLL, which is typically "ws2_32.dll", to handle its network socket operations.

Remember, WannaCry initiates its infamous propagation routine by scanning the local network and the internet for machines vulnerable to the EternalBlue exploit, and it uses the Windows socket API to carry out this network communication.

Question 14: What DLL is loaded by the decryptor used for socket creation in Case 002?
  Answer: WS2_32.dll

----------------------------------------

┌──(nihilobstat㉿NihilObstat)-[~/Tryhackme/Volatility/volatility3]
└─$ python3 vol.py -f 'Investigation-2.raw' windows.handles | grep 1940
596gresscsrss.exe 0x82218da0B scan0x388finProcess 0x1f0fff    tasksche.exe Pid 1940
596 csrss.exe 0x8222eda0 0x390 Thread 0x1f03ff Tid 1944 Pid 1940
596 csrss.exe 0x81fdd9f8 0x3f0 Thread 0x1f03ff Tid 500 Pid 1940
596 csrss.exe 0x81fdd640 0x400 Thread 0x1f03ff Tid 504 Pid 1940
596 csrss.exe 0x81fe72f8 0x458 Thread 0x1f03ff Tid 472 Pid 1940
596 csrss.exe 0x81fe3870 0x45c Thread 0x1f03ff Tid 468 Pid 1940
596 csrss.exe 0x81fa9b20 0x470 Thread 0x1f03ff Tid 488 Pid 1940
596 csrss.exe 0x81fa5640 0x478 Thread 0x1f03ff Tid 496 Pid 1940
676 lsass.exe 0x82218da0 0x4dc Process 0x478 tasksche.exe Pid 1940
1024 svchost.exe 0x82218da0 0xae8 Process 0x478 tasksche.exe Pid 1940
1024 svchost.exe 0x81f61940 0x1148 IoCompletion 0x1f0003
1940 tasksche.exe 0xe1005468 0x4 KeyedEvent 0xf0003 CritSecOutOfMemoryEvent
1940 tasksche.exe 0xe147f350 0x8 Directory 0x3 KnownDlls
1940 tasksche.exe 0x81fbce00 0xc File 0x100020 \Device\HarddiskVolume1\WINDOWS\WinSxS\x86_Microsoft.Windows.Common-Controls_6595b64144ccf1df_6.0.2600.6028_x-ww_61e65202
1940 tasksche.exe 0x8217cfa0 0x10 WindowStation 0xf037f WinSta0
1940 tasksche.exe 0xe15a9d50 0x14 Directory 0xf000f Windows
1940 tasksche.exe 0xe1b8a450 0x18 Port 0x21f0001
1940 tasksche.exe 0x82251428 0x1c Event 0x21f0003
1940 tasksche.exe 0x82365c80 0x20 Desktop 0xf01ff Default
1940 tasksche.exe 0x8217cfa0 0x24 WindowStation 0xf037f WinSta0
1940 tasksche.exe 0x821aa390 0x28 Semaphore 0x100003
1940 tasksche.exe 0x821aa358 0x2c Semaphore 0x100003
1940 tasksche.exe 0xe1a05938 0x30 Key 0x20f003f MACHINE
1940 tasksche.exe 0x82233f18 0x34 File 0x100020 \Device\HarddiskVolume1\Intel\ivecuqmanpnirkt615
1940 tasksche.exe 0xe1a67d48 0x38 Token 0x8
1940 tasksche.exe 0xe149f908 0x3c Directory 0x2000f BaseNamedObjects
1940 tasksche.exe 0x821883e8 0x40 Mutant 0x120001 ShimCacheMutex
1940 tasksche.exe 0xe16644e0 0x44 Section 0x2 ShimSharedMemory
1940 tasksche.exe 0x822386a8 0x48 File 0x100001 \Device\KsecDD
1940 tasksche.exe 0x823d54d0 0x4c Semaphore 0x1f0003 shell.{A48F1A32-A340-11D1-BC6B-00A0C90312E1}
1940 tasksche.exe 0x823a0cd0 0x50 File 0x100020 \Device\HarddiskVolume1\WINDOWS\WinSxS\x86_Microsoft.Windows.Common-Controls_6595b64144ccf1df_6.0.2600.6028_x-ww_61e65202
1940 tasksche.exe 0x8224f180 0x54 Mutant 0x1f0001 MsWinZonesCacheCounterMutexA
1940 tasksche.exe 0x822e3b08 0x58 Mutant 0x1f0001 MsWinZonesCacheCounterMutexA0
1940 tasksche.exe 0x82234450 0x5c Event 0x1f0003
1940 tasksche.exe 0x821dbdd8 0x60 Semaphore 0x100003
1940 tasksche.exe 0x822398f8 0x64 Semaphore 0x100003
1940 tasksche.exe 0x8221da98 0x68 Semaphore 0x100003
1940 tasksche.exe 0x8221d9f0 0x6c Semaphore 0x100003
1940 tasksche.exe 0x8221da28 0x70 Semaphore 0x100003
1940 tasksche.exe 0x820146d8 0x74 Semaphore 0x100003
1940 tasksche.exe 0x81ff09f0 0x78 Semaphore 0x100003
1940 tasksche.exe 0x81ff0988 0x7c Semaphore 0x100003
1940 tasksche.exe 0x81ff0a58 0x80 Semaphore 0x100003
1940 tasksche.exe 0x81ff0b90 0x84 Semaphore 0x100003
1940 tasksche.exe 0x81ff0b28 0x88 Semaphore 0x100003
1940 tasksche.exe 0x81ff0c60 0x8c Semaphore 0x100003
1940 tasksche.exe 0x8225f5d8 0x90 Event 0x1f0003
1940 tasksche.exe 0x8223b668 0x94 Event 0x1f0003
1940 tasksche.exe 0x8215c330 0x98 Event 0x1f0003
1940 tasksche.exe 0x822555f0 0x9c Event 0x1f0003
1940 tasksche.exe 0x8222eda0 0xa0 Thread 0x1f03ff Tid 1944 Pid 1940
1940 tasksche.exe 0x8219d480 0xa4 IoCompletion 0x1f0003
1940 tasksche.exe 0x81fe7e88 0xa8 IoCompletion 0x1f0003
1940 tasksche.exe 0x8219d480 0xac IoCompletion 0x1f0003
1940 tasksche.exe 0x81fa9b20 0xb4 Thread 0x1f03ff Tid 488 Pid 1940
1940 tasksche.exe 0x81fdd640 0xb8 Thread 0x1f03ff Tid 504 Pid 1940
1940 tasksche.exe 0x821dea50 0xc0 Semaphore 0x1f0003 shell.{210A4BA0-3AEA-1069-A2D9-08002B30309D}
1940 tasksche.exe 0xe1b978d0 0xc4 Key 0x20f003f USER\S-1-5-21-602162358-764733703-1957994488-1003
1940 tasksche.exe 0x8219bde0 0xc8 Event 0x1f0003 userenv: User Profile setup event
1940 tasksche.exe 0xe1530470 0xd0 Port 0x1f0001
1940 tasksche.exe 0xe1a45cd8 0xe4 Port 0x1f0001
1940 tasksche.exe 0xe18c02d0 0xe8 Section 0x4

----------------------------------------

Question 15: What mutex can be found that is a known indicator of the malware in question in Case 002?
  Answer: Global\MsWinZonesCacheCounterMutexA - Mandiant has a report on this one here: https://www.mandiant.com/resources/blog/wannacry-malware-profile. CTRL-F: mutex and you will find the answer. 
Question 16: What plugin could be used to identify all files loaded from the malware working directory in Case 002?
  Answer: windows.filescan
