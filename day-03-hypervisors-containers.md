# Day 3: Hypervisors, VMs and How Containers Really Work

> 60-Day AI Infrastructure Challenge · Day 3 · 6 Nov 2026

**Story:** A company has one big server and wants to run 3 apps: a website, a database, and an AI model.

---

## 1. Why VMs were invented

**Option A:** install all 3 apps directly on the server
- One computer can only run one OS (website needs Windows, AI model needs Linux) ❌
- One app crashes → can take the whole server down ❌
- Apps need different library versions → conflict ❌

**Option B:** buy 3 servers → each idle ~90% of the time 💸

**Goal:** one physical server that behaves like 3 separate computers → **VMs**.

---

## 2. Virtual Machine (die virtuelle Maschine)

**EN:** A VM is a **computer made of software**. It runs inside a real computer but behaves like a separate one.

A VM has:
- virtual CPU (a share of the real CPU)
- virtual RAM (a share of the real RAM)
- virtual disk (really one big file on the real disk)
- its **own full operating system with its own kernel**

The OS inside doesn't know it's virtual.

```
One physical server
 ├── VM 1: Windows → website
 ├── VM 2: Linux   → database
 └── VM 3: Linux   → AI model
```
VM 2 crashes → VM 1 and VM 3 keep running ✅

**DE:** *Eine virtuelle Maschine ist ein Computer aus Software. Sie läuft auf einem echten Computer und hat ein eigenes Betriebssystem.*

---

## 3. Hypervisor (der Hypervisor), it's software!

**EN:** The software that **creates and runs VMs**. Two jobs:
1. **Splits** the real CPU, RAM and disk into pieces for each VM
2. **Keeps VMs apart**, so they can't see or disturb each other

No hypervisor = no VMs.

**DE:** *Der Hypervisor ist die Software, die virtuelle Maschinen erstellt und verwaltet.*

### Type 1 vs Type 2 (two types of hypervisors)

The only difference: **where the hypervisor sits**.

```
Type 2:  Hardware → Windows → VirtualBox (an app) → VM
Type 1:  Hardware → Hypervisor (directly on hardware) → VMs
```

| | Type 1 | Type 2 |
|---|---|---|
| Sits on | The hardware directly | A normal OS (Windows, macOS) |
| Speed | Faster | Slower (goes through the OS) |
| Where | Servers, cloud (Azure) | Your laptop, learning/testing |
| Example | Hyper-V, VMware ESXi, KVM | VirtualBox |

🏠 **Type 2** = subletting a room in your flat (you still live there).
🏨 **Type 1** = a hotel (the whole building is made for renting rooms).

💡 **WSL2** (Linux on Windows) runs in a small VM on Hyper-V.
💡 Creating a VM in **Azure** → a Type 1 hypervisor creates it on a physical server.
💡 **AI link:** clouds can give a VM direct access to a whole GPU (**GPU passthrough**) → that's how you rent GPU machines.

---

## 4. The problem with VMs

- Each VM has a **full OS** → several GB
- Starting takes **minutes** (boots a whole OS)
- 10 small apps = 10 operating systems → wasted RAM and disk
- Plus: 😩 *"It works on my machine!"*

---

## 5. Container (der Container)

**EN:** A **box that packs your app + everything it needs** (.NET runtime, libraries, config), but **without its own kernel**.

🚢 Name comes from **shipping containers**: one standard box that fits every ship, train and truck.

### Why containers?

| Reason | Meaning |
|---|---|
| Same everywhere | Runs the same on laptop, test server and production |
| Light | MBs instead of GBs |
| Fast | Starts in seconds |
| Many per server | 50 containers where only 5 VMs fit |
| Easy to scale | Need 10 copies of an AI model? Start 10 containers |

**DE:** *Ein Container verpackt eine Anwendung mit allem, was sie zum Laufen braucht. Er läuft überall gleich.*

---

## 6. Container and the kernel

- **VM** brings its **own kernel**
- **Container** **borrows the host's kernel** → for the kernel, a container is just a **normal process**

🏠 VM = separate houses (own heating/electricity). Container = apartments in one building (shared).

**New problem:** normal processes can **see each other** and **take all RAM/CPU**.
**Solution:** two Linux kernel features → **namespaces** and **cgroups**.
(They already existed in the kernel; Docker, 2013, made them easy to use.)

---

## 7. Namespaces = walls 🧱 (what a container can SEE)

Each container gets its **own private view**:

| Namespace | What the container sees |
|---|---|
| PID | Only its own processes (its app is PID 1) |
| Network | Its own IP address and ports |
| Mount | Its own files |
| UTS | Its own hostname |
| User | Its own users (root inside ≠ root on host) |

🏠 Own rooms, own door number and mailbox, own furniture.

**Example:** 2 containers can **both** use port 80, because each has its own network namespace.

**DE:** *Namespaces sorgen dafür, dass jeder Container nur seine eigenen Prozesse, Dateien und Netzwerke sieht.*

---

## 8. cgroups = limits 📏 (how much a container can USE)

cgroups (control groups) set a **maximum** for **CPU and RAM** (and disk speed).

🏠 Each apartment has its own electricity meter with a fuse.

