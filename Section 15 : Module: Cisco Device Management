Neil Anderson ke CCNA Course (Module: Cisco Device Management - Topics 87 to 94) ka complete, in-depth guide niche diya gaya hai.

---

# 🛠️ Module: Cisco Device Management

---

## 📍 87. Introduction

Cisco Device Management me routers aur switches ki internal hardware memory, boot cycle, backup, image restoration, aur password recovery jaise operational tasks cover hote hain.

### 🧠 Cisco Router Memory Types (Four Main Memories):

1. **ROM (Read-Only Memory):** * Non-volatile memory (power off hone par erase nahi hota).
* Isme **POST (Power-On Self-Test)** code, **Bootstrap Program**, aur **ROMMON (ROM Monitor)** mini-operating system store rehta hai.


2. **Flash Memory:** * Non-volatile EEPROM memory.
* Isme actual **Cisco IOS Operating System Image (`.bin` ya `.pkg` file)** store rehti hai.


3. **NVRAM (Non-Volatile RAM):** * Non-volatile memory.
* Isme device ka **`startup-config`** (saved configuration) aur **Configuration Register** value store hoti hai.


4. **RAM (Random Access Memory):** * Volatile memory (power off hone par erase ho jata hai).
* Isme **`running-config`**, Routing Table, ARP Table, MAC Table, aur active OS process run hote hain.



---

## 📍 88 & 89. The Cisco IOS Boot-Up Process (Step-by-Step)

Jab aap Cisco Router/Switch ka Power Switch **ON** karte ho, toh sequence aisa hota hai:

```text
 ┌──────────────┐      ┌─────────────────┐      ┌─────────────────┐      ┌─────────────────────┐
 │ 1. POST      │ ───► │ 2. Bootstrap    │ ───► │ 3. Locate & Load│ ───► │ 4. Locate & Load    │
 │ (ROM Hardware│      │ (ROM program    │      │    Cisco IOS    │      │    Configuration    │
 │  Diagnostics)│      │  initialization)│      │    (from Flash) │      │    (from NVRAM)     │
 └──────────────┘      └─────────────────┘      └─────────────────┘      └─────────────────────┘

```

### 🎬 Step-by-Step Breakdown:

1. **POST (Power-On Self Test):** * ROM se run hota hai. CPU, RAM, interfaces, aur hardware components ko test karta hai.
2. **Bootstrap Program Execution:** * ROM se Bootstrap load hota hai. Yeh Configuration Register ko check karta hai (Default value: `0x2102`).
3. **Locate & Load Cisco IOS:** * Bootstrap program Flash memory me `.bin` OS file dhoondhta hai aur use RAM me de-compress karke load karta hai.
* *Fallback:* Agar Flash me IOS na mile, toh router TFTP Server dhoondhta hai, aur aakhir me **ROMMON Mode** (`rommon 1>`) par chala jata hai.


4. **Locate & Load Configuration:** * IOS load hone ke baad, Router NVRAM se **`startup-config`** file ko RAM me **`running-config`** ke roop me load karta hai.
* *Fallback:* Agar NVRAM me startup-config na mile, toh device **System Configuration Dialog (Setup Mode)** launch kar deta hai:
```text
Would you like to enter the initial configuration dialog? [yes/no]:

```





---

## 📍 90 & 91. Factory Reset and Password Recovery

Agar aap router ka enable password bhool gaye ho ya use factory reset karna chahte ho, toh **Configuration Register** ka khel aata hai.

### 🔢 Configuration Register Values:

* **`0x2102` (Default Normal Boot):** Router normal boot karega (Flash se IOS load karega aur NVRAM se `startup-config` load karega).
* **`0x2142` (Bypass NVRAM Boot):** Router Flash se IOS load karega, lekin **NVRAM `startup-config` ko IGNORE kar dega**.

---

### 🔑 Step-by-Step Password Recovery Process:

#### Step 1: Console Connect Karo & Break Send Karo

* Router ko Console Cable se PC par connect karo (PuTTY/TeraTerm).
* Router ko Reboot/Power Cycle karo aur start-up ke waqt **`Break Key`** (`Ctrl + Break` ya `Esc`) dabao taaki Router **ROMMON Mode** (`rommon 1>`) par pause ho jaye.

#### Step 2: Config-Register Change Karo (NVRAM Bypass)

```text
rommon 1> confreg 0x2142
rommon 2> reset

```

*(Router reboot hoga, NVRAM config ignore karega aur direct Setup mode me aayega. Enter `no` to setup prompt).*

