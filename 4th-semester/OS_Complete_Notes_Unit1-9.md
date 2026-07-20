# OPERATING SYSTEMS — COMPLETE NOTES (Unit 1 – Unit 9)
### BSIT — CC-311 | Prepared for Ahmad

---

# UNIT 1 — COMPUTER FOUNDATIONS

## 1.1 Computer Architecture Basics
- **CPU** = brain, does processing/calculations
- **RAM** = fast, temporary (volatile) memory — erased on shutdown
- **Storage (Disk/SSD)** = slow, permanent memory
- **I/O Devices** = keyboard, mouse, screen, printer
- **Flow:** Storage → RAM → CPU → Output
- Why RAM is needed: Storage is too slow for CPU to work directly from; RAM is the fast middle layer.

## 1.2 Kernel, User Mode vs Kernel Mode
- **Kernel** = core of the OS, directly controls hardware
- **User Mode** = restricted; normal apps run here (Chrome, Word)
- **Kernel Mode** = full access; only OS core runs here
- Why two modes? **Safety/Protection** — apps can't directly touch hardware, must request via kernel.
- Mode switch happens via **System Call (trap)**.

## 1.3 Interrupts
- **Interrupt** = signal telling CPU to pause current task and handle something urgent
- **Types:**
  - Hardware Interrupt — from physical devices (keyboard press)
  - Software Interrupt (Trap) — program-triggered (system call)
  - Exception — error (divide by zero, invalid memory access)
- **Steps:** CPU saves current state → jumps to ISR (Interrupt Service Routine) → handles it → restores state → resumes.

## 1.4 System Boot Process
- **Order:** BIOS/UEFI (hardware check) → POST (Power-On Self-Test: tests RAM/keyboard/disk) → Bootloader (loads Kernel into RAM, e.g. GRUB in Linux) → Kernel Initialization (starts services, shows login/desktop)
- Bootloader's ONLY job: find and load the kernel, then it disappears.

---

# UNIT 2 — OPERATING SYSTEM BASICS

## 2.1 What is an OS? Goals
- **OS** = software between user/applications and hardware; manages everything
- **2 Goals:** Convenience (easy to use) + Efficiency (best resource usage)
- Acts as: Resource Manager + Abstraction Layer (extended machine)

## 2.2 Multiprogramming & Multitasking ⭐
- **Problem:** CPU is fast, I/O devices are slow → CPU sits idle waiting for I/O
- **Multiprogramming** = keep multiple programs in RAM so CPU switches to another program when one waits for I/O → maximizes CPU + I/O utilization
- **Multitasking** = fast switching (time slices) so it FEELS like multiple programs run simultaneously (user-experience focus)
- **Difference:** Multiprogramming → CPU utilization focus; Multitasking → user experience/fast response focus
- **Exam answer structure:** (1) Define multiprogramming (2) Problem it solves (CPU idle during I/O wait) (3) How: OS switches CPU to next ready program (4) Conclusion: maximized CPU+I/O utilization

## 2.3 Types of Operating Systems
| Type | Meaning |
|---|---|
| Batch OS | Jobs collected, run one by one, no interaction |
| Multiprogrammed OS | Multiple programs in RAM, CPU never idle |
| Time-Sharing OS | CPU time divided into slices among many users |
| Multiprocessor OS | One computer, 2+ CPUs together |
| Distributed OS | Multiple separate computers work as one system |
| Real-Time OS (RTOS) | Guaranteed response within strict time limit (not just "fast") |
| Mobile OS | For phones/tablets (Android, iOS) |

## 2.4 Operating System Services
Program Execution, I/O Operations, File System Manipulation, Communication, Error Detection, Resource Allocation, Accounting (tracks usage/CPU time history), Protection & Security.

