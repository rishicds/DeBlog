+++
title = "Inside a Real Malware Email: Dissecting a GuLoader Trojan"
date = 2025-07-10T14:15:00+05:30
draft = false
description = "A deep dive into a real-world GuLoader malware incident disguised as a Swift payment request"
image = "/images/malware/cover_image.png"
imageBig = "/images/malware/cover_image.png"
categories = ["cybersecurity", "malware", "infosec"]
authors = ["Nirjhar Barma"]
avatar = "/images/nir.png"
+++

# Inside a Real Malware Email: How I got a GuLoader(aka CloudEyE) Trojan Disguised as a Business Payment

## Introduction

I didn’t expect a cyber attack disguised as a Swift transaction to land in my inbox. But on July 10, 2025, I received a very polished email that claimed to be from a business contact, complete with official-sounding language, phone numbers, and even a company website.

The subject?

> “Fw: Swift Copy:0008/025/05/06_dt.07/09/25 by SANWAR MAL SUREKA Insurance & Investment Advisors”

The message claimed to be from “Sreekumar K S”  of Mor Aqua Fresh, referencing an overdue balance and requesting payment confirmation. It included a password-protected .lzh archive, presented as supporting documentation, and even provided a password for access — a tactic often used in legitimate financial correspondence.

At first glance, everything seemed in order: the email was polite, well-formatted, and included branding and contact numbers. But behind the professional facade lurked something far more sinister.

![Cover Image](images/malware/mail.png)

This was no payment request — it was a delivery mechanism for GuLoader, a sophisticated malware dropper often used to load Remote Access Trojans (RATs), credential stealers, and surveillance tools. What followed was a deep dive into obfuscation, memory injection, and the kind of social engineering that even seasoned professionals must stay vigilant against.

In this post, I’ll walk you through how I analyzed the threat, uncovered its behavior, and understood how it cleverly slipped past even Gmail’s robust security filters.

---

## 🦠 What is GuLoader?

GuLoader (also known as CloudEyE) is a popular malware loader first observed in 2019. It is commonly used to:

- Deliver second-stage malware  
- Evade antivirus detection using in-memory injection  
- Establish persistence while avoiding behavioural sandboxes and VMs  

It typically arrives via phishing emails and employs techniques like:

- NSIS packing  
- Shellcode obfuscation  
- Process hollowing and memory injection  
- Anti-VM/anti-debug checks  

The malware is widely sold on underground forums and used by various threat groups due to its modularity and stealth.

---

## Stage 1: The Email Lure

### 📧 Email Header Analysis

The email came from a legit-looking Gmail address (`as21185@gmaildotcom`) with valid SPF, DKIM and DMARC authentication.

![Mail Info](/images/malware/mail_info.png)

![Original Mail](/images/malware/original_mail.png)

---

### Why it was effective:

- Social Engineering: It used urgency and financial context (payment confirmation)  
- Impersonation: Real-sounding names, companies and contact info  
- Password-Protected Archive: Prevented antivirus scanners from detecting the contents  
- Attachment Format: “.lzh” is uncommon, avoiding most signature-based detections  

---

### 🚩 Red Flags:

- No prior relationship or context for the financial transaction  
- Generic language and urgency to confirm payment  
- Foreign messaging platforms (QQ, wechat)  
- BCC delivery to hide recipient list and enable mass phishing  

---

## Stage 2: Static Information

### 📁 Archive Contents

Once extracted using the password, the “.lzh” file revealed a Windows executable:

**Attached File**

- Filename: `091208478 DTD 10.07.2025 09750913 640976289-85665.exe` (inside .lzh)  
- Password: `310921680`  
- Type: PE32 executable (GUI) - Nullsoft Installer (NSIS)  
- Size: ~1.2 MB  

![Files](/images/malware/file_name.png)

**Metadata:**  
![Metadata](/images/malware/metadata.png)

This is consistent with GuLoader characteristics – fake metadata, NSIS-based shellcode loader and packing to avoid detection.

**TRiD:**  
![TRiD analysis](/images/malware/TRiD.png)

---

## Stage 3: Dynamic Behaviour Analysis 

