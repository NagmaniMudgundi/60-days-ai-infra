# Day 2: Operating System Basics + How AI Uses Hardware

> 60-Day AI Infrastructure Challenge · Day 2 · 5 Nov 2026

---

## 1. Operating System (das Betriebssystem)

**EN:** The OS is the manager between programs and hardware. Programs never touch the hardware directly. They ask the OS.
Examples: Windows, Linux, macOS. Almost all servers and AI systems run on **Linux**.

**DE:** *Das Betriebssystem verwaltet die Hardware und verteilt die Ressourcen an die Programme.*

🍕 Analogy: the **kitchen manager**. Cooks (programs) ask the manager for ovens and ingredients.

### How does the OS do it? 4 tools

| Tool | What it does | Analogy |
|---|---|---|
| **System call** (*Systemaufruf*) | The only way a program can ask the kernel: read a file, get memory, use the network | A cook fills in an order slip |
| **Scheduler** | Gives each process a few milliseconds on a CPU core, then switches | "You cook 2 minutes, then you" |
| **Memory manager** | Gives each process its own area of RAM, protects it from others | Each cook gets their own part of the counter |
| **Drivers** (*Treiber*) | Small programs in the kernel that speak to one device (disk, Wi-Fi, GPU) | A translator for each machine |

---

## 2. CPU (der Prozessor)

**EN:** The CPU executes instructions. It has several **cores**; each core works on one thing at a time, but very fast. The OS switches between tasks so fast that it looks parallel.

**DE:** *Die CPU führt Befehle aus. Mehrere Kerne können parallel arbeiten.*

🍕 CPU cores = **cooks**. 8 cores = 8 cooks.

---

## 3. Memory (der Arbeitsspeicher / RAM) and Disk (die Festplatte)

| | RAM | Disk |
|---|---|---|
| Speed | Fast | Slow |
| Lifetime | Temporary, empty after restart | Permanent |
| Used for | Running programs | Storing files |

**DE:** *Der Arbeitsspeicher ist schnell, aber flüchtig. Die Festplatte speichert Daten dauerhaft.*

🍕 RAM = kitchen counter (fast, small). Disk = storage room (big, slow).

⚠️ If RAM is full → the process is killed: **OOM (Out of Memory)**. Very common in Kubernetes ("OOMKilled").

---

## 4. Process (der Prozess)

**EN:** A **program** is a file on the disk. A **process** is that program **while it is running**, with its own memory and an ID (**PID**).
One program can run as many processes (e.g. Chrome opened 3 times).

**DE:** *Ein Prozess ist ein laufendes Programm mit eigenem Speicher und einer Prozess-ID.*

🍕 Recipe in the book = program. A cook cooking that pizza right now = process.

---

## 5. Kernel (der Kernel)

**EN:** The core of the OS. It talks directly to the hardware and decides which process gets CPU time and how much memory.

- **Kernel space:** full power over hardware
- **User space:** apps run here with limited rights (safety)

**DE:** *Der Kernel ist der Kern des Betriebssystems und steuert den Zugriff auf die Hardware.*

🍕 The kernel = head chef.

### The layers

```
Apps / processes (Ollama, Chrome, C# app)    ← user space
System calls (the only door)
KERNEL: scheduler · memory manager · file system · drivers   ← kernel space
HARDWARE: CPU · RAM · Disk · GPU · Network
```

---

## 6. VM vs Container ⭐ (top interview question)

| | VM | Container |
|---|---|---|
| Own kernel? | Yes (full OS) | No, shares the host kernel |
| Size | GBs | MBs |
| Start time | Minutes | Seconds |
| Isolation | Stronger | Weaker (enough for most apps) |
| Runs on | Hypervisor | Container runtime (Docker) |
| Example | Azure VM | Docker, Kubernetes pods |

🏠 VM = separate houses, each with own kitchen and electricity.
🏢 Container = apartments in one building, sharing water and electricity.

**Interview answer (EN):** A VM virtualizes the hardware and runs its own OS. A container virtualizes only the OS. It shares the host kernel, so it is smaller and starts faster.

**DE:** *Eine VM virtualisiert die Hardware und hat ein eigenes Betriebssystem. Ein Container teilt sich den Kernel des Hosts und ist daher kleiner und schneller.*

**AI link:** containers start fast → ideal for scaling AI models up and down.

---

## 7. The full flow: what happens when I run an AI model

Example: `ollama run llama3.2` → ask "What is a meteor?"

