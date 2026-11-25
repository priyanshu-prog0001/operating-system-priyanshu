# operating-system-priyanshu
# Operating Systems Lab — C++ Implementations

Practical file for **CEC301 / CSC301 — Operating Systems** (B.Tech / B.Sc. CSE, Third Year).
All 11 lab programs from the DU OS Lab Manual, re-implemented in **modern C++17**
(`<iostream>`, `<vector>`, `<thread>`, STL algorithms) instead of C.

## Programs



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



## Author

Submitted by: Priyanshu negi / 2472171
