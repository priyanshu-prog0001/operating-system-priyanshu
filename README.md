# operating-system-priyanhshu
# Operating Systems Lab — C++ Implementations

Practical file for **CEC301 / CSC301 — Operating Systems** (B.Tech / B.Sc. CSE, Third Year).
All 11 lab programs from the DU OS Lab Manual, re-implemented in **modern C++17**
(`<iostream>`, `<vector>`, `<thread>`, STL algorithms) instead of C.

## Programs

| #  | Program | File |
|----|---------|------|
| 1  | Process creation with `fork()`, `exec()`, `wait()` | [`src/01_fork_exec_wait.cpp`](src/01_fork_exec_wait.cpp) |
| 2  | Thread creation and management with POSIX threads | [`src/02_pthreads_sum.cpp`](src/02_pthreads_sum.cpp) |
| 3  | CPU scheduling — FCFS, SJF, Priority, Round Robin | [`src/03_cpu_scheduling.cpp`](src/03_cpu_scheduling.cpp) |
| 4  | Producer–Consumer using semaphores | [`src/04_producer_consumer.cpp`](src/04_producer_consumer.cpp) |
| 5  | Readers–Writers using semaphores | [`src/05_readers_writers.cpp`](src/05_readers_writers.cpp) |
| 6  | Dining Philosophers using semaphores | [`src/06_dining_philosophers.cpp`](src/06_dining_philosophers.cpp) |
| 7  | Deadlock avoidance — Banker's Algorithm | [`src/07_bankers_algorithm.cpp`](src/07_bankers_algorithm.cpp) |
| 8  | Memory allocation — First / Best / Worst Fit | [`src/08_memory_allocation.cpp`](src/08_memory_allocation.cpp) |
| 9  | Page replacement — FIFO, LRU, Optimal | [`src/09_page_replacement.cpp`](src/09_page_replacement.cpp) |
| 10 | Disk scheduling — FCFS, SSTF, SCAN, C-SCAN | [`src/10_disk_scheduling.cpp`](src/10_disk_scheduling.cpp) |
| 11 | File allocation — Contiguous, Linked, Indexed | [`src/11_file_allocation.cpp`](src/11_file_allocation.cpp) |

`src/sync_common.hpp` holds a small counting `Semaphore` class (mutex + condition
variable) shared by programs 4, 5 and 6.

## Requirements

- A C++17 compiler (`g++` 7+ or `clang++`)
- Linux / macOS / WSL (programs 1 and 2 use POSIX `fork`, `exec` and `pthread`)
- `make` (optional)

## Build

```bash
make            # builds every program into ./bin
make clean      # removes ./bin
```

Or compile a single program by hand:

```bash
g++ -std=c++17 -Wall -Wextra -O2 src/03_cpu_scheduling.cpp -o bin/03_cpu_scheduling -pthread
```

## Run

```bash
./bin/01_fork_exec_wait
./bin/03_cpu_scheduling
# ... and so on
```

Programs 1, 2, 4, 5 and 6 need no input. The others read from standard input.

## Sample inputs and outputs

**Program 3 — Round Robin (quantum = 4)**

```
4
0 5 2
1 3 1
2 8 3
3 6 4
4
4
```
```
PID  AT  BT  CT  TAT  WT
1    0   5   16  16   11
2    1   3   7   6    3
3    2   8   20  18   10
4    3   6   22  19   13
Average Waiting Time    = 9.25
Average Turnaround Time = 14.75
```

**Program 7 — Banker's Algorithm**: available `3 3 2`, 5 processes x 3 resources
→ `SAFE state, safe sequence: P1 P3 P4 P0 P2`; request `1 0 2` by P1 → granted.

**Program 8 — Memory allocation**: blocks `100 500 200 300 600`, processes `212 417 112 426`

| Process | First Fit | Best Fit | Worst Fit |
|---------|-----------|----------|-----------|
| P1 (212) | B2 | B4 | B5 |
| P2 (417) | B5 | B2 | B2 |
| P3 (112) | B2 | B3 | B5 |
| P4 (426) | Not allocated | B5 | Not allocated |

**Program 9 — Page replacement**: reference string `7 0 1 2 0 3 0 4 2 3 0 3`, 3 frames
→ FIFO = 10, LRU = 9, Optimal = 7 page faults.

**Program 10 — Disk scheduling**: requests `98 183 37 122 14 124 65 67`, head = 53, disk size = 200
→ FCFS = 640, SSTF = 236, SCAN = 331, C-SCAN = 382.

## Notes on differences from the lab manual's sample output

While verifying the programs by hand, a few sample outputs printed in the manual
turned out not to match the correct result for the given input. These
implementations produce the verified values:

| Program | Manual sample | This repo (verified) | Reason |
|---------|---------------|----------------------|--------|
| 3 — Round Robin | CT = 17, 8, 22, 19 | CT = 16, 7, 20, 22 | Gantt chart: P1(0-4) P2(4-7) P3(7-11) P4(11-15) P1(15-16) P3(16-20) P4(20-22) |
| 9 — FIFO / LRU | 9 / 8 faults | 10 / 9 faults | Hand-traced on the reference string above (Optimal = 7 matches) |
| 10 — SCAN | 236 | 331 | Head moves up to cylinder 199, then back down to 14: (199−53) + (199−14) |
| 10 — C-SCAN | 269 | 382 | 146 up to 199, 199 jump to 0, then 37 up to 37 |

Other differences from the C originals: containers (`vector`, `queue`) replace fixed
arrays, `std::thread` replaces `pthread_create` where possible (programs 4–6),
Program 8 also prints the leftover free memory, and Program 7 prints the safe
sequence after a granted request.

## Push to GitHub

```bash
cd OS-Lab-CPP
git init
git add .
git commit -m "Add Operating Systems lab programs in C++"
git branch -M main
git remote add origin https://github.com/<your-username>/<repo-name>.git
git push -u origin main
```

Create the empty repository on GitHub first (without a README, since this one already exists).

## Author

Submitted by: _<your name / roll no.>_
