# 🐉 Kali Linux on UTM – Installation Lab (Apple Silicon ARM64)
*TryHackMe Labs – Environment Setup*
*(Lab 00 – Base System Configuration)*

---

## 🎯 Lab Summary

This lab documents the installation and configuration of **Kali Linux** as a virtual machine on **Apple Silicon (M1)** using **UTM** as the hypervisor. Getting a fully functional Kali environment running on ARM64 required some non-obvious steps — particularly around display configuration — that are worth documenting to save future headaches.

The main tasks were to:
- Download the correct Kali Linux ARM64 installer image
- Configure UTM with appropriate virtualization settings
- Work around a known display bug during text-based installation
- Clean up post-installation configuration
- Boot into a fully operational Kali Linux environment

---

## ⚙️ Environment Specs

| Component | Detail |
|---|---|
| **Host Machine** | MacBook Pro (Apple M1) |
| **Hypervisor** | UTM (Virtualize mode) |
| **Guest OS** | Kali Linux ARM64 (Installer Image) |
| **RAM Allocated** | 6 GB |
| **CPU Cores Allocated** | 6 |
| **Storage Allocated** | 64 GB |
| **Source Image** | [kali.org – Installer Images (ARM64)](https://www.kali.org/get-kali/#kali-installer-images) |

> **Why Virtualize over Emulate?** UTM offers two modes: Emulate (slower, simulates x86) and Virtualize (near-native performance using Apple's Hypervisor framework). Since Kali now provides native ARM64 images, Virtualize is the correct and most performant choice on M1/M2/M3 hardware.

---

## 🛠️ Installation Steps

### 1. Download Required Files

- Downloaded UTM from the [official UTM website](https://mac.getutm.app/)
- Navigated to [Kali Linux Downloads](https://www.kali.org/get-kali/#kali-installer-images) → **Installer Images** → selected **Apple Silicon ARM 64** `.iso`

---

### 2. Create the Virtual Machine in UTM

1. Opened UTM and clicked **+** (Create New VM)
2. Selected **Virtualize**
3. Selected **Linux**
4. Clicked **Browse** under the boot ISO field → selected the downloaded Kali ARM64 `.iso`
5. Allocated resources: **6 GB RAM / 6 CPU cores**
6. Set storage to **64 GB**
7. Named the VM `Kali Linux` and clicked **Save**

---

### 3. ⚠️ Critical Step – Add Serial Device (Avoid Black Screen)

This was the main issue encountered during first boot. Without this step, the VM boots into a **completely black screen** with no display output.

As documented in the [Kali Linux UTM guide](https://www.kali.org/docs/virtualization/install-utm-guest-vm/), a **Serial device** must be added before installation:

1. Right-clicked the Kali VM in UTM → **Settings**
2. Went to **Devices** → clicked **New**
3. Added a **Serial** device
4. Saved settings

> This forces the installer to use a **text-based console** interface instead of the graphical installer, which is known to cause blank screen issues on ARM64 with certain display configurations.

---

### 4. Installation Process

1. Clicked **Play** to start the VM
2. Selected **Install** (text-based — NOT Graphical Install)
3. Followed the terminal prompts:
   - Selected language and region
   - Set hostname: `kali`
   - Configured user credentials
   - Selected **Guided – use entire disk** for partitioning
4. Completed installation and shut down the VM when prompted

---

### 5. Post-Installation Cleanup

After installation, the Serial device and ISO must be removed to boot normally:

1. Opened UTM **Settings** for the Kali VM
2. Removed the **Serial** device under Devices
3. Cleared the installed ISO from the **virtual CD/DVD drive**
4. *(Optional)* Changed display card to `virtio-gpu-pci` for better graphics performance
5. Booted the VM — Kali Linux loaded successfully into the login screen

---

## 🧠 Reflections / Notes

- The **black screen issue** is the most common blocker for first-time Kali installs on UTM — it is not a hardware problem, it is a display driver issue during installation that is solved by using the Serial device workaround
- **Virtualize mode** is the right call on Apple Silicon — Emulate mode exists for legacy x86 software, not for running ARM-native operating systems
- Allocating **6 GB RAM and 6 cores** provides comfortable headroom for running memory-intensive tools like Burp Suite or Metasploit alongside the host OS
- **64 GB storage** is generous for a lab environment — 30 GB is the minimum, but wordlists and tool installations add up quickly
- The text-based installer looks intimidating at first but is straightforward — and more reliable than the graphical one in virtualized environments

---

## 📚 Key Skills Demonstrated

- Virtualization setup on **Apple Silicon (ARM64)** using UTM
- Selection and configuration of appropriate **hypervisor mode** (Virtualize vs. Emulate)
- Troubleshooting a **known display bug** using Serial device workaround
- Post-installation **VM configuration cleanup**
- Baseline understanding of **resource allocation** for a security lab environment

---

## 🔗 References

- [UTM – Official Website](https://mac.getutm.app/)
- [Kali Linux Downloads – Installer Images](https://www.kali.org/get-kali/#kali-installer-images)
- [Kali Linux – UTM Guest VM Documentation](https://www.kali.org/docs/virtualization/install-utm-guest-vm/)

---

*This lab documents the foundational environment setup required for all subsequent TryHackMe practical exercises. A stable and correctly configured Kali Linux VM is the baseline for all offensive and defensive security tooling used in this repository.*