**Behaviour Summary (from Any.Run)**

- Drops a DLL (`system.dll`) to `%TEMP%`  
- Creates autorun registry keys for persistence  
- Connects to suspicious external IPs on uncommon ports  
- Uses `VirtualAlloc`, `WriteProcessMemory`, `Createthread` for shellcode injection  
- Reads system & browser settings  
- Executes secondary process (`wmlaunch.exe`, `slui.exe`)  

![Behaviour Analysis](/images/malware/behaviour_analysis.png)

---

### Technique Used

- NSIS Packing – To hide the real payload  
- Shellcode Loader – Injects payload directly into memory  
- Registry Persistence – Auto-runs on startup  
- Anti-VM/Anti-Debug – Behaviour may change in sandbox environments  

---

## Stage 4: C2 Infrastructure & Payload Analysis

Although the report showed no plaintext C2 domain, analysis indicates that GuLoader:

- Connects to dynamic C2 domains over HTTPS  
- Downloads and injects encrypted second-stage payloads like AgentTesla, FormBook or Remcos  

Even without clear C2 in strings, the behaviour matches known GuLoader indicators:

- Process hollowing  
- Memory injection  
- Registry persistence  

GuLoader is a loader trojan sold on underground markets. It’s widely used by threat actors to:

- Bypass antivirus  
- Load info-stealers (AgentTesla, FormBook, etc)  
- Maintain stealth (in-memory execution)  

---

### Detection Engines (from AV Scanners):

- Microsoft: `Trojan:Win32/GuLoader.RAZ!MTB`  
- Kaspersky: `HEUR:Trojan.Win32.Makoob.gen`  
- Malwarebytes: `Trojan.GuLoader`  
- Symantec: `Scr.NSISHeur!gen3`  

---

## Stage 5: Indicators of Compromise (IOC)

![IOCs](/images/malware/ioc.png)

### Hashes:

- MD5: `F54C3C32364C91454BEA24A1E6B98713`  
- SHA256: `4668DC8468E677499828C63E313945125134803D6C96FE431BB71C332A64BE49`  

### Files:

- `%TEMP%\System.dll`  
- `HKCU\Software\Locales Approx`  
- `HKCU\washoan\Uninstall\blodfattigste`  
- `HKCU\bowline\elice\lseplaners`  

### Processes:

- `wmlaunch.exe`  
- `slui.exe`  

**Processes**

- Monitored processes – 1  
- Malicious processes – 1  
- Suspicious processes – 1  



**Process 2040 –**  

![2040](/images/malware/2040.png)

**Process 5612 –**  

![5612](/images/malware/5612.png)

**Process 7092 –**  

![7092](/images/malware/7092.png)

---

# Behaviour graph

![Behaviour graph](/images/malware/behaviour_graph.png)

## Conclusion

This real-world attack shows how easily cybercriminals can blend into daily business communication. The email successfully bypassed:

- Gmail spam/phishing filters  
- Signature-based antivirus  
- User skepticism — with professional formatting and emotional appeal  

The payload, GuLoader, delivered stealthy second-stage malware using:

- Memory injection  
- Process hollowing  
- Registry-based persistence  

This incident is a stark reminder that security is not just about technology — it’s about awareness. Even the most well-crafted emails must be scrutinized, especially when they include executable attachments.

---

## Tools I used:

- Any.Run (Interactive sandbox)  
- ExifTool (metadata)  
- Strings/hexdump (static analysis)  
- x64dbg (debugging)  
- PEStudio, DIE, cyberchef  

---

## [LINKS]

- **Any.Run link**: [Any.run/Task](https://app.any.run/tasks/c653a81e-4e54-4e2a-973f-e32e39b9d4fa)  
- **Report link**: [Any.run/report](https://any.run/report/4668dc8468e677499828c63e313945125134803D6C96FE431BB71C332A64BE49/c653a81e-4e54-4e2a-973f-e32e39b9d4fa#i-table-processes-2ca8267e-9675-43b8-b0c9-196debf87044)  
- **Malware link**: [Cybersharing.net](https://cybersharing.net/s/629605c93acb4598) *(Use isolated virtual machine to run)*