## 2.5 System Calls ⭐
- **System Call** = way for User Mode program to request Kernel service
- **Mechanism:** Program calls system call → TRAP (mode switch User→Kernel) → Kernel executes → switches back → program resumes
- **Categories & examples:**
  - Process Control: `fork()`, `exit()`, `wait()`
  - File Management: `open()`, `read()`, `write()`, `close()`
  - Device Management: `ioctl()`
  - Information Maintenance: `getpid()`, `time()`
  - Communication: `pipe()`, `send()`, `receive()`

## 2.6 Operating System Structure
| Structure | Meaning |
|---|---|
| Monolithic | Everything bundled in one kernel — fast, less safe |
| Layered | OS divided into layers, each talks only to layer below |
| Microkernel | Minimal kernel; rest (drivers, file system) runs as User Mode services — safe, slower |
| Modular | Core + loadable/pluggable modules (Linux uses this — hybrid Monolithic+Modular) |

---

# UNIT 3 — PROCESSES & THREADS

## 3.1 Process Concept & 5-State Process Model ⭐
- **Program** = passive file on disk. **Process** = program actively running (in execution).
- **5 States:** New → Ready → Running → Waiting (Blocked) → Terminated
- **Transitions:**
  - New → Ready: admitted
  - Ready → Running: dispatched by scheduler
  - Running → Ready: interrupt / time slice over
  - Running → Waiting: needs I/O or event
  - Waiting → Ready: I/O/event completed
  - Running → Terminated: process finishes
- **Diagram (memorize):**
```
   New --admitted--> Ready <---(I/O complete)--- Waiting
                       |  ^                          ^
              dispatch |  | interrupt/time-slice-over |
                       v  |                           |
                    Running -----(needs I/O)-----------+
                       |
                 (finishes)
                       v
                  Terminated
```

## 3.2 Process Control Block (PCB) ⭐
- **PCB** = data structure/"identity card" OS keeps for every process
- **Fields:** Process ID (PID), Process State, Program Counter (PC — next instruction), CPU Registers (saved values), CPU Scheduling Info (priority), Memory Management Info (base/limit, page tables), I/O Status Info (open files, devices), Accounting Info (CPU time used)
- PCB is what allows OS to pause and resume processes correctly.

## 3.3 Process Scheduling — 3 Schedulers ⭐
| Scheduler | Frequency | Controls |
|---|---|---|
| Long-term (Job Scheduler) | Rare | New → Ready (which programs enter system; controls degree of multiprogramming) |
| Short-term (CPU Scheduler) | Very frequent | Ready → Running (picks next process for CPU) |
| Medium-term | Occasional | Swaps processes out/in RAM (memory overload management) |
- **Memory trick:** Long-term = LONG gap between runs = controls ENTRY. Short-term = SHORT gap = controls CPU (busiest scheduler).

## 3.4 Context Switching
- **Context Switch** = OS saving current running process's state (into its PCB) and loading another process's state (from its PCB)
- **Steps:** Save Process A's state into A's PCB → pick next process (short-term scheduler) → load Process B's state from B's PCB → B resumes
- **Pure overhead** — no useful work done during the switch itself.
- Triggers: time slice ends, higher priority process arrives, I/O wait needed, interrupt occurs.

## 3.5 Zombie & Orphan Process ⭐
- **Zombie process** = child has finished (terminated), but its PCB entry stays in process table because parent hasn't called `wait()` to read exit status yet. Removed once parent calls `wait()`.
- **Orphan process** = parent terminates BEFORE child finishes; child is still running, parentless — gets adopted by `init`/`systemd` (PID 1).
- Zombie = dead child + lazy parent. Orphan = dead parent + alive child.

## 3.6 Threads ⭐
- **Thread** = lightweight execution unit inside a process; shares process's memory/files/resources
- Each thread has own: Program Counter, CPU Registers, Stack
- **Advantages:** Faster creation, resource sharing, responsiveness, better CPU utilization, economical
- **Disadvantages:** Synchronization issues (race conditions), harder debugging, one thread crash can affect whole process, complex programming

## 3.7 Multithreading Models
- **User-level thread** = managed by program itself; **Kernel-level thread** = managed by OS

