# Modern C++ IMU Sensor Pipeline

This project is a small C++17 sensor-processing pipeline that simulates how IMU data can be collected, passed between threads, processed, and logged in real time.

The main goal of the project is to demonstrate clean C++ design, safe multi-threading, and basic real-time sensor data handling.

---

## Overview

The pipeline has three main parts:

1. **IMU Producer Thread**
   Generates simulated IMU acceleration data at 100 Hz.

2. **Thread-Safe Queue**
   Stores sensor readings safely between the producer and consumer threads.

3. **Logger / Consumer Thread**
   Reads the sensor data asynchronously, processes it, and measures timing information using `std::chrono`.

```text
┌──────────────┐       ┌──────────────┐       ┌──────────────┐
│  IMU Thread  │──────▶│ Sensor Queue │──────▶│  Logger      │
│  Producer    │       │ Thread-safe  │       │  Consumer    │
│  100 Hz      │       │ mutex + cv   │       │  Async       │
└──────────────┘       └──────────────┘       └──────────────┘
```

The pipeline uses atomic variables to start and stop the system safely.

---

## What This Project Shows

* Real-time-style IMU data generation
* Producer-consumer architecture
* Safe communication between threads
* Thread-safe queue implementation
* Use of mutexes and condition variables
* Atomic flag for safe pipeline shutdown
* Basic timing measurement with `std::chrono`
* CMake-based build workflow
* Modern C++17 coding style

---

## Concepts Used

| C++ / Software Concept |
| ---------------------- |
| Modern C++17           |
| Threads                |
| Mutexes                |
| Condition variables    |
| Atomic variables       |
| RAII                   |
| Move semantics         |
| Templates              |
| `std::optional`        |
| `std::chrono`          |
| CMake                  |

---

## Project Structure

```text
.
├── CMakeLists.txt
├── main.cpp
├── sensor_pipeline.hpp
└── README.md
```

---

## Build Instructions

### Requirements

* CMake 3.16 or newer
* C++17 compatible compiler
* Linux recommended

---

### Build and Run

```bash
mkdir build
cd build

cmake ..
make -j$(nproc)

./sensor_pipeline
```

Example output:

```text
[INFO] Pipeline running at 100 Hz
[INFO] Processed 300 readings
```

---

## Possible Extensions

This project can be extended into more advanced sensor-processing systems, such as:

* unit testing with GoogleTest
* AddressSanitizer and ThreadSanitizer support
* CPU and heap profiling
* sensor fusion pipelines
* UAV telemetry processing
* radar or SDR data pipelines
* embedded Linux sensor applications
* real hardware IMU integration
* Kalman filtering
* UDP/TCP data streaming
* multi-producer and multi-consumer systems

---

## Purpose

This project was built as a portfolio project to practice modern C++17, multi-threading, synchronization, and real-time-style sensor data processing.

It is especially relevant for embedded systems, robotics, sensor software, radar processing, and measurement system applications.

---
