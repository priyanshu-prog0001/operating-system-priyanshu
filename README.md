<div align="center">

# Operating Systems Lab — C++

**CEC301 / CSC301 · Practical File · B.Tech / B.Sc. (CSE), Third Year**

Eleven classic OS lab programs, implemented in modern **C++17**.


</div>

---

## Table of Contents

- [About](#about)
- [Project Structure](#project-structure)
- [Getting Started](#getting-started)
- [Programs at a Glance](#programs-at-a-glance)
- [Program Details](#program-details)
- [Corrections to the Lab Manual](#corrections-to-the-lab-manual)
- [Differences from the C Versions](#differences-from-the-c-versions)
- [Troubleshooting](#troubleshooting)
- [Publishing to GitHub](#publishing-to-github)
- [Author](#author)

---

## About

This repository contains the programs from the **Delhi University Operating Systems Lab Manual**,
re-written in C++ using the STL (`vector`, `queue`, `algorithm`) and `std::thread`.
The topics cover the whole syllabus:

| Area | Topics |
|------|--------|
| **Processes & threads** | `fork` / `exec` / `wait`, POSIX threads |
| **CPU scheduling** | FCFS, SJF, Priority, Round Robin |
| **Synchronization** | Producer–Consumer, Readers–Writers, Dining Philosophers |
| **Deadlocks** | Banker's Algorithm |
| **Memory management** | First / Best / Worst Fit, FIFO / LRU / Optimal page replacement |
| **Storage** | Disk scheduling, file allocation methods |

Every program is self-contained, compiles with `-Wall -Wextra` without warnings, and was
tested against the sample inputs from the manual.

---

## Project Structure

```
OS-Lab-CPP/
├── src/
│   ├── 01_fork_exec_wait.cpp        # Process creation
│   ├── 02_pthreads_sum.cpp          # Multithreading
│   ├── 03_cpu_scheduling.cpp        # FCFS, SJF, Priority, RR
│   ├── 04_producer_consumer.cpp     # Bounded buffer
│   ├── 05_readers_writers.cpp       # Readers-preference solution
│   ├── 06_dining_philosophers.cpp   # Deadlock-free dining
│   ├── 07_bankers_algorithm.cpp     # Safety + resource-request algorithm
│   ├── 08_memory_allocation.cpp     # First / Best / Worst Fit
│   ├── 09_page_replacement.cpp      # FIFO, LRU, Optimal
│   ├── 10_disk_scheduling.cpp       # FCFS, SSTF, SCAN, C-SCAN
│   ├── 11_file_allocation.cpp       # Contiguous, Linked, Indexed
│   └── sync_common.hpp              # Reusable counting Semaphore class
├── Makefile
├── .gitignore
└── README.md
```

---

## Getting Started

### Prerequisites

- A C++17 compiler: `g++` 7+ or `clang++`
- Linux, macOS or Windows with WSL (programs 1 and 2 use POSIX APIs)
- `make` (optional, but recommended)

### Build

```bash
git clone https://github.com/<your-username>/<repo-name>.git
cd <repo-name>

make          # compile all programs into ./bin
make clean    # delete ./bin
```

Compile a single program manually:

```bash
mkdir -p bin
g++ -std=c++17 -Wall -Wextra -O2 src/03_cpu_scheduling.cpp -o bin/03_cpu_scheduling -pthread
```

### Run

```bash
./bin/01_fork_exec_wait
./bin/07_bankers_algorithm
```

Programs **1, 2, 4, 5 and 6** need no input. Programs **3, 7, 8, 9, 10 and 11** prompt for input,
which you can also pipe in:

```bash
printf '12\n7 0 1 2 0 3 0 4 2 3 0 3\n3\n' | ./bin/09_page_replacement
```

---

## Programs at a Glance

| #  | Program | Key concept | Input |
|----|---------|-------------|:-----:|
| 1  | [Process creation](src/01_fork_exec_wait.cpp) | `fork()`, `exec()`, `wait()` | – |
| 2  | [POSIX threads](src/02_pthreads_sum.cpp) | `pthread_create` / `pthread_join` | – |
| 3  | [CPU scheduling](src/03_cpu_scheduling.cpp) | Waiting & turnaround time | ✔ |
| 4  | [Producer–Consumer](src/04_producer_consumer.cpp) | Counting + binary semaphores | – |
| 5  | [Readers–Writers](src/05_readers_writers.cpp) | Shared vs exclusive access | – |
| 6  | [Dining Philosophers](src/06_dining_philosophers.cpp) | Circular-wait avoidance | – |
| 7  | [Banker's Algorithm](src/07_bankers_algorithm.cpp) | Safe state, deadlock avoidance | ✔ |
| 8  | [Memory allocation](src/08_memory_allocation.cpp) | Fragmentation | ✔ |
| 9  | [Page replacement](src/09_page_replacement.cpp) | Page faults, Belady's anomaly | ✔ |
| 10 | [Disk scheduling](src/10_disk_scheduling.cpp) | Total head movement | ✔ |
| 11 | [File allocation](src/11_file_allocation.cpp) | Block allocation tables | ✔ |

---

## Program Details

Outputs below were produced by the programs in this repo. Where output depends on thread
timing or process IDs (programs 1, 2, 5, 6), your run will differ in ordering or numbers.

### 1 · Process Creation — `fork()`, `exec()`, `wait()`

The parent forks a child; the child replaces itself with `ls -l` via `execlp`; the parent
blocks in `wait()` and reports the child's exit status (preventing a zombie process).

```
Parent process: PID = 181
Parent waiting for child (PID = 183) to finish...
Child process: PID = 183, Parent PID = 181
Child is about to exec 'ls -l'
...directory listing...
Child exited with status 0
Parent process resumes and exits.
```

### 2 · POSIX Threads

Four threads each sum a quarter of `0 … 999 999`; `main` joins them and adds the partial sums.

```
Thread 11552: sum[250000..500000) = 93749875000
Thread 18848: sum[500000..750000) = 156249875000
Thread 4256: sum[0..250000) = 31249875000
Thread 26144: sum[750000..1000000) = 218749875000
Total sum (0 to 999999) computed using 4 threads = 499999500000
```

### 3 · CPU Scheduling

Menu-driven: FCFS, SJF (non-preemptive), Priority (non-preemptive, lower number = higher
priority) and Round Robin.
`TAT = CT − AT`, `WT = TAT − BT`.

**Input** (arrival time, burst time, priority per process):

```
4
0 5 2
1 3 1
2 8 3
3 6 4
```

| Algorithm | Avg. waiting time | Avg. turnaround time |
|-----------|:-----------------:|:--------------------:|
| FCFS | 5.75 | 11.25 |
| SJF | **5.25** | **10.75** |
| Priority | 5.75 | 11.25 |
| Round Robin (q = 4) | 9.25 | 14.75 |

SJF gives the lowest average waiting time, as theory predicts. Round Robin trades a higher
average for fairness and responsiveness.

### 4 · Producer–Consumer

A producer and a consumer share a 5-slot circular buffer, synchronised with `empty`, `full`
and `mutex` semaphores.

```
Producer produced item 1 at slot 0
Producer produced item 2 at slot 1
		Consumer consumed item 1 from slot 0
...
All items produced and consumed.
```

### 5 · Readers–Writers (first problem)

Multiple readers can read together; a writer needs exclusive access. The first reader locks
the resource and the last reader releases it. This variant can starve writers.

```
Writer 1: wrote shared_data = 1
Reader 2: read shared_data = 1
Reader 3: read shared_data = 1
...
Writer 2: wrote shared_data = 2
```

### 6 · Dining Philosophers

Five philosophers, five forks. A `room` semaphore initialised to `N − 1` lets at most four
philosophers pick up forks at once, which breaks the circular-wait condition.

```
Philosopher 0 is thinking
Philosopher 0 is eating (fork 0 and fork 1)
Philosopher 0 finished eating and put down forks
...
```

### 7 · Banker's Algorithm

Runs the **safety algorithm** on the initial state, then the **resource-request algorithm**
for one process (tentative allocation, safety check, rollback if unsafe).

```
Enter available resources of each type (3 types): 3 3 2
Maximum matrix:    7 5 3 | 3 2 2 | 9 0 2 | 2 2 2 | 4 3 3
Allocation matrix: 0 1 0 | 2 0 0 | 3 0 2 | 2 1 1 | 0 0 2

System is in a SAFE state.
Safe sequence: P1 P3 P4 P0 P2
Enter process number requesting resources: 1
Enter request vector (3 values): 1 0 2
Request can be granted safely.
```

### 8 · Contiguous Memory Allocation

Blocks `100 500 200 300 600`, processes `212 417 112 426`:

| Process | First Fit | Best Fit | Worst Fit |
|---------|:---------:|:--------:|:---------:|
| P1 (212) | B2 | B4 | B5 |
| P2 (417) | B5 | B2 | B2 |
| P3 (112) | B2 | B3 | B5 |
| P4 (426) | ✘ | B5 | ✘ |

Best Fit is the only strategy that places all four processes for this input.

### 9 · Page Replacement

Reference string `7 0 1 2 0 3 0 4 2 3 0 3` with 3 frames:

| Algorithm | Page faults |
|-----------|:-----------:|
| FIFO | 10 |
| LRU | 9 |
| Optimal | **7** |

### 10 · Disk Scheduling

Requests `98 183 37 122 14 124 65 67`, head at `53`, disk size `200`.
SCAN and C-SCAN move toward the higher cylinders first.

| Algorithm | Total head movement |
|-----------|:-------------------:|
| FCFS | 640 |
| SSTF | **236** |
| SCAN | 331 |
| C-SCAN | 382 |

### 11 · File Allocation Methods

Menu-driven simulation on a 30-block disk.

```
--- Indexed Allocation ---
File 1 - Index block: 0, Data blocks: 1 2 3 4
File 2 - Index block: 5, Data blocks: 6 7 8

--- Linked Allocation ---
File 1 allocated blocks: 0 -> 1 -> 2 -> 3 -> NULL
```

Contiguous allocation rejects a file whose requested blocks overlap an existing file or run
past the end of the disk.

---

## Corrections to the Lab Manual

While verifying the programs by hand, four sample outputs in the manual were found to be
inconsistent with the inputs shown. This repo prints the verified values.

| Program | Manual sample | Verified result | Working |
|---------|---------------|-----------------|---------|
| 3 · Round Robin (q = 4) | CT = 17, 8, 22, 19 | CT = **16, 7, 20, 22** | P1(0–4) P2(4–7) P3(7–11) P4(11–15) P1(15–16) P3(16–20) P4(20–22) |
| 9 · FIFO | 9 faults | **10** | Traced by hand on the reference string above |
| 9 · LRU | 8 faults | **9** | Traced by hand on the reference string above |
| 10 · SCAN | 236 | **331** | (199 − 53) + (199 − 14) = 146 + 185 |
| 10 · C-SCAN | 269 | **382** | 146 + 199 (jump to 0) + 37 |


## Author

**Name:**Priyasnhu Negi
**Roll No.:** 2472171
**Course:** CEC301  — Operating Systems
**Institution:** Doon university