| Model | Mapping | Note |
|---|---|---|
| Many-to-One | Many user threads → 1 kernel thread | One blocking call freezes ALL threads |
| One-to-One | 1 user thread → 1 kernel thread | Good parallelism, more OS overhead (Linux uses this via `pthread_create()`) |
| Many-to-Many | Many user threads → fewer/equal kernel threads | Balanced/flexible |

## 3.8 Threading Issues
- fork()/exec() semantics (duplicate all threads or just the calling one?)
- Thread cancellation: Asynchronous (kill immediately) vs Deferred (checks periodically, safer)
- Signal handling (which thread responds?)
- Thread pools (reuse ready threads instead of creating fresh each time — used by web servers)
- Thread-specific data (private data per thread despite shared memory)

---

# UNIT 4 — CPU SCHEDULING

## 4.1 Scheduling Concepts & Criteria — CORE FORMULAS ⭐

**CPU Scheduling** = deciding WHICH process in the Ready queue gets the CPU next, and for how long.
```
Turnaround Time (TAT) = Completion Time (CT) − Arrival Time (AT)
Waiting Time (WT)     = Turnaround Time (TAT) − Burst Time (BT)
Average Waiting Time (AWT)   = (Sum of all WT) / (number of processes)
Average Turnaround Time      = (Sum of all TAT) / (number of processes)
```
- **Criteria:** Maximize CPU Utilization & Throughput; Minimize Turnaround Time, Waiting Time, Response Time.

## 4.2 FCFS (First Come First Serve) ⭐
- Non-preemptive; run strictly in **Arrival Time** order
- Weakness: **Convoy Effect** (short jobs stuck behind long ones)
- Method: order by AT → draw Gantt chart (show idle gaps if CPU free before next arrival) → table (AT,BT,CT,TAT,WT) → AWT

## 4.3 SJF & SRTF ⭐
- **SJF (non-preemptive):** pick shortest Burst Time among ARRIVED processes; once started, NEVER interrupted even if shorter job arrives later
- **SRTF (preemptive) — Shortest Remaining Time First:** at every new arrival, compare new process's BT vs running process's REMAINING time; if new BT is shorter → preempt
- SRTF generally gives lower/equal AWT than SJF non-preemptive.
- In SRTF, same process can appear multiple times in Gantt chart (interrupted & resumed).

## 4.4 Priority Scheduling ⭐
- Pick process with HIGHEST priority (convention: usually **smaller number = higher priority** — always check question wording!)
- **Non-preemptive:** running process completes fully regardless of new higher-priority arrivals
- **Preemptive:** switches immediately if new arrival has higher priority
- **Problem: Starvation** (low priority process never runs) → **Solved by Aging** (gradually increase priority of waiting process)

## 4.5 Round Robin ⭐⭐⭐ (appears in EVERY paper)
- Each process gets fixed **Time Quantum (TQ)**; if not finished, goes to BACK of Ready Queue
- **GOLDEN RULE:** When a process's TQ ends, any NEWLY ARRIVED processes (during that run) join the queue BEFORE the just-finished (but not fully done) process gets re-added to the back.
- Fair, preemptive, avoids starvation
- TQ too large → behaves like FCFS. TQ too small → excessive context-switch overhead.

## 4.6 Wrap-up — Comparison Table
| Algorithm | Preemptive? | Rule | Weakness |
|---|---|---|---|
| FCFS | No | Arrival Time | Convoy Effect |
| SJF | No | Shortest Burst Time | Can starve long jobs |
| SRTF | Yes | Shortest Remaining Time | Frequent switching overhead |
| Priority (NP) | No | Priority number | Starvation (fix: Aging) |
| Priority (P) | Yes | Priority (rechecked) | Starvation + overhead |
| Round Robin | Yes | Time Quantum | Wrong TQ size issues |

**Universal Method:** identify algorithm → check special rules (priority convention/TQ) → build Gantt chart (track Ready Queue) → make CT/TAT/WT table → calculate AWT.