#### Step 3: Copy Startup Config to Running Config

```text
Router> enable
Router# copy startup-config running-config

```

> ⚠️ **IMPORTANT INTERVIEW POINT:** Hamesha `startup-config` ko `running-config` me copy karo! Agar galati se `copy running-config startup-config` chala diya, toh purana poora configuration ERASE ho jayega!

#### Step 4: Password Change Karo

```text
Router(config)# enable secret NewPassword123
Router(config)# interface GigabitEthernet 0/0
Router(config-if)# no shutdown

```

#### Step 5: Config Register Wapas Normal Karo & Save

```text
Router(config)# config-register 0x2102
Router(config)# end
Router# copy running-config startup-config

```

---

## 📍 92. Backing Up System Image and Configuration

Production network me disaster recovery ke liye `running-config` aur IOS Image ka external **TFTP Server** (jaise SolarWinds TFTP / Tftpd64) par backup rakhna zaroori hota hai.

### 📤 Backing Up Configuration to TFTP:

```text
Router# copy running-config tftp:
Address or name of remote host []? 192.168.1.100
Destination filename [router-confg]? R1-Backup-Config
!!
[OK - 2048 bytes]

```

### 📥 Restoring Configuration from TFTP:

```text
Router# copy tftp: running-config
Address or name of remote host []? 192.168.1.100
Source filename []? R1-Backup-Config
Destination filename [running-config]? 

```

---

## 📍 93. Upgrading Cisco IOS (Flash Image Update)

Jab Cisco security patches ya naya feature release karti hai, tab Router ka IOS Upgrade karna padta hai.

### 🔄 Upgrade Steps:

#### 1. Current Flash Check Karo:

```text
Router# show flash:

```

*(Check karo ki naye IOS file size ke liye Flash memory me पर्याप्त space hai ya nahi).*

#### 2. TFTP Server se New IOS Image Download Karo:

```text
Router# copy tftp: flash:
Address or name of remote host []? 192.168.1.100
Source filename []? c1900-universalk9-mz.SPA.155-3.M.bin
Destination filename [c1900-universalk9-mz.SPA.155-3.M.bin]? 
CCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCC
[OK - 33554432 bytes]

```

#### 3. MD5 Hash Integrity Verify Karo (Crucial Step!):

```text
Router# verify /md5 flash:c1900-universalk9-mz.SPA.155-3.M.bin

```

*(Cisco portal par diye MD5 hash se match karo taaki corrupt file load na ho).*

#### 4. Boot System Command Update & Reload:

```text
Router(config)# boot system flash c1900-universalk9-mz.SPA.155-3.M.bin
Router(config)# exit
Router# copy running-config startup-config
Router# reload

```

#### 5. Verify Active Version:

```text
Router# show version

```

---

## 📍 94. Cisco Device Management - Lab Verification Summary

### 🛠️ Key CLI Verification Commands:

| Command | Usage / Purpose |
| --- | --- |
| `show version` | IOS Version, Uptime, Hardware Model, Serial Number, aur Config-Register Value (`0x2102`) dekhne ke liye. |
| `show flash:` | Flash memory capacity aur available `.bin` images dekhne ke liye. |
| `show running-config` | RAM me active current configuration dekhne ke liye. |
| `show startup-config` | NVRAM me saved configuration dekhne ke liye. |
| `write memory` / `copy run start` | `running-config` ko `startup-config` me save karne ke liye. |
| `erase startup-config` | Router ko Factory Reset karne ke liye (Reset ke baad `reload` zaroori hai). |

---

## 🎙️ Neil Anderson Style Interview Focus Questions

**Q1: Cisco Router ka Default Configuration Register value kya hota hai aur Password Recovery me use kya set karte hain?**

> **Answer:** Default value **`0x2102`** hoti hai (jo normal boot process follow karti hai). Password Recovery ke waqt ise **`0x2142`** set karte hain taaki router boot hone par NVRAM `startup-config` ko ignore kar de.

**Q2: Password Recovery ke waqt `copy startup-config running-config` command kyun chalayi jaati hai?**

> **Answer:** Jab router `0x2142` se boot hota hai, toh RAM khali hoti hai. Agar hum direct new password set karke save kar denge, toh purana sara interface/IP/routing config delete ho jayega. Isiliye pehle `startup-config` ko RAM (`running-config`) me late hain, password change karte hain, aur fir save karte hain.

---

Bhai, Device Management, Password Recovery aur Boot Process ekdum clear ho gaya? Next topic kaunsa revise karein? 🚀
