# Cybersecurity Lab Setup: VirtualBox + Kali Linux

**Week 1 Project (WK1-PM1), Cybersecurity Internship at Networkwalks**

In this project I built a small cybersecurity practice lab on my own laptop. Kali Linux runs as the attacker machine inside VirtualBox, on its own isolated NAT Network. This lab is the base I'll use for the hands-on tasks in the coming weeks.

---

## Task Requirements

- Use VirtualBox as the base (latest recommended version)
- Set up Kali Linux as the attacking machine
- Create a NAT Network on the subnet `10.0.0.0/24`
- Enable the shared clipboard and drag-and-drop
- Share the host `Downloads` folder with the VM
- Give Kali the static IP `10.0.0.2/24`
- Make sure Kali has full internet access
- Take a snapshot of the VM

---

## Lab Overview

| Item | Value |
|---|---|
| Host OS | Windows 10 |
| Hypervisor | Oracle VirtualBox |
| Attacker VM | Kali Linux (64-bit) |
| VM Memory | 3238 MB |
| Network type | NAT Network (`NatNetwork`) |
| Subnet | `10.0.0.0/24` |
| Kali IP | `10.0.0.2/24` |
| Gateway | `10.0.0.1` |
| DNS | `8.8.8.8` |
| Promiscuous Mode | Allow All |
| Shared Clipboard / Drag-and-Drop | Bidirectional |
| Shared folder | Host `Downloads` (Auto-mount, Full access) |

---

## Network Diagram

```
        Windows 10 (Host)
               |
        +--------------+
        |  VirtualBox  |
        +--------------+
               |
     NatNetwork 10.0.0.0/24
               |
        Kali Linux
        10.0.0.2/24
        Gateway: 10.0.0.1
```

---

## Steps I Followed

### 1. Installed the tools
I installed 7-Zip (to extract the Kali download) and the latest VirtualBox from the official website.

### 2. Created the NAT Network
In VirtualBox, I opened **File → Tools → Network → NAT Networks** and created a network with these settings:
- Name: `NatNetwork`
- IPv4 Prefix: `10.0.0.0/24`
- DHCP: enabled

### 3. Imported Kali Linux
I downloaded the Kali Linux virtual machine from kali.org and added it to VirtualBox.

### 4. Configured the VM settings
With the VM powered off, I changed these settings:
- **Network → Adapter 1:** Attached to `NAT Network`, Name `NatNetwork`, Promiscuous Mode `Allow All`
- **General → Advanced:** Shared Clipboard and Drag-and-Drop set to `Bidirectional`
- **Shared Folders:** added my Windows `Downloads` folder with Auto-mount and Make Permanent ticked and Read-only unticked

### 5. Set the static IP in Kali
I used the Kali network settings (Edit Connections → IPv4 Settings → Manual) with:
- Address: `10.0.0.2`
- Netmask: `24`
- Gateway: `10.0.0.1`
- DNS: `8.8.8.8`

The same thing can be done from the terminal:

```bash
sudo nmcli con mod "Wired connection 1" ipv4.method manual \
  ipv4.addresses 10.0.0.2/24 ipv4.gateway 10.0.0.1 ipv4.dns 8.8.8.8
sudo nmcli con down "Wired connection 1"
sudo nmcli con up "Wired connection 1"
```

### 6. Took a snapshot
Once everything worked, I took a snapshot so I can always go back to a clean, working lab.

---

## Verification

I ran these commands in Kali to check the setup:

```bash
ip a                  # confirm the IP is 10.0.0.2/24
ping -c 3 10.0.0.1    # gateway
ping -c 3 8.8.8.8     # internet
ping -c 3 google.com  # DNS
ls /media             # shared folder
```

| Check | Result |
|---|---|
| Kali IP is `10.0.0.2/24` | [ ] Pass |
| Ping to gateway `10.0.0.1` | [ ] Pass |
| Ping to `8.8.8.8` | [ ] Pass |
| Ping to `google.com` | [ ] Pass |
| Shared folder visible in Kali | [x] Pass (`sf_Downloads` on the desktop) |
| Clipboard works both ways | [ ] Pass |
| Snapshot taken | [ ] Pass |

*(Tick each box as you confirm it and delete this note.)*

---

## Screenshots

### Kali Linux running with the shared folder mounted
![Kali desktop with sf_Downloads](screenshots/01-kali-desktop-shared-folder.png)

### NAT Network settings
![NAT Network](screenshots/02-nat-network.png)

### Kali network adapter settings
![Adapter settings](screenshots/03-kali-adapter-settings.png)

### Static IP configuration
![ip a output](screenshots/04-ip-a.png)

### Connectivity tests
![Ping tests](screenshots/05-ping-tests.png)

### Snapshot
![Snapshot](screenshots/06-snapshot.png)

---

## Problems I Faced and How I Fixed Them

*(Write this part in your own words. Examples of things that might have happened to you:)*

- The Name field said "Not selected" in the network settings, because the NAT Network did not exist yet. I created it first and then selected it.
- Some VM settings were greyed out while Kali was running, so I shut the VM down before changing them.
- Drag-and-drop failed at first. [Explain what you did, for example installing the Guest Additions, or using the shared folder instead.]
- If Kali had no internet after setting the static IP, this fix worked for VirtualBox 7 with newer Kali versions:

```bash
sudo nmcli connection modify "Wired connection 1" ipv4.dad-timeout 0
sudo nmcli connection down "Wired connection 1"
sudo nmcli connection up "Wired connection 1"
```

---

## What I Learned

*(Write 3 to 5 sentences in your own words. Ideas:)*

- How a NAT Network lets several VMs talk to each other and still reach the internet
- Why a fixed IP is useful in a lab where I need to know where each machine is
- How snapshots protect me from breaking the lab while practising
- What the shared folder and clipboard make easier

---

## Next Steps

- Add more machines to the lab (Windows or other targets) on `10.0.0.0/24`
- Practise basic scanning and enumeration against my own lab machines only
- Keep a snapshot of each clean setup

---

## Disclaimer

This lab is for learning and is used only on machines I own or have permission to test.

---

**Author:** Rafia Noor
**Program:** Cybersecurity Internship, Networkwalks (Oct 25 – Nov 22, 2026)