## 4.7 Multilevel Queue Scheduling
- Multiple SEPARATE queues by process type (System / Interactive / Batch), each can use a DIFFERENT algorithm
- Processes PERMANENTLY assigned to one queue — NO movement between queues.

**How do queues get CPU time relative to each other?**
Usually via **strict priority** (Queue 1 always runs before Queue 2, which runs before Queue 3) OR **time-slicing between queues** (e.g., Queue 1 gets 50% of CPU time, Queue 2 gets 30%, Queue 3 gets 20%).

## 4.8 Multilevel Feedback Queue (MLFQ)
- Same as Multilevel Queue BUT processes CAN move between queues
- **Demotion:** process uses too much CPU without finishing → moved to lower priority queue (larger TQ)
- **Promotion (Aging):** process waited too long in low queue → moved back up to prevent starvation
- Most flexible/adaptive scheduling method.

---

# UNIT 5 — PROCESS SYNCHRONIZATION

## 5.1 Critical Section Problem ⭐
- **Critical Section** = part of code accessing SHARED data
- Code structure: Entry Section → Critical Section → Exit Section → Remainder Section
- **3 Requirements for good solution:**
  1. **Mutual Exclusion** — only one process in CS at a time
  2. **Progress** — decision of who enters next can't be postponed forever
  3. **Bounded Waiting** — limit on how many times others can enter before a waiting process gets its turn

## 5.2 Race Condition ⭐
- **Race Condition** = final result depends on unpredictable TIMING of concurrent access to shared data
- Caused by operations (e.g. `balance = balance + 50`) being split into steps: READ → MODIFY → WRITE, which can be interleaved badly
- **Exam method:** draw a timeline table showing wrong final result vs correct expected result

Time | Process A steps | Process B steps | shared variable value

- Solution: protect with Mutex/Semaphore (make it atomic)

## 5.3 Mutex
- **Mutex** = simple lock ensuring only ONE process in Critical Section at a time
- Operations: `lock()` (acquire — wait if busy), `unlock()` (release)
- Forgetting to unlock → other processes wait forever (deadlock risk)

## 5.4 Semaphores ⭐⭐⭐
- **Semaphore** = integer variable, accessed ONLY via atomic `wait()` (P) and `signal()` (V)
```
wait(S):    while (S <= 0) ; // busy wait
            S = S - 1;
signal(S):  S = S + 1;
```
- **Binary Semaphore** (0 or 1) = mutual exclusion, like mutex
- **Counting Semaphore** (any non-negative value) = manages a POOL of multiple identical resource instances
- Both wait()/signal() MUST be atomic (else race condition on semaphore itself)

## 5.5 Monitors
- High-level construct bundling shared data + procedures + AUTOMATIC mutual exclusion (no manual wait/signal needed)
- Only ONE process executes any procedure inside a monitor at a time
- Real example: Java's `synchronized` keyword

## 5.6 Producer-Consumer (Bounded Buffer) ⭐⭐⭐
- Producer adds items to shared buffer (N slots); Consumer removes items
- **3 Semaphores:** `empty` (init = N, counts empty slots — Producer waits on this), `full` (init = 0, counts filled slots — Consumer waits on this), `mutex` (init = 1, protects actual buffer access)
```
PRODUCER:                      CONSUMER:
wait(empty)                    wait(full)
wait(mutex)                    wait(mutex)
   add item to buffer             remove item from buffer
signal(mutex)                  signal(mutex)
signal(full)                   signal(empty)
```
- **CRITICAL RULE:** `wait(empty)`/`wait(full)` MUST happen BEFORE `wait(mutex)` — reverse order causes deadlock.

