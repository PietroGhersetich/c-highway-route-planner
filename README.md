# Optimal Highway Route Planner in C

This repository implements a **time and space efficient** route planning engine in pure C, built from scratch using custom **Binary Search Trees (BST)**, **Max-Heaps**, and **Directed Acyclic Graph (DAG)** shortest-path algorithms. Developed for the **Algorithms and Data Structures (Algoritmi e Strutture Dati)** final project at **Politecnico di Milano** (A.Y. 2022/2023), the system models a dynamic highway network of electric vehicle service stations and computes the optimal route with the minimum number of stops under strict time and memory constraints.

---

## Project Description

The highway is modeled as a sequence of service stations, each uniquely identified by its non-negative integer distance from the beginning of the highway. Each station hosts a fleet of up to 512 electric rental cars, where each vehicle has a specific battery range (in kilometers).

When traveling from a departure station to a destination station:
1. **Unidirectional movement**: The driver can never turn back; each consecutive stop must be strictly closer to the destination.
2. **Vehicle reachability**: Moving from station *s* to station *t* is only possible if the distance between *s* and *t* is less than or equal to the maximum range among the vehicles available at *s*.
3. **Minimum stops & tie-breaking**: The planner finds the path with the fewest intermediate stops. If multiple paths share the same minimum number of stops, it selects the path that favors stops with the shortest distance from the start of the highway (starting from the end of the route).

---

## Features

- **Zero external dependencies**: Implemented purely in standard C using custom data structures and manual memory management[.
- **Dynamic highway management**: Supports adding/demolishing stations and adding/scrapping electric vehicles in real time via `stdin`.
- **Fast maximum-range lookup**: Each station maintains a **Max-Heap** of its vehicles to retrieve the maximum available driving range in `O(1)` time.
- **Directional route optimization**:
  - **Forward travel (`src < dst`)**: Backward greedy traversal using BST predecessors and a dynamic linked list (`path_from_end`).
  - **Backward travel (`src > dst`)**: Topological-order relaxation over a Directed Acyclic Graph (`dag_shortest_path`) built from BST successors.

---

## Architecture & Data Structures

The system combines four core custom data structures in `main.c`:

- **Binary Search Tree (`StationsTree_t` / `StationsTreeNode_t`)**: Stores highway stations ordered by distance (`dist`). Supports insertion, search, predecessor/successor lookup, and node deletion via subtree transplant (`bst_delete_trans`).
- **Max-Heap (`ParkingLotMaxHeap_t`)**: Fixed-capacity array (`HEAP_LEN = 512`) inside each BST node representing the station's car fleet. Provides `O(1)` access to the best car (`heap_maximum`) and `O(log N)` insertion/deletion.
- **Dynamic Linked List (`node_t`)**: Tracks and prunes candidate stops when computing routes in the forward direction (`src < dst`).
- **Directed Acyclic Graph (`dag_t`)**: Dynamically allocated list of stations between `dst` and `src` used to relax edges and reconstruct the shortest path when traveling toward the start of the highway (`src > dst`).

---

## Supported Commands & I/O Format

The program reads commands line-by-line from `stdin` and prints the corresponding output to `stdout`:

| Command | Format | Expected Output |
| :--- | :--- | :--- |
| **Add Station** | `aggiungi-stazione <distance> <num-cars> <range-1> ... <range-n>` | `aggiunta` / `non aggiunta` |
| **Demolish Station** | `demolisci-stazione <distance>` | `demolita` / `non demolita` |
| **Add Car** | `aggiungi-auto <station-distance> <car-range>` | `aggiunta` / `non aggiunta` |
| **Scrap Car** | `rottama-auto <station-distance> <car-range>` | `rottamata` / `non rottamata` |
| **Plan Route** | `pianifica-percorso <start-distance> <end-distance>` | Space-separated station distances, or `nessun percorso` |

---

## Usage

### Compilation

Compile `main.c` using `gcc` with optimization flags enabled:

```bash
gcc -Wall -Werror -std=gnu11 -O2 main.c -o highway_planner
```

### Execution

Feed an input file containing a sequence of commands via standard input redirection:

```bash
./highway_planner < input.txt > output.txt
```

---

## Repository Structure

```text
.
├── main.c          # Complete C implementation (BST, Max-Heap, DAG, and CLI parser)
├── Open.pdf    # Official project specification (Italian)
└── README.md       # Project documentation
```