1. The OS loads the Ollama program from **disk** into **RAM** → it becomes a **process** (PID).
2. The **scheduler** gives the process time on a **CPU core**.
3. **System call:** Ollama asks the kernel to read the model file → file system → disk driver → into RAM.
4. **System call:** Ollama asks for more memory. Not enough? → **OOM**, process killed.
5. If there is a GPU: the model is copied from RAM into the **GPU's memory (VRAM)** through the **GPU driver**. (It is copied because the GPU needs it there to calculate fast, **not** because RAM is full.)
6. The model does huge amounts of multiplications → the GPU does thousands at once.
7. The answer goes back token by token → kernel → screen.

**DE:**
1. *Ein Programm wird von der Festplatte in den Arbeitsspeicher geladen und läuft dann als Prozess.*
2. *Der Prozess bittet den Kernel über Systemaufrufe um Ressourcen.*
3. *Der Kernel verteilt CPU-Zeit und Speicher und spricht über Treiber mit der Hardware.*

💡 German tip: tech German uses the **passive** a lot: *wird geladen* (is loaded), *wird ausgeführt* (is executed), *wird bereitgestellt* (is deployed).

---

## 8. How an AI model works (explained simply)

### What is a model?
A **huge file of numbers** called **weights** (*Gewichte*). llama3.2 ≈ 3 billion numbers. They hold the "knowledge" (patterns) learned in training.

### What is a token?
Not the request. The request (a sentence) is **cut into small pieces**; each piece is a token.
"Where is my order?" → `Where` · `is` · `my` · `order` · `?` = 5 tokens

### The guessing game = the "multiplication"
"The cat sat on the ___"

| Clue | Points to "mat" | Points to "moon" |
|---|---|---|
| cat | 3 | 0 |
| sat | 2 | 1 |

- Score "mat" = 3 + 2 = **5** ✅ winner
- Score "moon" = 0 + 1 = 1

*Clue × importance*, add up, highest score wins. The importance numbers (3, 2, 0, 1) **are the weights**.
A real model does this with ~100,000 possible tokens and billions of weights, for every single token of the answer.

### Training
Like a child reading every book in the world while playing the guessing game. Wrong guess → adjust the numbers. After billions of guesses, the numbers are frozen and saved in a file = **the model**.

**DE:** *Beim Training lernt das Modell, welche Wörter zusammenpassen. Dieses Wissen wird als Zahlen gespeichert, den sogenannten Gewichten.*

---

## 9. Why AI needs GPUs (NVIDIA chips)

### CPU vs GPU

| | CPU | GPU |
|---|---|---|
| Cores | 8–64 smart cores | Thousands of simple cores |
| Best for | Different, complex tasks one after another | The same simple math, massively in parallel |
| Analogy | 8 master chefs | 5,000 helpers chopping onions at the same time |

