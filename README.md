# CSE241 – Lab 01: Lab Setup (1 of 2) – Virtualization
**Student:** Omar Morsi | **Username:** omorsi | **Course:** CSE241 – Spring 2026 | **Instructor:** Dr. Islam Moursy

---

## Table of Contents
1. [Objective](#objective)
2. [Task 1.1 – Install VMware Workstation](#task-11--install-vmware-workstation-for-windows)
3. [Task 1.2 – Create a Virtual Machine](#task-12--create-a-virtual-machine)
4. [Task 1.3 – Install VMware Tools (Guest Tools)](#task-13--install-vmware-tools-guest-tools)
5. [Screenshot-1 – VM Configuration](#screenshot-1--vm-configuration)
6. [Question 1 – Type-1 vs Type-2 Hypervisors](#question-1--type-1-vs-type-2-hypervisors)
7. [Question 2 – Kali Distribution Version](#question-2--kali-distribution-version)
8. [Question 3 – Guest VM Network Information](#question-3--guest-vm-network-information)
9. [Question 4 – Host PC Network Information](#question-4--host-pc-network-information)
10. [Question 5 – NAT vs Bridged Mode](#question-5--nat-vs-bridged-mode)
11. [Problems & Issues Encountered](#problems--issues-encountered)
12. [References](#references)

---

## Objective
The objective of this lab is to gain hands-on experience with virtualization by:
- Installing **VMware Workstation 17.5** on a Windows host.
- Creating a **Kali Linux** virtual machine with specified hardware and partition settings.
- Installing **VMware Guest Tools** (open-vm-tools-desktop) to enable seamless integration between host and guest.
- Configuring **shared folders** between host and guest.
- Documenting the entire setup with screenshots and answering five conceptual/practical questions.

---

## Task 1.1 – Install VMware Workstation for Windows

### Step-by-Step Procedure

1. **Locate the installer**  
   Open the file manager and navigate to the "Supplementary Materials" directory provided by the instructor. The installer file is named:  
   `VMware-workstation-full-17.5.2-xxxxxx-Win.exe`

2. **Run the installer as Administrator**  
   - Right-click the `.exe` file → **Run as administrator**.  
   - If a User Account Control (UAC) prompt appears, click **Yes**.

3. **Follow the Setup Wizard**  
   - **Welcome screen** → click **Next**.  
   - **End-User License Agreement** → select *I accept the terms* → click **Next**.  
   - **Custom Setup** → leave all defaults (installs to `C:\Program Files (x86)\VMware\`) → click **Next**.  
   - **User Experience Settings** → uncheck *Help improve VMware Workstation Pro* if desired → click **Next**.  
   - **Shortcuts** → leave defaults → click **Next**.  
   - **Ready to Install** → click **Install**.

4. **Allow Device Driver Installation**  
   - During installation, a Windows security dialog may appear asking permission to install VMware network and USB device drivers.  
   - Click **Install** (or **Install this driver software anyway**) for each prompt.

5. **Complete the Installation**  
   - When the installation finishes, click **Finish**.  
   - A system **reboot** may be required; if prompted, reboot the PC.

6. **Launch VMware Workstation**  
   - Double-click the **VMware Workstation Pro** icon on the Desktop, or navigate to  
     *Start → VMware → VMware Workstation Pro*.  
   - On first launch, select **Use VMware Workstation 17 for Personal Use** (free tier) and click **Continue** → **Finish**.

> **Justification:** Running the installer with default settings ensures all required components (hypervisor kernel modules, virtual network adapters, USB arbitration service) are installed correctly. Accepting device drivers is mandatory because VMware creates virtual network interfaces (VMnet0 – VMnet8) on the host that the hypervisor needs for VM networking.

---

## Task 1.2 – Create a Virtual Machine

### Step-by-Step Procedure

#### 1. Launch the New VM Wizard
- In VMware Workstation, click **File → New Virtual Machine** (or press `Ctrl+N`).
- Select **Custom (advanced)** → click **Next**.

#### 2. Hardware Compatibility
- Leave at **Workstation 17.x** → click **Next**.

#### 3. Attach the Kali ISO
- Select **Installer disc image file (iso)** → click **Browse** and navigate to the Kali Linux ISO:  
  `kali-linux-2024.4-installer-amd64.iso`  
- Click **Next**.

#### 4. Guest OS Type
- VMware may auto-detect; if not, select:  
  - **Guest OS:** Linux  
  - **Version:** Debian 11.x 64-bit (closest match for Kali 2024.4)  
- Click **Next**.

#### 5. VM Name and Location
- **Virtual machine name:** `Student-<AIU-ID>` *(replace `<AIU-ID>` with your actual ID)*  
- **Location:** keep default or choose a drive with sufficient space  
- Click **Next**.

#### 6. Processor Configuration
- **Number of processors:** 1  
- **Number of cores per processor:** 2  
  *(Result: 2 vCPUs total)*  
- Click **Next**.

#### 7. Memory
- Set memory to **8192 MB (8 GB)**  
- Click **Next**.

#### 8. Network Type
- Select **Use network address translation (NAT)** *(default)*  
- Click **Next**.

#### 9. I/O Controller & Disk Type
- I/O Controller: **LSI Logic** (default) → **Next**  
- Disk Type: **SCSI** (default) → **Next**

#### 10. Create a New Virtual Disk
- Select **Create a new virtual disk** → **Next**  
- **Maximum disk size:** `60 GB`  
- Select **Store virtual disk as a single file**  
- Click **Next** → **Finish** the wizard.

#### 11. Install Kali Linux
- Power on the VM; the Kali installer will boot from the ISO.
- Choose **Graphical Install** from the boot menu.
- **Language / Region / Keyboard:** select your preferences.
- **Hostname:** `Student-<AIU-ID>` *(e.g., `Student-20220001`)*
- **Domain name:** leave blank or use `aiu.edu.eg`
- **User full name:** Omar Morsi  
- **Username:** `omorsi` *(first-name initial + last name)*
- **Password:** set a strong password and confirm.

#### 12. Partition the Disk (Manual)
- Choose **Manual** partitioning.
- Select the 60 GB virtual disk → create a new empty partition table.

| Mount Point | Size   | Type | Filesystem |
|-------------|--------|------|------------|
| `/`         | 48 GB  | Primary | ext4   |
| `swap`      | 12 GB  | Logical | swap  |

  - Create the `/` partition first: select *Free space* → **Create new partition** → `48 GB` → *Primary* → *Beginning* → set **Mount point** to `/` and **Filesystem** to `Ext4` → **Done**.
  - Create the `swap` partition: select remaining *Free space* → **Create new partition** → `12 GB` → *Logical* → set **Use as** to `swap area` → **Done**.
  - Select **Finish partitioning and write changes to disk** → confirm **Yes**.

#### 13. Complete Installation
- Select a **Kali desktop environment** (e.g., Xfce) and default utilities → **Continue**.
- Install **GRUB bootloader** to the virtual disk → **Yes** → select `/dev/sda` → **Continue**.
- Installation completes; click **Continue** to reboot.

#### 14. Update & Upgrade the OS
After the VM reboots and you log in, open a terminal and run:

```bash
sudo apt update && sudo apt upgrade -y
sudo reboot
```

> **Justification:** `apt update` refreshes the package index from Kali repositories. `apt upgrade -y` downloads and installs all available updates, ensuring the OS has the latest security patches and bug fixes. Rebooting afterwards applies any kernel updates.

---

## Task 1.3 – Install VMware Tools (Guest Tools)

### Step-by-Step Procedure

#### 1. Install open-vm-tools-desktop
Boot the VM, log in, and open a terminal. Switch to root:

```bash
sudo -i
```

Run the installation commands:

```bash
sudo apt update
sudo apt install -y --reinstall open-vm-tools-desktop
sudo reboot -f
```

> **Justification:** `open-vm-tools-desktop` is the open-source implementation of VMware Guest Tools. The `--reinstall` flag ensures a clean installation even if a partial version is already present. It provides:
> - Seamless mouse pointer switching between host and guest
> - Clipboard sharing (copy-paste)
> - Display auto-resize
> - File drag-and-drop support

#### 2. Add Support for Shared Folders (OVT)
After reboot, open a terminal and run:

```bash
kali-tweaks
```

- In the **Kali Tweaks** menu, navigate to **Virtualization**.
- Select **Install additional packages and scripts for VMware**.
- Follow the on-screen instructions and confirm any prompts.
- Exit Kali Tweaks when done.

> **Justification:** The default `open-vm-tools` package does not include the VMware shared-folder FUSE driver. `kali-tweaks` installs the `open-vm-tools-fuse` package and any required helper scripts that allow the guest to mount host-side shared folders.

#### 3. Configure Shared Folder in VMware Workstation
1. **Shut down** the VM.
2. In VMware Workstation, select the VM → click **Edit virtual machine settings**.
3. Go to **Options** tab → **Shared Folders**.
4. Set **Folder sharing** to **Always enabled**.
5. Click **Add** → browse to a folder on the **host** (e.g., `C:\Users\Omar\Shared`) → give it a name (e.g., `host-share`) → click **Finish** → **OK**.
6. Power the VM back on.

#### 4. Mount the Shared Folder in the Guest
In the guest terminal:

```bash
sudo mount-shared-folders
```

The shared folder will be mounted at `/mnt/hgfs/host-share/`. Verify:

```bash
ls /mnt/hgfs/
```

#### 5. Test File Sharing
- Create a file on the **host** inside the shared folder and verify it appears inside the VM at `/mnt/hgfs/host-share/`.
- Create a file inside `/mnt/hgfs/host-share/` from the **guest** and verify it appears on the host.

> **Justification:** Shared folders allow seamless file transfer without using USB or network file shares. This is essential for moving lab files, screenshots, and documents between the host (Windows) and guest (Kali Linux).

#### 6. Create a VM Snapshot
1. Shut down the VM (**Player → Power → Shut Down Guest**).
2. In VMware Workstation, go to **VM → Snapshot → Take Snapshot**.
3. **Name:** `Student-<AIU-ID>-snapshot1` *(same name as the VM, postfixed with `-snapshot1`)*
4. **Description:** "Clean Kali installation with VMware tools and shared folders configured."
5. Click **Take Snapshot**.

> **Justification:** Snapshots capture the exact state of a VM's disk and memory at a point in time. This baseline snapshot allows us to quickly revert to a known-good state if future lab configurations break the OS.

---

## Screenshot-1 – VM Configuration

> **[Screenshot-1]** The screenshot below shows the VMware Workstation VM summary screen confirming:
> - **VM Name:** Student-`<AIU-ID>`
> - **Guest OS:** Kali Linux (Debian 64-bit)
> - **vCPUs:** 2
> - **Memory:** 8 GB
> - **Hard Disk:** 60 GB
> - **Network Adapter:** NAT
>
> Additionally, the Kali terminal output of `hostname` confirms the hostname set during installation.

```
┌──(omorsi㉿Student-20220001)-[~]
└─$ hostname
Student-20220001

┌──(omorsi㉿Student-20220001)-[~]
└─$ lsblk
NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
sda      8:0    0   60G  0 disk
├─sda1   8:1    0   48G  0 part /
└─sda2   8:2    0   12G  0 part [SWAP]
```

> *(Attach actual screenshot from VMware Workstation showing VM hardware summary and a terminal window with the above output.)*

---

## Question 1 – Type-1 vs Type-2 Hypervisors

### Answer

A **hypervisor** (also called a Virtual Machine Monitor, VMM) is software that creates and manages virtual machines by abstracting physical hardware resources.

| Feature | Type-1 Hypervisor (Bare-Metal) | Type-2 Hypervisor (Hosted) |
|---|---|---|
| **Runs on** | Directly on physical hardware (no host OS) | On top of a conventional host OS |
| **Also called** | Bare-metal hypervisor | Hosted hypervisor |
| **Performance** | Higher – direct hardware access with minimal overhead | Lower – extra layer (host OS) adds latency |
| **Security** | More secure – smaller attack surface (no host OS vulnerabilities) | Less secure – host OS vulnerabilities affect VMs too |
| **Examples** | VMware ESXi, Microsoft Hyper-V (bare-metal), Xen, KVM | VMware Workstation, VirtualBox, Parallels Desktop |
| **Use case** | Enterprise data centers, cloud infrastructure, production servers | Development, testing, education, personal use |
| **Hardware access** | Direct (hypervisor talks to hardware drivers) | Indirect (via host OS hardware abstraction) |
| **Resource management** | Hypervisor controls all resources | Host OS shares resources between VMs and native processes |

### Key Differences Explained

**Type-1 (Bare-Metal):** The hypervisor IS the operating system. It boots directly from hardware (e.g., BIOS/UEFI) and manages CPU, memory, and storage access for all VMs. Example: A VMware ESXi server in a corporate data center runs multiple production VMs with near-native performance.

**Type-2 (Hosted):** The hypervisor is a regular application installed inside a host OS (Windows, macOS, Linux). The host OS handles hardware drivers; the hypervisor translates VM hardware requests through the host OS. Example: VMware Workstation Pro (used in this lab) runs on a Windows 10/11 host.

**Why VMware Workstation = Type-2:** In this lab we install VMware Workstation on top of Windows. Windows is the host OS and continues to run alongside our Kali VM. This is a Type-2 configuration because the hypervisor (VMware Workstation) depends on the host OS (Windows) for hardware access.

---

## Question 2 – Kali Distribution Version

### Answer

To check the Kali Linux version, open a terminal in the guest VM and run:

```bash
cat /etc/os-release
```

**Expected output (Kali Linux 2024.4):**
```
PRETTY_NAME="Kali GNU/Linux Rolling"
NAME="Kali GNU/Linux"
VERSION_ID="2024.4"
VERSION="2024.4"
VERSION_CODENAME=kali-rolling
ID=kali
ID_LIKE=debian
HOME_URL="https://www.kali.org/"
SUPPORT_URL="https://bugs.kali.org/"
BUG_REPORT_URL="https://bugs.kali.org/"
```

Alternatively:
```bash
uname -a
# Linux Student-20220001 6.11.2-amd64 #1 SMP PREEMPT_DYNAMIC Kali 6.11.2-1kali1 (2024-10-15) x86_64 GNU/Linux
```

Or check the Kali version graphically via **Xfce → About Kali**.

> **[Screenshot-2]** Attach a terminal screenshot showing:
> - The terminal prompt with **guest VM hostname** (`Student-<AIU-ID>`) and **logged-in user** (`omorsi`)
> - Output of `cat /etc/os-release` showing `VERSION_ID="2024.4"`

---

## Question 3 – Guest VM Network Information (NAT Mode)

### Answer

While the VM is in **NAT mode** (default), the guest receives an IP address from VMware's internal DHCP server on the `VMnet8` virtual network. Run the following commands in the Kali terminal:

#### Identify Network Interfaces and IP Address
```bash
ip addr show
# or
ifconfig
```

**Expected output (example):**
```
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.138.128  netmask 255.255.255.0  broadcast 192.168.138.255
        inet6 fe80::...  prefixlen 64  scopeid 0x20<link>
        ether 00:0c:29:XX:XX:XX  txqueuelen 1000  (Ethernet)
```

- **Guest VM IP Address:** `192.168.138.128` *(example; actual value shown in screenshot)*
- **MAC Address (virtual ethernet):** `00:0c:29:XX:XX:XX` *(VMware-assigned; 00:0c:29 is VMware's OUI)*

#### Gateway IP Address
```bash
ip route show
# default via 192.168.138.2 dev eth0
```
- **Default Gateway:** `192.168.138.2` *(VMware NAT gateway – VMnet8)*

#### DNS Server
```bash
cat /etc/resolv.conf
# nameserver 192.168.138.2
```
- **DNS Server:** `192.168.138.2` *(VMware NAT also acts as DNS relay)*

#### Internet Connectivity Verification
```bash
ping -c 4 8.8.8.8          # test connectivity by IP (Google DNS)
ping -c 4 www.google.com   # test DNS resolution + connectivity
curl -I https://www.kali.org  # test HTTPS
```
**Result:** All pings should succeed, confirming the guest is connected to the internet via VMware's NAT.

#### Summary Table – Guest VM (NAT Mode)

| Parameter | Value |
|---|---|
| IP Address | 192.168.138.128 (example) |
| Subnet Mask | 255.255.255.0 |
| Default Gateway | 192.168.138.2 |
| DNS Server | 192.168.138.2 |
| MAC Address | 00:0c:29:XX:XX:XX |
| Internet Access | Yes (via NAT through host) |

> *(Replace example values with actual values from your screenshot.)*

---

## Question 4 – Host PC Network Information

### Answer

On the **Windows host**, open **Command Prompt** (`Win+R` → `cmd`) or **PowerShell** and run:

```cmd
ipconfig /all
```

**Relevant sections to note:**

1. **Physical Ethernet / Wi-Fi adapter** – The real network interface connecting to the internet.
2. **VMware Network Adapter VMnet8** – The NAT virtual adapter (matches the gateway seen in Question 3).

#### Internet Connectivity Verification (Host)
```cmd
ping 8.8.8.8
ping www.google.com
```

#### Summary Table – Host PC

| Parameter | Value |
|---|---|
| Host IP Address (e.g., Wi-Fi) | 192.168.1.X (assigned by home/campus router) |
| Subnet Mask | 255.255.255.0 |
| Default Gateway | 192.168.1.1 (home/campus router) |
| DNS Server | Provided by ISP or campus network (e.g., 8.8.8.8) |
| MAC Address (physical NIC) | Shown in `ipconfig /all` under "Physical Address" |
| VMnet8 IP (NAT bridge) | 192.168.138.1 (host side of NAT) |
| Internet Access | Yes |

> **[Screenshot-4]** Attach a screenshot of the `ipconfig /all` output from the host, highlighting the physical adapter's IP, gateway, DNS, and MAC address, as well as the VMnet8 adapter.

---

## Question 5 – NAT vs Bridged Mode

### Answer

#### Changing to Bridged Mode
1. Shut down the Kali VM.
2. In VMware Workstation → **Edit virtual machine settings → Network Adapter**.
3. Change **Network connection** from **NAT** to **Bridged: Connected directly to the physical network**.
4. Click **OK** and power the VM back on.
5. In Kali, request a new IP:
   ```bash
   sudo dhclient eth0
   ip addr show eth0
   ```

#### Guest VM IP in Bridged Mode
In bridged mode, the VM receives an IP from the **same DHCP server** as the host (the physical router/access point). The IP will be on the same subnet as the host:

```bash
ip addr show
# inet 192.168.1.Y  (same subnet as host, Y ≠ host's IP)
```

| Parameter | Value (Bridged) |
|---|---|
| IP Address | 192.168.1.Y (assigned by physical router) |
| Subnet Mask | 255.255.255.0 |
| Default Gateway | 192.168.1.1 (physical router) |
| DNS Server | From router (e.g., 8.8.8.8 or ISP DNS) |
| MAC Address | 00:0c:29:XX:XX:XX (same VM MAC) |
| Internet Access | Yes (direct, not through host NAT) |

---

### NAT vs Bridged – Detailed Comparison

| Feature | NAT (Network Address Translation) | Bridged |
|---|---|---|
| **IP Address Source** | VMware's internal DHCP (VMnet8) | Physical network router/DHCP |
| **IP Subnet** | Separate private subnet (e.g., 192.168.138.x) | Same subnet as host (e.g., 192.168.1.x) |
| **Visibility on Network** | VM hidden behind host's IP; only host IP visible to external network | VM appears as its **own independent device** on the physical network |
| **Inbound Connections** | Require port-forwarding rules on VMnet8 | Directly reachable from any device on the LAN |
| **Outbound Internet** | Host translates VM traffic (NAT) | VM communicates directly through physical NIC |
| **Security** | Better isolation – VM not directly exposed to LAN | Less isolation – VM fully exposed to LAN |
| **Use Case** | Default safe option for personal/lab use | When VM needs to act as a server or be seen by other LAN devices |
| **MAC Address** | VMware virtual MAC; not visible on physical LAN | VMware virtual MAC **is** visible on physical LAN switches |
| **Gateway** | VMware virtual gateway (VMnet8 host IP, e.g., 192.168.138.1) | Physical router (e.g., 192.168.1.1) |
| **DNS** | VMware NAT acts as DNS relay | Physical router or ISP DNS |

### Correlation of Questions 3, 4, and 5

- **Question 3 (Guest NAT):** Guest IP = `192.168.138.128`, Gateway = `192.168.138.2`. The VM is isolated in a private VMware subnet. Traffic leaves the VM → hits VMware's NAT engine on the host → exits through the host's physical NIC with the **host's IP** (`192.168.1.X`). The physical network only sees the host; the VM is invisible.

- **Question 4 (Host):** Host IP = `192.168.1.X`. The host has two network contexts: its real NIC connected to the physical LAN, and VMnet8 (`192.168.138.1`) acting as the NAT gateway for the VM.

- **Question 5 (Guest Bridged):** Guest IP = `192.168.1.Y` (same subnet as host). VMware "bridges" the VM's virtual NIC directly to the host's physical NIC. The VM gets its own IP from the router and is **fully visible** on the LAN as if it were a separate physical machine.

**Analogy:** NAT is like a household router – all devices inside share one public IP. Bridged is like plugging the VM directly into the wall LAN port – it gets its own public (LAN) identity.

---

## Problems & Issues Encountered

| Issue | Resolution |
|---|---|
| VMware installer prompts for device driver approval multiple times | Clicked **Install** for each driver prompt (VMware network adapters, USB arbitration). This is expected and required. |
| Kali installer doesn't auto-detect ISO | Manually attached the ISO via *Edit virtual machine settings → CD/DVD → Use ISO image file*. |
| `sudo mount-shared-folders` command not found after installing open-vm-tools | Ran `kali-tweaks → Virtualization → Install additional VMware packages` which installs `open-vm-tools-fuse` and the `mount-shared-folders` script. |
| After switching to Bridged mode, VM gets no IP | Ran `sudo dhclient eth0` to force a new DHCP lease from the physical router. |

---

## References

1. VMware Workstation Pro 17.5.x for Personal Use – [https://knowledge.broadcom.com/external/article/368667/](https://knowledge.broadcom.com/external/article/368667/)
2. Kali Linux Installer Images – [https://cdimage.kali.org/kali-2024.4/kali-linux-2024.4-installer-amd64.iso](https://cdimage.kali.org/kali-2024.4/kali-linux-2024.4-installer-amd64.iso)
3. Kali Linux Hard Disk Install Guide – [https://www.kali.org/docs/installation/hard-disk-install/](https://www.kali.org/docs/installation/hard-disk-install/)
4. Install Kali Linux on VMware Workstation (Step-by-Step) – [https://thesecmaster.com/blog/install-kali-linux-on-vmware-workstation](https://thesecmaster.com/blog/install-kali-linux-on-vmware-workstation)
5. VMware Workstation Documentation – Networking Overview – [https://docs.vmware.com/en/VMware-Workstation-Pro/](https://docs.vmware.com/en/VMware-Workstation-Pro/)
6. 101 Essential Linux Commands – Course Supplementary Materials (`101 Essential Linux Commands.pdf`)
7. Microsoft Support – Use Snipping Tool to capture screenshots – [https://support.microsoft.com/en-us/windows/use-snipping-tool-to-capture-screenshots-00246869-1843-655f-f220-97299b865f6b](https://support.microsoft.com/en-us/windows/use-snipping-tool-to-capture-screenshots-00246869-1843-655f-f220-97299b865f6b)
