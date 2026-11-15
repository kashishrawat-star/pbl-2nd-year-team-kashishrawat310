<h1 align="center">🛡️ Cyber-Attack Simulation & Network Defense Engine</h1><h3 align="center">Algorithmic Malware Propagation & Dynamic Active Defense Simulator in C++</h3><p align="center">
  <img src="https://img.shields.io/badge/Language-C++17-blue">
  <img src="https://img.shields.io/badge/Algorithms-BFS-orange">
  <img src="https://img.shields.io/badge/Design-OOP-green">
</p>---


##📌 Executive Summary
Modern enterprise infrastructure faces attacks ranging from autonomous, indiscriminate lateral movement to surgical, high-priority penetrations. The Cyber-Attack Simulation & Network Defense Engine models network routing infrastructure as a weighted directed graph to simulate and analyze these threat behaviors.

By applying fundamental data structures (Queues, Min-Heaps, Adjacency Lists) alongside C++ Object-Oriented Design, the engine demonstrates both malware propagation mechanics and active defense mitigation via edge-severing isolation protocols.


##✨ Features

•🐛 Autonomous Worm Contagion (BFS): Simulates self-replicating worms spreading outward to all directly connected neighboring routers layer by layer using a FIFO queue.

•🎯 Targeted Trojan Infiltration (Dijkstra): Identifies and exploits the fastest, lowest-latency path through the subnet using a Min-Heap priority queue to strike high-value assets.

•🔒 Role-Based Clearance Controls: Each node implements security clearance levels ($L1 \rightarrow L5$); attacks stall when node defenses outmatch malware sophistication.

•⚡ Dynamic Quarantine Protocol: Real-time active defense triggers isolate compromised assets by severing incident edges ($Weight \rightarrow \infty$) without halting the entire grid.

•📂 Dynamic CSV Topology Parser: Ingests external network maps (network_topology.csv) on startup for flexible architectural evaluations without code recompilation.

•📝 Automated After-Action Forensics (AAR): Automatically exports timeline analytics, compromised nodes, and defense logs to an external after_action_report.txt.

•🎨 ANSI Terminal UI: Interactive, color-coded CLI status monitoring (Green = Secured, Red = Compromised, Yellow = Quarantined).


##🛠️ Tech Stack
Domain	Technologies / Concepts
Language	C++17
| Domain | Technologies / Concepts |
|---|---|
| Language | C++17 |
| Data Structures | Graph, Queue, Priority Queue |
| Algorithms | BFS, Dijkstra |
| OOP | Encapsulation, Inheritance, Polymorphism |
| Memory Management | Smart Pointers |
| File Handling | File Streams |


##🚀 Quick Start / Installation
Prerequisites
Modern C++17 compiler (g++, clang++, or MSVC)
CMake 3.15+ (optional) or native CLI tools
Build & Run
# 1. Clone the repository
git clone https://github.com/Zishan3909/cyber-attack-simulation.git
cd cyber-attack-simulation

# 2. Compile with C++17 standards
g++ -std=c++17 -O3 -o sim.exe main.cpp

# 3. Launch the Command Center
./sim.exe


##📸 Terminal Output & Workflow
  ============= COMMAND CENTER =============
  1. View Network Status
  2. View Network Topology (Connections)
  3. Launch Worm Attack        (BFS)
  4. Launch Targeted Trojan     (Dijkstra)
  5. Quarantine a Router
  6. Restore a Quarantined Router
  7. Export After-Action Report
  8. Reset Simulation
  0. Exit
