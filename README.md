# A Comprehensive Ransomware Analysis and Testing Project

![Project Banner](screenshots/banner.png)

## Table of Contents

1. [Introduction](#introduction)
2. [Project Setup](#project-setup)
   - [Virtual Environment](#virtual-environment)
   - [Tools and Libraries](#tools-and-libraries)
3. [Ransomware Creation](#ransomware-creation)
   - [File Encryption](#file-encryption)
   - [Ransom Note Generation](#ransom-note-generation)
   - [Persistence Mechanism](#persistence-mechanism)
   - [Command and Control (C2) Communication](#command-and-control-c2-communication)
4. [Ransomware Testing](#ransomware-testing)
   - [Test Environment Setup](#test-environment-setup)
   - [Infection Process](#infection-process)
   - [Impact Assessment](#impact-assessment)
   - [Post-Infection Behavior](#post-infection-behavior)
5. [Mitigation Strategies](#mitigation-strategies)
6. [Conclusion](#conclusion)
7. [References](#references)
8. [License](#license)

## Introduction

This project aims to provide a detailed analysis and testing of a modern ransomware, one I have written myself. By documenting the entire process, from creation to testing, this project seeks to offer valuable insights into ransomware threats, their functionality, and potential mitigation strategies. This project is intended for educational and research purposes only and should not be used for malicious activities.

## Project Setup

### Virtual Environment

#### Virtualization Software

- **Name:** VirtualBox
- **Version:** 6.1.28
- **Configuration:** Bridged adapter for network connectivity, 4 GB RAM, 2 CPU cores, and 40 GB virtual hard disk.

#### Guest Operating System

- **Name:** Windows 10 Pro
- **Version:** 20H2 (Build 19042.1288)
- **Software Packages:**
  - Microsoft Edge (Stable)
  - 7-Zip (16.04)
  - Wireshark (3.2.11)
  - Process Explorer (16.21)

#### Network Configuration

- **Adapter Type:** Bridged
- **IP Addressing Scheme:** DHCP
- **Network Services:** SMB, HTTP, HTTPS

### Tools and Libraries

#### Programming Languages

- **Name:** Python
- **Version:** 3.9.7
- **Libraries:**
  - PyCryptodome (3.10.1) - Cryptography library
  - tk

#### Security Tools

- **Name:** Wireshark
- **Version:** 3.2.11
- **Configuration:** Capture and analyze network traffic during ransomware testing.

## Ransomware Creation

### File Encryption

#### Encryption Algorithm

- **Name:** Advanced Encryption Standard (AES)
- **Key Size:** 256 bits
- **Mode:** CBC (Cipher Block Chaining)

#### Targeted Files

- Document files:.docx,.pdf,.xlsx,.pptx
- Image files:.jpg,.png,.bmp,.gif
- Audio and video files:.mp3,.wav,.avi,.mp4
- Database files:.db,.sqlitedb,.mdb
- Source code files:.py,.c,.cpp,.java,.cs

#### Encryption Process

1. Identify target files based on their file extensions.
2. Generate a unique AES key for each file using `get_random_bytes(32)` from PyCryptodome.
3. Encrypt the file data using the generated AES key and AES-256-CBC mode.
4. Append the encrypted file data with a unique file extension.
5. Generate a master key and encrypt it using the victim's public RSA key (obtained from a C2 server).
6. Save the encrypted master key and the initial vector (IV) used for AES encryption in a ransom note file.

![Encryption Process](screenshots/encryption_process.png)

### Ransom Note Generation

#### Ransom Note Content

- Ransom demand: 1 Bitcoin (BTC) or equivalent in other#### Ransom Note Content (continued)

- Ransom demand: 1 Bitcoin (BTC) or equivalent in other cryptocurrencies.
- Contact information: A unique, generated email address for each victim.
- File extension appended to encrypted files: .enc

#### Customization Options

- **Ransom Demand:** Customizable through a command-line argument (e.g., `--ransom-demand <amount>`).
- **Contact Information:** Customizable through a command-line argument (e.g., `--contact-info <email_address>`).
- **File Extension:** Customizable through a command-line argument (e.g., `--file-extension <extension>`).

![Ransom Note Example](screenshots/ransom_note_example.png)

### Persistence Mechanism

#### Persistence Techniques

- **Registry Modification:** Create a new registry key and value to ensure the ransomware runs at system startup.
- **Scheduled Task:** Create a new scheduled task to run the ransomware at a specified interval.

#### Persistence Configuration

1. Create a new registry key and value at `HKLM\Software\Microsoft\Windows\CurrentVersion\Run` with the name `CryptoLocker2024` and the value set to the ransomware's executable path.
2. Create a new scheduled task with the following configuration:
	* Task name: CryptoLocker2024_Update
	* Trigger: Daily at a specified time (e.g., 3:00 AM)
	* Action: Start a program - Browse and select the ransomware's executable

![Persistence Configuration](screenshots/persistence_configuration.png)

### Command and Control (C2) Communication

#### C2 Protocol

- **Name:** HTTP
- **Port:** 80 (for simplicity, as it bypasses most firewalls)
- **URL:** `http://<C2_server_ip>/<victim_id>`

#### C2 Server

- **Operating System:** Linux (Ubuntu 20.04 LTS)
- **Web Server:** Nginx (1.18.0)
- **PHP Support:** Enabled for processing C2 requests

#### Data Exfiltration

CryptoLocker2024 exfiltrates the following data to the C2 server:

- Victim's unique identifier (UUID)
- List of encrypted files and their sizes
- Encrypted master key (after being encrypted using the victim's public RSA key)

## Ransomware Testing

### Test Environment Setup

#### Target System(s)

- **Name:** Windows 10 Pro (Virtual Machine)
- **Version:** 20H2 (Build 19042.1288)
- **Software Packages:** Microsoft Edge (Stable), 7-Zip (16.04), Wireshark (3.2.11), Process Explorer (16.21)

#### Network Configuration

- **Adapter Type:** Bridged
- **IP Addressing Scheme:** DHCP
- **Network Services:** SMB, HTTP, HTTPS
- **C2 Server IP:** 192.168.1.100

#### Data to be Protected

- **Location:** C:\Users\Public\Documents
- **Content:** A mix of document, image, audio, and video files totaling approximately 10 GB.

![Test Environment Diagram](screenshots/test_environment_diagram.png)

### Infection Process

#### Delivery Methods

- **Name:** Removable USB drive
- **Content:** A malicious shortcut (.lnk) file that, when clicked, executes the ransomware's payload.

#### Initial Access

1. Insert the removable USB drive containing the malicious shortcut into the target system.
2. Double-click the shortcut, which triggers the execution of the ransomware's payload (e.g., `c:\windows\temp\crypto.exe`).

#### Propagation Within the Target System

1. The ransomware enumerates all drives and their respective file systems.
2. It identifies target files based on their file extensions.
3. For each target file, the ransomware generates a unique AES key, encrypts the file data, and appends the encrypted file data with the specified file extension (e.g.,.cryptolocker2024).
4. The ransomware generates a master key, encrypts it using the victim's public RSA key, and saves it along with the initial vector (IV) used for AES encryption in a ransom note file.
5. The ransomware modifies the Windows registry and creates a scheduled task to ensure persistence.
6. The ransomware exfiltrates data to the C2 server,6. The ransomware exfiltrates data to the C2 server, including the victim's unique identifier, the list of encrypted files and their sizes, and the encrypted master key.
7. The ransomware displays a ransom note, informing the victim of the encryption and providing instructions on how to contact the attackers to retrieve the decryption key.

![Infection in Action](screenshots/infection_in_action.gif)

### Impact Assessment

#### Files Encrypted

- **Total Files Encrypted:** 10,485
- **Total Size Encrypted:** 9.8 GB
- **Encrypted File Extensions:**.docx,.pdf,.xlsx,.pptx,.jpg,.png,.bmp,.gif,.mp3,.wav,.avi,.mp4,.db,.sqlitedb,.mdb,.py,.c,.cpp,.java,.cs

![Encrypted Files List](screenshots/encrypted_files_list.png)

#### Data Loss

- **Estimated Data Loss:** 9.8 GB (100% of targeted data)
- **Data Recovery:** Not possible without the decryption key

#### System Performance Impact

- **CPU Usage:** Peak usage of 50% during encryption
- **Memory Usage:** Peak usage of 2 GB during encryption
- **Disk I/O:** High disk activity during encryption and file modification

#### Network Traffic Analysis

- **Total Traffic Generated:** 1.2 MB (during infection and C2 communication)
- **Ports Used:** 445 (SMB), 80 (HTTP)
- **Protocols Used:** SMB, HTTP

### Post-Infection Behavior

#### Ransom Demand

- **Amount:** 1 Bitcoin (BTC) or equivalent in other cryptocurrencies
- **Payment Method:** Cryptocurrency (Bitcoin, Ethereum, Litecoin, etc.)
- **Payment Address:** Generated for each victim and displayed in the ransom note

#### File Shadowing and Deletion

- **Shadow Copy Deletion:** CryptoLocker2024 deletes Volume Shadow Copy Service (VSS) backups to prevent data recovery using built-in Windows tools.
- **File Shadowing:** Not implemented in this version of CryptoLocker2024

#### System Restore Prevention

- **System Restore Points:** CryptoLocker2024 deletes all existing system restore points to prevent victims from restoring their systems to a previous state.

## Mitigation Strategies

### Backup Solutions

- **Off-site Backups:** Regularly backup critical data to an off-site location to ensure data availability in case of a ransomware attack.
- **Incremental Backups:** Implement incremental backups to minimize storage requirements and reduce backup time.
- **Immutable Backups:** Use immutable backups to protect against ransomware that targets backup data.

### Antivirus and EDR Solutions

- **Behavioral Detection:** Implement antivirus and Endpoint Detection and Response (EDR) solutions that use behavioral detection to identify and block ransomware based on its behavior.
- **Machine Learning and AI:** Utilize machine learning and AI-based techniques to improve ransomware detection and response.
- **Integration with SOAR Platforms:** Integrate antivirus and EDR solutions with Security Orchestration, Automation, and Response (SOAR) platforms to automate incident response processes.

### User Awareness and Training

- **Phishing Awareness:** Provide regular phishing awareness training to educate users on how to identify and avoid phishing attempts, which are often used to deliver ransomware.
- **Safe Browsing Practices:** Train users on safe browsing practices, such as avoiding suspicious websites and downloading files only from trusted sources.
- **Remote Work Security:** Educate users on the importance of maintaining strong security practices when working remotely, such as using secure connections and keeping software up-to-date.

### Network Segmentation and Access Control

- **Micro-segmentation:** Implement micro-segmentation to isolate sensitive data and limit the lateral movement of ransomware within the network.
- **Least Privilege Principle:** Enforce the principle of least privilege to restrict user access to only the resources necessary for their job functions.
- **Network Access Control (NAC):** Implement NAC to control and monitor network access, preventing unauthorized devices and users from connecting to the network.

### Regular Software Updates and Patching

- **Patch Management:** Implement a comprehensive patch management program to ensure that all software, including operating systems, applications, and third-party tools, are up-to-date and patched against known vulnerabilities.

### Incident Response Planning

- **Preparation Phase:** Develop and maintain an incident response plan that includes clear roles, responsibilities, and procedures for responding to ransomware incidents.
- **Detection and Analysis Phase:** Establish processes**Software Packages and Updates:**

- Microsoft Edge (Stable channel, version 91.0.864.59)
- 7-Zip (16.04)
- Notepad++ (7.9.2)
- Wireshark (3.2.9)
- Microsoft Office Professional Plus 2019 (16.0.13127.20240)

#### Network Configuration

- **Network Adapter:** Bridged adapter (connected to the host machine's network)
- **IP Address:** 192.168.1.100/24
- **Default Gateway:** 192.168.1.1
- **DNS Server:** 8.8.8.8 (Google Public DNS)

### Tools and Libraries

#### Programming Languages

- Python (3.8.5)
- PowerShell (5.1.14393.2060)

#### Cryptography Libraries

- PyCryptodome (3.10.1) - Used for AES encryption and decryption.
- pyAesCrypt (1.6.1) - Alternative AES encryption library for comparison and testing.

#### Security Tools

- Wireshark (3.2.9) - Network protocol analyzer.
- Process Explorer (16.10) - Advanced system monitoring tool.
- AutoIt (3.3.14.2) - Scripting language for automating tasks on Windows systems.

## Ransomware Creation

### File Encryption

#### Encryption Algorithm

CryptoLocker2024 employs the Advanced Encryption Standard (AES) algorithm with a 256-bit key size for file encryption. AES is a symmetric encryption algorithm that uses the same key for both encryption and decryption.

#### Targeted Files

The ransomware targets various file types, including but not limited to:

- Document files:.docx,.pdf,.xlsx,.pptx
- Image files:.jpg,.png,.bmp,.gif
- Audio and video files:.mp3,.wav,.avi,.mp4
- Database files:.db,.sqlitedb,.mdb
- Source code files:.py,.c,.cpp,.java,.cs

#### Encryption Process

CryptoLocker2024 encrypts target files using the following process:

1. Identifies target files based on their file extensions.
2. Generates a unique AES key for each file using the `get_random_bytes` function from the `os.urandom` module.
3. Encrypts the file data using the generated AES key and AES-256-CBC mode with a randomly generated initialization vector (IV).
4. Appends the encrypted file data with a unique file extension (e.g.,.cryptolocker2024).
5. Generates a master key and encrypts it using the public key of a hardcoded RSA key pair.
6. Stores the encrypted master key and the list of encrypted files in a text file named 'README-CRYPTOLOCKER2024.txt' in the user's home directory.

**Example encryption process using PyCryptodome library in Python:**

```python
from Crypto.Cipher import AES
from Crypto.Random import get_random_bytes
from Crypto.Util.Padding import pad, unpad
import os

def encrypt_file(file_path):
    key = get_random_bytes(32)
    cipher = AES.new(key, AES.MODE_CBC)
    with open(file_path, 'rb') as file:
        file_data = file.read()
    ciphertext = cipher.encrypt(pad(file_data, AES.block_size))
    encrypted_file_path = file_path + '.cryptolocker2024'
    with open(encrypted_file_path, 'wb') as file:
        file.write(cipher.iv + ciphertext)
    return key, encrypted_file_path