- **Without cgroups:** one buggy container eats all RAM → whole server crashes ❌
- **With cgroups:** container hits its limit → kernel stops **only that container** = **OOMKilled** ✅

In Kubernetes (later):
```yaml
resources:
  limits:
    memory: "512Mi"   # enforced by cgroups
    cpu: "1"
```

⚠️ GPUs are handled differently: Kubernetes gives a container a **whole GPU** (learned later).

**DE:** *cgroups begrenzen, wie viel CPU und Arbeitsspeicher ein Container verbrauchen darf.*

---

## 9. Everything together

| | VM | Container |
|---|---|---|
| What it is | Computer made of software | Box with app + what it needs |
| Created by | Hypervisor | Docker (using kernel features) |
| Own kernel? | Yes | No, borrows host kernel |
| Isolation by | Hypervisor | Namespaces (walls) |
| Limits by | Hypervisor | cgroups (meters) |
| Size / start | GBs / minutes | MBs / seconds |

**In real companies, both together:**
```
Physical server
 → Type 1 hypervisor (e.g. Azure)
    → VM (Linux)
       → Docker / Kubernetes
          → many containers (.NET API, AI model, ...)
```
VMs keep **different customers** strongly separated. Containers make **apps** light and fast.
(Interview question: *"Why still VMs if we have containers?"* → stronger isolation, because containers share one kernel.)

---

## 🎯 Remember these 4 lines

1. **VM** = a computer made of software, with its own OS.
2. **Hypervisor** = software that creates VMs. Type 1 on hardware (cloud), Type 2 as an app (laptop).
3. **Container** = a light box for an app, borrowing the host's kernel.
4. **Namespaces** = what it can **see**. **cgroups** = how much it can **use**.

**DE:**
1. *Eine VM ist ein Computer aus Software mit eigenem Betriebssystem.*
2. *Ein Hypervisor erstellt VMs.*
3. *Ein Container nutzt den Kernel des Hosts.*
4. *Namespaces bestimmen, was ein Container sieht. cgroups bestimmen, wie viel er nutzen darf.*

### Interview answer
**EN:** Containers use two Linux kernel features: namespaces isolate what a process can see, like processes, network and files, and cgroups limit what it can use, like CPU and memory. A hypervisor, in contrast, creates full virtual machines with their own kernel.

**DE:** *Container nutzen zwei Funktionen des Linux-Kernels: Namespaces isolieren, was ein Prozess sieht, und cgroups begrenzen, wie viele Ressourcen er nutzt. Ein Hypervisor erstellt dagegen komplette virtuelle Maschinen mit eigenem Kernel.*

---

## ✍️ Self-check: my answers and review

### 1. Is a hypervisor hardware or software? What does it do? ✅
**My answer:** Software that creates and manages VMs. It splits the real CPU, RAM and disk into pieces for each VM and keeps the VMs apart so they can't see each other.
**Review:** Perfect.

### 2. Why containers if we already have VMs? ✅
**My answer:** Each VM has a full OS, several GB, takes a long time to start, and wastes RAM and disk. A container is a box with the app and everything it needs, without its own kernel. It borrows the host kernel, so it is light and very fast.
**Add:** Containers **run the same everywhere** → no "works on my machine".

### 3. Namespaces vs cgroups ✅
**My answer:** Containers share the kernel, so they could see each other and take all RAM/CPU. That's why namespaces and cgroups are needed. Namespace = wall (what you are allowed to see). cgroup = limit (how much RAM/CPU is assigned).
**Corrections:**
- cgroups limit **CPU and RAM** (and disk speed), **not GPU**. GPUs are assigned whole by Kubernetes.
- Namespaces and cgroups already existed in the Linux kernel; Docker made them easy to use.

**Polished (EN):** Containers share the host kernel, so without protection they could see each other and use all resources. Namespaces are the walls: they control what a container can see. cgroups are the limits: they control how much CPU and memory it can use.

**Polished (DE):** *Namespaces sind die Wände: Sie bestimmen, was ein Container sieht. cgroups sind die Grenzen: Sie bestimmen, wie viel CPU und Arbeitsspeicher er nutzen darf.*

---

## 🖥️ Hands-on (Windows)

1. PowerShell: `wsl --list --verbose`
   - Ubuntu with VERSION 2 → WSL2 works (a Hyper-V VM!)
   - If not: `wsl --install`, then restart
2. In Ubuntu: `uname -r` → shows the kernel version

---

## 🇩🇪 Vocabulary

| German | English |
|---|---|
| die virtuelle Maschine | virtual machine |
| der Hypervisor | hypervisor |
| aufteilen (split verb) | to split up |
| physisch | physical |
| der Rechner | computer |
| echt | real |
| erstellen | to create |
| verwalten | to manage |
| verpacken | to pack |
| überall | everywhere |
| gleich | the same |
| sorgen dafür, dass | to make sure that |
| sehen | to see |
| begrenzen | to limit |
| verbrauchen | to consume |
| dürfen | to be allowed to |
| die Wand / die Wände | wall / walls |
| die Grenze | limit / border |
| dagegen | in contrast |
| komplett | complete |

💡 **Memory trick:** Namespaces → *sehen* (see). cgroups → *begrenzen* (limit).