## 5.7 Reader-Writer Problem ⭐⭐⭐
- Multiple READERS can read simultaneously (no harm). Only ONE WRITER at a time, and no readers while writing.
- Variables: `readCount` (active readers count), `mutex` (protects readCount), `wrt` (protects actual data)
```
WRITER:                          READER:
wait(wrt)                        wait(mutex)
   <writing>                     readCount++
signal(wrt)                      if (readCount==1) wait(wrt)   // FIRST reader locks data
                                  signal(mutex)
                                     <reading>
                                  wait(mutex)
                                  readCount--
                                  if (readCount==0) signal(wrt) // LAST reader unlocks data
                                  signal(mutex)
```
- **Key trick:** FIRST reader locks `wrt` (blocks writers); LAST reader unlocks `wrt`.
- Line-tracing skill: if Writer holds `wrt`, 1st Reader blocks at `wait(wrt)`; 2nd Reader blocks at `wait(mutex)` (since 1st reader still holds mutex, stuck before its `signal(mutex)`).

---

# UNIT 6 — DEADLOCKS

## 6.1 Deadlock Conditions ⭐
- **Deadlock** = processes stuck waiting forever in circular dependency
- **4 Necessary conditions (ALL must hold simultaneously):**
  1. Mutual Exclusion — resource non-shareable
  2. Hold and Wait — holding one resource while waiting for another
  3. No Preemption — resource can't be forcibly taken
  4. Circular Wait — closed loop of processes each waiting on the next
- Resource Allocation Graph: cycle among processes/resources = deadlock (single-instance case)

## 6.2 Deadlock Prevention ⭐
- Break just ONE of the 4 conditions:
  - Break Mutual Exclusion → make resources shareable (not always practical)
  - Break Hold and Wait → request ALL resources upfront
  - Break No Preemption → allow forced resource takeback
  - Break Circular Wait → impose strict resource ORDERING (request only in increasing order) — most practical/common

## 6.3 Deadlock Avoidance
- OS grants request ONLY if resulting state is a **Safe State**
- **Safe State** = at least one order exists where all processes can finish without getting stuck
- **Unsafe State** = no safe order exists (deadlock POSSIBLE, not guaranteed)
- Requires processes to declare **Maximum resource need** upfront.

## 6.4 Banker's Algorithm ⭐⭐⭐ (guaranteed every paper)
- Data: **Max**, **Allocation**, **Available**, **Need = Max − Allocation**
- **Safety Algorithm steps:**
  1. Find process where Need ≤ Available
  2. "Run" it: Available = Available + its Allocation
  3. Mark finished, add to Safe Sequence
  4. Repeat for remaining processes with updated Available
  5. All finish → Safe State + Safe Sequence found. Stuck (no process qualifies) → Unsafe State.
- **Request check:** (1) Request ≤ Need? (2) Request ≤ Available? (3) If both yes, pretend-grant and re-run safety algorithm on hypothetical state — if safe sequence still exists, grant it for real.
- Always show Need table explicitly first.

