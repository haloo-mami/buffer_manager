# Buffer Pool Simulator

## Project Description

Buffer Pool Simulator is a C++ application that simulates and compares database buffer pool management strategies. It models how a database engine manages a fixed-size in-memory buffer pool when serving page requests from disk, implementing three classic eviction policies:

- **LRU (Least Recently Used)** — evicts the page that was accessed least recently
- **MRU (Most Recently Used)** — evicts the page that was accessed most recently
- **CLOCK** — a circular approximation of LRU using reference bits

The simulator operates on a real SQLite database (`simulation.db`) containing sample `Students` and `Enrollments` tables. It generates page access traces for two workload types — **SELECT** (random/sequential reads) and **JOIN** (simulated hash join across two tables) — then replays those traces through each buffer manager, recording hits, misses, disk reads/writes, and eviction events.

Results are persisted to SQLite and exposed via an embedded HTTP server. A browser-based dashboard (`index.html`) lets users configure simulations, watch step-by-step buffer animations, compare strategies, and execute live SQL queries against the database.

---

## Dependencies / Requirements

| Dependency | Version | Purpose |
|---|---|---|
| `g++` (GCC) | C++17 or later | Compilation |
| `sqlite3` (dev library) | Any recent | Database backend |
| `httplib.h` | Bundled (cpp-httplib) | Embedded HTTP server |
| Standard C++17 library | — | STL containers, threads, mutexes |

### Install dependencies (Ubuntu/Debian)

```bash
sudo apt update
sudo apt install g++ libsqlite3-dev
```

### Install dependencies (macOS with Homebrew)

```bash
brew install gcc sqlite
```

---

## File Structure

```
buffer/
├── main.cpp                  # Entry point — initializes DB and starts HTTP server
├── defs.h                    # Class declarations for Page, LRU, MRU, CLOCK buffer managers; StepInfo/SimResult structs
├── defs.cpp                  # Full implementation of all three buffer manager classes
├── server.cpp                # Embedded HTTP server (cpp-httplib); handles all API routes and simulation logic
├── sqlite_integration.h      # Header for SQLite helper functions
├── sqlite_integration.cpp    # SQLite init, page trace generation, SQL query execution
├── httplib.h                 # Bundled cpp-httplib single-header HTTP library
├── index.html                # Browser dashboard (Control Panel, Buffer Animation, Results, SQL Query UI)
├── Makefile                  # Build instructions

```

---

## Output Files

| File | Description |
|---|---|
| `buffer_sim` | The compiled binary executable |
| `simulation.db` | SQLite database; stores simulation results in a `sim_results` table alongside the `students` and `enrollments` data tables |
| `results.csv` | Exported simulation metrics — strategy, workload, buffer size, disk reads/writes, total I/O, hits, misses, hit rate (%), evictions |
| `log_clock.txt` | Per-access log for the CLOCK buffer manager (written at runtime) |
| `log_lru.txt` | Per-access log for the LRU buffer manager (written at runtime) |
| `log_mru.txt` | Per-access log for the MRU buffer manager (written at runtime) |
| `join_output.txt` | Result rows from the JOIN workload query |
| `data.txt` | Plain-text dump of the `students` and `enrollments` table contents |

---

## Steps to Run the Project

### 1. Clone / Extract the project

```bash
unzip final.zip
cd buffer
```

### 2. Build the project

```bash
make
```

This compiles `main.cpp`, `defs.cpp`, `sqlite_integration.cpp`, and `server.cpp` into the `buffer_sim` executable using:

```
g++ -std=c++17 -O2 -o buffer_sim main.cpp defs.cpp sqlite_integration.cpp server.cpp -lsqlite3
```

### 3. Run the simulator

```bash
./buffer_sim
```

or equivalently:

```bash
make run
```

Expected output:

```
Database initialized at simulation.db
Open http://localhost:8080 in your browser
Press Ctrl+C to stop server.
```

### 4. Open the dashboard

Open your browser and navigate to:

```
http://localhost:8080
```

From the dashboard you can:

- **Run a simulation** — select a buffer replacement strategy (LRU / MRU / CLOCK), workload type (SELECT / JOIN), and buffer size, then click **Run Simulation**
- **Replay step-by-step** — watch an animated visualization of page hits, misses, and evictions frame by frame
- **Compare results** — view a table of all past simulation runs with I/O and hit rate metrics
- **Execute SQL** — run arbitrary SQL queries directly against `simulation.db` from the browser UI

### 5. Stop the server

Press `Ctrl+C` in the terminal.

##Authors

---
