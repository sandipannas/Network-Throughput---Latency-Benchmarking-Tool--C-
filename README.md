# NetTester — TCP Throughput & Latency Benchmarking Tool

A lightweight command-line tool written in C++ that measures TCP throughput and round-trip latency between two endpoints. Built to understand the fundamentals of socket programming and basic network performance measurement.

## Features

- **Throughput mode** — streams a configurable amount of data from client to server and reports achieved throughput in Mbps.
- **Latency mode** — sends small ping-style messages and measures round-trip time (RTT), reporting average, minimum, and maximum over N iterations.
- Simple, dependency-free implementation using the POSIX sockets API (no external networking libraries).
- Works over loopback (localhost) or across machines on the same LAN.

## Why this project

Networking theory (TCP handshakes, congestion control, etc.) is easy to read about and hard to internalize without writing the code yourself. This project was built to get hands-on with the actual `socket()` / `bind()` / `listen()` / `accept()` / `connect()` / `send()` / `recv()` calls that sit underneath every higher-level networking library, and to practice measuring real network performance rather than just estimating it.

## Requirements

- A Linux or macOS environment (uses POSIX sockets — not tested on Windows)
- A C++ compiler supporting C++17 (g++ or clang++)
- `make` (optional, for the provided Makefile)

## Building

```bash
git clone <your-repo-url>
cd nettester
make
```

This produces two binaries: `server` and `client`.

## Usage

### Throughput test

Start the server on the receiving machine:

```bash
./server --port 5000
```

Run the client on the sending machine:

```bash
./client --host <server-ip> --port 5000 --mode throughput --size 100MB
```

Example output:

```
Sent 100.0 MB in 1.842s
Throughput: 434.2 Mbps
```

### Latency test

Start the server:

```bash
./server --port 5000
```

Run the client in latency mode:

```bash
./client --host <server-ip> --port 5000 --mode latency --count 20
```

Example output:

```
20 pings sent
Avg RTT: 0.412 ms
Min RTT: 0.298 ms
Max RTT: 0.611 ms
```

## How it works

- **Throughput mode:** the client opens a single TCP connection and writes data in fixed-size chunks until the target size is sent. The server reads until the connection closes and reports total bytes received and elapsed time.
- **Latency mode:** the client sends a small timestamped message; the server echoes it back immediately; the client computes RTT from the round-trip time. This repeats for the configured number of iterations.
