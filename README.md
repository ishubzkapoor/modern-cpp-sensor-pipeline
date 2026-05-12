````md
# Modern C++ Sensor Pipeline

A modern C++17 multi-threaded sensor pipeline simulating real-time IMU data processing using a producer-consumer architecture. The project demonstrates concurrent programming, bounded queues, RAII-based thread management, synchronization primitives, profiling, and sanitizer-based debugging workflows commonly used in embedded, robotics, and sensor-processing systems.

---

# Architecture

The pipeline is divided into three main components:

- **IMU Thread (Producer)**  
  Simulates IMU sensor readings at 100 Hz and continuously generates timestamped acceleration data.

- **Filter Queue (Bounded Queue)**  
  Acts as a thread-safe communication layer between producer and consumer threads. The queue uses mutexes and condition variables to safely synchronize concurrent access.

- **Logger / Consumer Thread**  
  Asynchronously retrieves sensor readings from the queue, computes processing statistics, and measures end-to-end latency using `std::chrono`.

The pipeline state is controlled using atomic variables to ensure safe thread shutdown and predictable execution behavior.

```text
┌──────────────┐       ┌──────────────┐       ┌──────────────┐
│  IMU Thread  │──────▶│ Filter Queue │──────▶│  Logger      │
│  (Producer)  │  push │ (Bounded)    │  pop  │  (Consumer)  │
│  100 Hz      │       │ mutex + cv   │       │  Async       │
└──────────────┘       └──────────────┘       └──────────────┘
       │                                              │
  std::atomic                                   std::chrono
  <bool> running                                timing stats
```
# Features

- Multi-threaded producer-consumer architecture
- Thread-safe bounded queue implementation
- Asynchronous sensor data processing
- Atomic synchronization and deterministic shutdown handling
- RAII-based thread lifecycle management
- Latency profiling using `std::chrono`
- Unit testing with GoogleTest
- AddressSanitizer (ASan) and ThreadSanitizer (TSan) support
- CMake-based build system
- Modern C++17 design principles

---

# Concepts Used

| Concepts |
|---|
| `constexpr`, `std::string_view` |
| `std::array`, fixed-capacity containers |
| `enum class` |
| RAII and move semantics |
| `[[nodiscard]]`, lambdas, `std::optional` |
| Encapsulation and const correctness |
| Templates |
| Threads, mutexes, condition variables, atomics |
| Sanitizers and debugging workflows |
| `std::chrono` profiling |

---

# Project Structure

```text
.
├── CMakeLists.txt
├── main.cpp
├── sensor_pipeline.hpp
├── test_pipeline.cpp
└── README.md
```

---

# Build Instructions

## Requirements

- CMake ≥ 3.16
- C++17 compatible compiler
- Linux recommended
- GoogleTest (optional for unit testing)

---

## Build

```bash
mkdir build
cd build

cmake ..
make -j$(nproc)
```

Run the executable:

```bash
./sensor_pipeline
```

Example output:

```text
[INFO] Pipeline running at 100 Hz
[INFO] Processed 300 readings
```

---

# Enable AddressSanitizer

AddressSanitizer helps detect:
- memory leaks
- invalid memory access
- buffer overflows

```bash
cmake -DENABLE_ASAN=ON -DCMAKE_BUILD_TYPE=Debug ..
make -j$(nproc)

./sensor_pipeline
```

---

# Enable ThreadSanitizer

ThreadSanitizer helps detect:
- data races
- synchronization issues
- unsafe concurrent access

```bash
cmake -DENABLE_TSAN=ON -DCMAKE_BUILD_TYPE=Debug ..
make -j$(nproc)

./sensor_pipeline
```

---

# Unit Tests

Enable tests:

```bash
cmake -DBUILD_TESTS=ON ..
make -j$(nproc)

ctest
```

Included tests:

| Test | Description |
|---|---|
| PushPopRoundTrip | Verifies queue push/pop behavior |
| ShutdownUnblocksConsumer | Ensures shutdown safely wakes waiting threads |
| ProcessesReadings | Confirms pipeline processes sensor data |
| RapidStartStop | Stress-tests RAII thread cleanup |

---

# Profiling Workflow

## Build for Profiling

```bash
cmake -DCMAKE_BUILD_TYPE=RelWithDebInfo ..
make -j$(nproc)
```

## CPU Profiling with perf

```bash
perf record -g --call-graph dwarf ./sensor_pipeline
```

Generate FlameGraph:

```bash
perf script | stackcollapse-perf.pl | flamegraph.pl > pipeline_flame.svg
```

---

## Heap Allocation Profiling

```bash
heaptrack ./sensor_pipeline
```

Useful for:
- identifying unnecessary allocations
- analyzing memory usage
- optimizing hot execution paths

---

# Thread Safety Notes

The implementation avoids common concurrency issues by:
- protecting shared state with mutexes
- synchronizing threads using condition variables
- using atomic variables for lightweight state management
- ensuring safe shutdown behavior without deadlocks

The consumer thread avoids unsafe patterns such as checking queue size outside synchronization scopes to prevent race conditions.

---

# Example Applications

This architecture can be extended for:
- sensor fusion pipelines
- robotics systems
- UAV telemetry processing
- radar and SDR applications
- embedded Linux systems
- real-time measurement systems
- communication system simulations

---

# Future Improvements

Potential future extensions include:
- multi-producer and multi-consumer support
- lock-free queue implementation
- Kalman filtering
- binary logging
- real hardware sensor integration
- UDP/TCP streaming
- SIMD optimization
- benchmark framework integration

---

# License

This project is intended for educational and portfolio purposes.
````