## 6.5 Deadlock Detection & Recovery
- **Detection** = periodically check CURRENT state (Allocation + Request only, no Max needed) for a cycle/stuck pattern
- **Recovery:**
  - Process Termination: abort all at once (simple, wasteful) OR one at a time (efficient, check after each)
  - Resource Preemption: select victim, ROLLBACK the victim to safe point, avoid starvation (don't always pick same victim)

---

# UNIT 7 — MEMORY MANAGEMENT

## 7.1 Logical vs Physical Address Space ⭐
- **Logical (Virtual) Address** = program's own view of memory (generated by CPU, starts conceptually from 0)
- **Physical Address** = actual RAM location
- **MMU (Memory Management Unit)** = hardware translating Logical → Physical (often Logical + Base Register = Physical)
- Benefits: Protection (can't touch others' memory) + Flexibility (OS can relocate freely)

## 7.2 Swapping
- **Swap-out** = RAM → Disk (free memory). **Swap-in** = Disk → RAM
- Managed by Medium-term Scheduler; has real time cost (disk is slower than RAM)

## 7.3 Paging ⭐⭐⭐ — CORE FORMULAS
- **Page** (logical, fixed size) / **Frame** (physical, same size) / **Page Table** maps Page# → Frame#
- Logical Address = [Page Number (p) | Offset (d)]
```
Offset bits (d)        = log2(Page Size)
Logical Address bits   = log2(Logical Address Space Size)
Page Number bits (p)   = Logical Address bits − Offset bits
Number of Pages        = 2^p  =  Logical Address Space / Page Size

Physical Address bits  = log2(Physical Memory Size)
Frame Number bits (f)  = Physical Address bits − Offset bits
Number of Frames       = 2^f  =  Physical Memory / Page Size

Page Table Entry size  = f bits (round UP to nearest byte)
Page Table Size        = Number of Pages × PTE size (in bytes)
```
- **Conversions:** 1 KB=2^10, 1 MB=2^20, 1 GB=2^30, 4 KB=2^12
- Offset stays SAME after translation (only Page# → Frame# changes)
- Common mistake: round bits UP to bytes (e.g., 13 bits → 2 bytes, 23 bits → 3 bytes)

## 7.4 Segmentation ⭐
- **Segment** = VARIABLE-sized logical unit (function, array, stack) — based on program's logical structure
- **Segment Table**: Segment# → Base + Limit
- Logical Address = [Segment Number (s) | Offset (d)]
- **Translation:** Check Offset < Limit (bounds check!) → Physical Address = Base + Offset. If Offset ≥ Limit → illegal access ERROR.
- **Paging vs Segmentation:**
| | Paging | Segmentation |
|---|---|---|
| Block size | Fixed | Variable |
| Basis | Mechanical | Logical program structure |
| Table | Page Table (Page→Frame) | Segment Table (Base+Limit) |
| Fragmentation | Internal | External |

## 7.5 TLB & Effective Access Time ⭐
- **TLB** = fast cache storing recent Page→Frame translations
- **TLB Hit** = found in TLB (1 extra memory access for data). **TLB Miss** = not found (2 extra accesses: page table + data)
```
Time (Hit)  = TLB access time + Memory access time
Time (Miss) = TLB access time + Memory access time (page table) + Memory access time (data)
EAT = (Hit Ratio × Time_Hit) + (Miss Ratio × Time_Miss)      where Miss Ratio = 1 − Hit Ratio
```

## 7.6 Virtual Memory & Demand Paging
- **Virtual Memory** = run programs larger than RAM by keeping only actively-needed pages in RAM
- **Demand Paging** = load a page only when actually accessed (lazy loading)
- **Valid-Invalid bit** in Page Table: Valid = page in RAM; Invalid = triggers **Page Fault** (OS fetches page from disk into free frame, updates table, restarts instruction)
- Page Fault is NORMAL, not an error — problem only if excessive (Thrashing)

## 7.7 Page Fault & Page Replacement Algorithms ⭐⭐⭐
- **FIFO** = remove page in RAM the LONGEST (oldest arrival) — track arrival queue order carefully
- **Optimal** = remove page NOT needed for longest time in FUTURE (look ahead at entire remaining reference string) — theoretical best, needs future knowledge
- **LRU (Least Recently Used)** = remove page unused for longest time in the PAST (look backward at history)
- Method: table with columns Reference | Frame contents | Fault/Hit; count total faults
- If page already in a frame → **Hit** (no replacement)

## 7.8 Thrashing
- **Thrashing** = system spends MORE time on page faults than actual work → CPU utilization crashes
- Cause: too many processes, too little RAM (frames) per process
- Graph: CPU Utilization rises then CRASHES past a certain Degree of Multiprogramming
- **Fix (counterintuitive):** REDUCE degree of multiprogramming (fewer processes) OR use Working Set Model / Page Fault Frequency monitoring

## 7.9 Memory Mapping
- **mmap()** = maps a file directly into a process's virtual address space; accessed like normal memory (pointers/arrays) instead of read()/write() calls
- Uses Demand Paging underneath (only accessed parts loaded)

---

# UNIT 8 — FILE SYSTEMS

## 8.1 Files & Directories
- **File attributes:** Name, Identifier, Type, Location, Size, Protection, Time/Date/User ID
- **Directory** = special file holding pointers/info about other files (not actual content)
- **Structures:** Single-level, Two-level (per user), Tree-structured (folders in folders — modern OS), Acyclic-graph (allows sharing/links, no cycles)

## 8.2 File Allocation Methods
| Method | How | Pro | Con |
|---|---|---|---|
| Contiguous | Blocks stored consecutively | Fast access | External fragmentation, hard to grow |
| Linked | Each block points to next | No fragmentation, easy to grow | Slow direct access (sequential only), pointer corruption risk |
| Indexed | Separate index block lists all data blocks | Fast direct access | Extra space for index block (Linux inodes use this idea) |

## 8.3 Free Space Management
| Method | Idea |
|---|---|
| Bit Vector (Bitmap) | 1 bit per block: free/allocated |
| Linked List | Free blocks chained together |
| Grouping | First free block lists several other free blocks |
| Counting | Start address + count of consecutive free blocks |

## 8.4 Disk Scheduling
- **FCFS** = arrival order (simple, inefficient, zigzag movement)
- **SSTF** (Shortest Seek Time First) = closest request next — efficient short-term, but can STARVE far requests
- **SCAN** ("elevator algorithm") = moves one direction servicing requests, reverses at the end, services on the way back too
- **C-SCAN** = moves one direction, jumps back to start WITHOUT servicing on return trip, more uniform wait time
- Worked method: list step-by-step track visited + total head movement (sum of absolute differences)

## 8.5 Swap Space Management
- **Swap Space** = dedicated disk area holding pages/processes swapped out of RAM
- **Separate Swap Partition** = faster, dedicated, bypasses file system overhead
- **Swap File** (inside file system) = more flexible/resizable, slightly slower
- Linux commands: `swapon`/`swapoff`, `free -h` (shows swap usage)

---

# UNIT 9 — PROTECTION, SECURITY & VIRTUALIZATION

## 9.1 Protection vs Security
- **Protection** = internal MECHANISM controlling who can access what (file permissions, ACLs, authentication) — a tool
- **Security** = broader GOAL of defending against external threats (hackers, malware, unauthorized access) — mechanisms can be misconfigured/bypassed, causing breaches even with protection in place
- Relevant to Ethical Hacking: pen-testers look for ways protection mechanisms are bypassed/misconfigured.

## 9.2 Virtual Machines
- **VM** = simulated computer running its own complete OS inside a real machine
- **Hypervisor** = creates/manages VMs, allocates real hardware resources
  - **Type 1 (Bare-metal)** = runs directly on hardware, no host OS (enterprise servers, e.g. VMware ESXi)
  - **Type 2 (Hosted)** = runs on top of existing host OS (VMware Workstation/VirtualBox — what you use for Ubuntu labs)
- Useful for Ethical Hacking: isolated environment to safely test tools/malware without risking the real host system.

---

# QUICK-REFERENCE FORMULA SHEET

**Scheduling:**
```
TAT = CT − AT
WT  = TAT − BT
AWT = ΣWT / n
```

**Memory / Paging:**
```
Offset bits (d)      = log2(Page Size)
Page Number bits (p) = log2(Logical Address Space) − d
Frame Number bits (f)= log2(Physical Memory) − d
Number of Pages      = 2^p        Number of Frames = 2^f
PTE size             = f bits → round up to bytes
```

**TLB / Effective Access Time:**
```
EAT = HitRatio×(TLB+Mem) + (1−HitRatio)×(TLB+Mem+Mem)
```

**Deadlock (Banker's):**
```
Need = Max − Allocation
Process runnable if Need ≤ Available
After running: Available += Allocation(that process)
```

**Semaphore basics:**
```
wait(S): while(S<=0); S=S-1;
signal(S): S=S+1;
```

---
*End of Notes — Units 1 to 9 (Theory Complete). Linux Practical Units 10-13 to follow separately.*