The AI calculations are **independent** (the score for "mat" doesn't wait for "moon") → perfect for parallel work → **GPU wins**.

### Second reason: memory speed ⭐
For every token, the GPU must read **all the weights**. AI GPUs have very fast memory (**HBM**, High Bandwidth Memory). VRAM size and speed are huge topics in AI infrastructure.

### Why NVIDIA?
- GPUs were built for **video games** (millions of pixels, same math in parallel). Around 2012, researchers found they are perfect for neural networks (AlexNet was trained on NVIDIA gaming GPUs).
- **CUDA** (since 2007): NVIDIA made GPUs programmable early. Almost all AI software (PyTorch, vLLM) is built on CUDA → NVIDIA's real advantage.
- Competitors: AMD (ROCm), Google (TPUs), and others.

### Training vs inference
- **Training** = creating the model → thousands of GPUs for weeks/months
- **Inference** = using the model to answer → billions of times a day. **AI infra engineers mostly work on inference.**

### The GPU software stack

```
AI app (Ollama, vLLM, PyTorch)
   ↓
CUDA            ← NVIDIA's toolkit: lets programs send math to the GPU
   ↓
NVIDIA driver   ← lives in the KERNEL, talks to the GPU
   ↓
NVIDIA GPU + VRAM
```

### The GPU cannot work alone
The **CPU is the boss**: it runs the OS and the AI program and sends the heavy math to the GPU.

1. CPU runs OS + AI program (process)
2. Program → CUDA library
3. CUDA → NVIDIA driver (in the kernel)
4. Driver sends job + data to the GPU (VRAM)
5. GPU does billions of multiplications in parallel
6. Result → CPU → kernel → screen

⚠️ **Word warning:** a **CUDA kernel** is a small function that runs on the GPU. Different from the **OS kernel**!

**DE:**
- *Ein KI-Modell besteht aus Milliarden von Zahlen, den sogenannten Gewichten.*
- *Eine GPU kann diese Berechnungen parallel ausführen, deshalb ist sie viel schneller als eine CPU.*
- *Die CPU steuert den Ablauf, die GPU übernimmt die schwere Rechenarbeit.*

---

## 10. My questions & answers (Q&A)

**Q: If I use ChatGPT on my laptop, is the model downloaded to my laptop?**
A: No. The ChatGPT app is only a window. The model runs in OpenAI's data center on GPUs. My text goes there over the internet. (With **Ollama**, a small model *is* downloaded and runs on my laptop.)

**Q: Is a token the request?**
A: No. The request is cut into small pieces; each piece is a token.

**Q: Does ChatGPT search for the answer? Is there a big file of answers on the server that it matches?**
A: No. There are no stored answers and no matching. The server only has the weights file (numbers). Every answer is **calculated new**, token by token.
- Cookbook lookup = Google / database ❌
- Experienced chef cooking from learned patterns = LLM ✅
- That's why it can answer new questions, and also why it can **hallucinate**.
- When ChatGPT shows sources, a **web search tool** finds text and puts it into the prompt. The model still only guesses tokens. (= RAG / tool calling, coming later.)

**DE:** *Das Modell speichert keine Antworten. Es erzeugt jede Antwort neu, Token für Token.*
**DE:** *Ein KI-Modell sucht nicht im Internet, sondern berechnet das wahrscheinlichste nächste Wort.*

**Q: My laptop is old and only has a CPU, but ChatGPT still works. So are NVIDIA chips only needed in data centers?**
A: Yes. The GPU is needed **where the model runs**, not where the question is typed.

| Who | Needs a GPU? |
|---|---|
| User of ChatGPT on any laptop/phone | No |
| Developer calling OpenAI / Azure OpenAI API from C# | No |
| OpenAI, Google, Anthropic, Microsoft | Yes, thousands |
| Company running its own model (privacy / DSGVO) | Yes |
| Anyone training a model | Yes, many |
| Running Ollama locally | Optional: works on CPU, slower, small models only |

**My future job:** the AI infrastructure engineer runs those GPU servers: keeps them up, scales them, makes them fast and cheap.

**DE:** *GPUs braucht man dort, wo das Modell läuft, also im Rechenzentrum, nicht beim Nutzer.*

---

## 11. Real problems an AI infra engineer fixes (linked to Day 2)

| Problem | Concept behind it |
|---|---|
| Pod killed with **OOMKilled** | Not enough RAM |
| "CUDA out of memory" | Model + KV cache too big for VRAM |
| "No GPU found" in a container | Driver / container can't access the GPU |
| CUDA version doesn't match driver | Driver (kernel) and CUDA (container) versions must fit |
| Slow responses, CPU at 100% | Too many processes fighting for cores |

---

## 🇩🇪 Vocabulary

| German | English |
|---|---|
| das Betriebssystem | operating system |
| verwalten | to manage |
| verteilen | to distribute |
| der Prozessor / die CPU | processor |
| der Kern | core |
| ausführen (split verb) | to execute |
| der Befehl | instruction / command |
| der Arbeitsspeicher | RAM / memory |
| die Festplatte | disk |
| flüchtig | volatile / temporary |
| dauerhaft | permanent |
| laufend | running |
| der Prozess | process |
| steuern | to control |
| der Zugriff | access |
| der Systemaufruf | system call |
| der Treiber | driver |
| sich teilen | to share |
| daher / deshalb | therefore |
| laden | to load |
| bitten um | to ask for |
| die Gewichte | weights |
| bestehen aus | to consist of |
| die Milliarde | billion (⚠️ German "Billion" = trillion!) |
| sogenannt | so-called |
| die Berechnung | calculation |
| gleichzeitig | at the same time |
| die Rechenarbeit | computing work |
| ermöglichen | to make possible |
| erzeugen | to generate |
| das Rechenzentrum | data center |

**Grammar patterns:**
- *nicht …, sondern …* = not …, but rather … → *Das Modell sucht nicht, sondern es berechnet.*
- Passive: *wird geladen / wird ausgeführt / wird bereitgestellt*

---

## ✍️ Self-check: questions, my answers, and model answers

### 1. What happens step by step when I start Ollama and ask a question?

**Model answer:**
1. **Load:** the OS loads Ollama from the **disk** into **RAM** → it becomes a **process**.
2. **CPU time:** the **scheduler** (in the kernel) gives the process time on a CPU core.
3. **Read the model:** Ollama asks the kernel (**system call**) to read the model file (the weights) from disk into RAM.
4. **To the GPU (if there is one):** the weights are copied into the **GPU's memory (VRAM)** through the **driver**.
5. **Tokens:** the question is cut into **tokens**.
6. **Guessing game:** the model calculates a score for every possible next token (billions of multiplications) and picks the best one, token by token.
7. **Answer:** each token goes back through the kernel to the screen.

**DE:** *Das Programm wird geladen, der Kernel teilt CPU und Speicher zu, das Modell berechnet Token für Token die Antwort.*

---

### 2. Why is a GPU faster than a CPU for AI (but not for everything)? ✅

**My answer:** AI needs huge calculations, and a GPU has thousands of cores that do them in parallel.

**Model answer:** AI needs billions of small, independent calculations, and a GPU's thousands of cores do them at the same time. **But** GPU cores are simple: they're great for the *same* small calculation many times. For complex, step-by-step logic (running the OS, `if/else` code), the CPU is better.

---

### 3. Why does VRAM matter so much?

**What is VRAM?** The GPU's own RAM (V = Video, because GPUs were first made for video and games).
- **RAM** = the CPU's memory, on the motherboard
- **VRAM** = the GPU's memory, on the graphics card right next to the GPU cores

🍕 RAM = the main kitchen counter. VRAM = a small counter right next to the 5,000 helpers.

**Why it matters:**
1. **The model must fit into VRAM.** Too small → "CUDA out of memory" error.
2. **Speed:** for every token, the GPU reads all the weights. Fast VRAM = fast answers.
3. **Cost:** more VRAM = more expensive GPU.

**Model answer:** VRAM is the GPU's own memory. The model's weights must fit into it, and its speed decides how fast tokens are generated.

**DE:** *VRAM ist der eigene Speicher der Grafikkarte. Das Modell muss hineinpassen.*

---

### 4. Can a GPU work without a CPU? Why not? ⚠️

**My answer:** No, because the CPU takes the request from the system calls and passes it further.

**Correction:** "No" is right, but **programs** make system calls to the **kernel** (which runs on the CPU). The CPU doesn't "take requests from system calls".

**Model answer:** No. The GPU has no operating system and can't run programs on its own. The CPU runs the OS and the AI program, and sends the heavy math to the GPU through the driver. The GPU only does the calculations it is given.

🍕 The helpers can chop onions, but the head chef (CPU) tells them what to chop.

---

### 5. Does ChatGPT search or guess? What's the difference? ✅

**My answer:** It guesses, based on the trained model and its patterns.

**Model answer:** It guesses. **Searching** = looking up existing text (like Google or a database). **Guessing** = calculating the most likely next token from the learned weights. The model never looks anything up, which is why it can hallucinate.

---

### 6. Difference between the NVIDIA driver and CUDA? ❌

**My answer:** CUDA is something like a programmable chip.

**Correction:** CUDA is **software**, not a chip. The chip is the **GPU**.

| | What it is | Where it lives | Analogy 🍕 |
|---|---|---|---|
| **GPU** | The hardware chip | Graphics card | The 5,000 helpers |
| **NVIDIA driver** | Software that talks to the GPU hardware | Inside the OS kernel | The translator |
| **CUDA** | Toolkit/library for programmers to send math to the GPU | Used by the AI program (PyTorch, vLLM) | The order form the head chef fills in |

**Flow:** AI program → CUDA → driver → GPU

**Model answer:** The driver lets the operating system talk to the GPU hardware. CUDA is NVIDIA's programming platform that lets applications use the GPU for calculations. CUDA uses the driver.

**DE:** *Der Treiber verbindet das Betriebssystem mit der GPU. CUDA ist eine Programmierplattform, mit der Anwendungen die GPU nutzen können.*

---

### 📊 My score

| Q | Result |
|---|---|
| 1 | Model answer learned |
| 2 | ✅ Correct |
| 3 | 📘 New: learned what VRAM is |
| 4 | ⚠️ Right "no", reason fixed |
| 5 | ✅ Correct |
| 6 | ❌ Fixed: CUDA is software, not a chip |

**New words:** *die Grafikkarte* (graphics card), *hineinpassen* (to fit into), *verbinden* (to connect), *nutzen* (to use)
