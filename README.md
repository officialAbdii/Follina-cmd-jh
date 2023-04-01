# MS-MSDT "Follina" Attack Vector

--------------

This Python script generates a maldoc, that executes a PowerShell command on the target machine. It also sets up an HTTP server to deliver the malicious payload to the target machine.

The script takes several arguments, including the command to be executed on the target machine, the name and location of the output maldoc file, the network interface or IP address to host the HTTP server, the port to serve the HTTP server, and the port to serve the reverse shell on.

The script first parses the supplied interface to determine what to reach out to. It then copies a Microsoft Word skeleton into a temporary staging folder, prepares a temporary HTTP server location, and modifies the Word skeleton to include the HTTP server. The script then rebuilds the original office file and creates the maldoc.

Next, the script encodes the PowerShell command as base64 and creates a unique MS-MSDT payload that is over 4096 bytes at minimum. It then creates an HTML endpoint and hosts the HTTP server on all interfaces.

If the user has specified a reverse shell, the script downloads and executes a Netcat binary to establish a reverse shell on the target machine. The script creates a new thread to host the HTTP server and starts it. The anti-virus on the target system can flag the Netcat binary being downloaded so be careful.

--------------

The Microsoft MSDT-Follina zero-day vulnerability is a type confusion bug that allows attackers to execute arbitrary code on a victim's computer. The vulnerability was first discovered in early 2021 and is actively being exploited in the wild. Here's how the vulnerability works in detail:

Initial infection: The attackers initially infect a victim's computer using a phishing email or other social engineering techniques. The payload is typically delivered through a malicious document or a link to a website hosting the exploit.

Exploit delivery: The exploit uses the vulnerability in the Microsoft Diagnostic and Recovery Toolset (MSDaRT) to execute arbitrary code on the victim's computer. The vulnerability exists because of the way the MSDaRT toolset processes a specific type of file, called a cabinet file (CAB).
The ms-msdt payload needs to be at least 4096 bytes in size because the payload is being delivered via a technique known as DLL search order hijacking. When a DLL is loaded by a Windows process, Windows will search for the DLL in several locations, including the current directory of the process. By creating a file with the same name as a DLL that the process is trying to load, an attacker can cause the process to load the attacker's file instead. However, if the attacker's file is smaller than the original DLL, the process may try to read beyond the end of the file and cause a crash. To avoid this, the attacker needs to ensure that the size of the malicious file is at least as large as the original DLL. In the case of the ms-msdt payload, the original DLL is typically around 4 KB in size, so the attacker needs to create a file of at least that size to avoid crashes.

Type confusion: The vulnerability is a type confusion bug, which means it occurs when the program mistakenly uses an object of one type as if it were an object of another type. In this case, the vulnerability arises because the MSDaRT toolset does not properly check the type of objects when processing CAB files.

Memory corruption: The exploit triggers a memory corruption vulnerability in the MSDaRT toolset by providing it with a specially crafted CAB file that causes the toolset to allocate memory incorrectly. This creates a situation where an attacker can control the contents of memory and execute arbitrary code.

Payload execution: The exploit then executes the attacker's payload, typically a Remote Access Trojan (RAT), which allows the attacker to take full control of the victim's computer. The RAT can be used to steal sensitive data, install additional malware, or perform other malicious actions.

Detection evasion: The attackers typically use various techniques to evade detection by antivirus software, including packing the payload with custom packers, encrypting the payload, or using fileless techniques.

The MSDT-Follina zero-day vulnerability is a serious threat to users of Windows operating systems. Microsoft has released a patch to fix the vulnerability, and users are strongly advised to update their systems as soon as possible to prevent exploitation. Additionally, users should be cautious when opening email attachments or clicking on links from unknown sources to avoid falling victim to phishing attacks.

--------------

Here is a step-by-step explanation of the code:

The script imports several libraries, including argparse, zipfile, tempfile, shutil, os, netifaces, ipaddress, random, base64, http.server, socketserver, string, and threading.
The script defines several command-line arguments using the argparse library. These arguments include the command to run on the victim's machine, the output maldoc file name, the network interface or IP address to host the HTTP server, the port to serve the HTTP server, and the port to serve the reverse shell on.
The script defines a function called "main" that takes in the command-line arguments as an argument.
The script attempts to parse the supplied interface by creating an instance of the IPv4Address class from the ipaddress library. If that fails, it attempts to use the netifaces library to get the IP address of the supplied interface. If both attempts fail, it prints an error message and exits.
The script creates a temporary folder to stage the Microsoft Word document skeleton and then copies the skeleton to that folder.
The script prepares a temporary location for the HTTP server.
The script modifies the Word skeleton to include the HTTP server.
The script rebuilds the original Office file.
The script encodes the command to run on the victim's machine using base64 encoding.
The script creates a unique MS-MSDT payload that is over 4096 bytes at minimum.
The script creates an HTML endpoint and writes the HTML payload to the endpoint.
The script defines a custom server class called ReuseTCPServer that reuses the TCP socket and binds to it.
The script defines a custom handler class called Handler that logs messages and requests only when the reverse shell is not enabled.
The script defines a function called serve_http that starts the HTTP server and serves requests forever.
The script starts the HTTP server on all interfaces and prints a message to indicate that it is serving the HTML payload.
The script checks if the reverse shell is enabled. If it is, it sets the command to run to include the Invoke-WebRequest command to download and execute a Netcat executable from GitHub.
The script encodes the command again, this time using base64 encoding.
The script creates another HTML payload that includes the new command and replaces the old payload.
The script writes the new HTML payload to the index.html file.
The script starts a new thread to serve the HTTP server.
The script waits for the thread to finish.
The program exits.

--------------

Create a "Follina" MS-MSDT attack with a malicious Microsoft Word document and stage a payload with an HTTP server.

![Screenshot](https://user-images.githubusercontent.com/6288722/171033876-dbe73e3e-0a3a-436a-91d8-7fa77a5c1ace.png)

# Usage

```
usage: follina.py [-h] [--command COMMAND] [--output OUTPUT] [--interface INTERFACE] [--port PORT]

options:
  -h, --help            show this help message and exit
  --command COMMAND, -c COMMAND
                        command to run on the target (default: calc)
  --output OUTPUT, -o OUTPUT
                        output maldoc file (default: ./follina.doc)
  --interface INTERFACE, -i INTERFACE
                        network interface or IP address to host the HTTP server (default: eth0)
  --port PORT, -p PORT  port to serve the HTTP server (default: 8000)
```

# Examples

Pop `calc.exe`:

```
$ python3 follina.py   
[+] copied staging doc /tmp/9mcvbrwo
[+] created maldoc ./follina.doc
[+] serving html payload on :8000
```

Pop `notepad.exe`:

```
$ python3 follina.py -c "notepad"
```

Get a reverse shell on port 9001. **Note, this downloads a netcat binary _onto the victim_ and places it in `C:\Windows\Tasks`. It does not clean up the binary. This will trigger antivirus detections unless AV is disabled.**

```
$ python3 follina.py -r 9001
```

![Reverse Shell](https://user-images.githubusercontent.com/6288722/171037880-03a73d6a-4606-4c42-abcb-ee52a9e669c6.png)
