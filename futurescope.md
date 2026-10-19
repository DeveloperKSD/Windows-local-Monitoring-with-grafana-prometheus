OS Performance Observatory
Operating Systems Course Project: Proposal
The Idea
A monitoring platform that does more than display metrics. It links live system behavior to each unit of our syllabus, and tests textbook OS algorithms against real workloads.
Stack: Windows Exporter, Prometheus, Grafana, Docker Compose, and a custom Python exporter.
What Makes It Different
Most monitoring projects only show graphs. We feed real process, memory and disk data into textbook algorithms and compare how they would perform. Theory is tested on a real machine.
What We Build, Unit by Unit
Unit
Feature
What it demonstrates
1. OS Overview
Kernel vs user mode CPU split, system calls and interrupts per second
The user/kernel boundary, live
2. Processes and Scheduling
Live process table, context switch rate, priority experiment, scheduling comparison engine (FCFS, SJF, Round Robin, Priority on real processes)
PCB contents, scheduling trade-offs
3. Concurrency
Race condition demo with and without locks, producer-consumer monitor
Why mutual exclusion matters, and its cost
4. Deadlock
Banker's algorithm panel, dining philosophers with deadlock detection and fix
Safe states, detection, prevention
5. Memory
Page fault and working set dashboard, page replacement simulator (FIFO, LRU, Optimal, Clock), leak and thrashing alerts
Paging, replacement, Belady's anomaly
6. I/O and Disk
IOPS and latency panel, disk scheduling simulator (FCFS, SSTF, SCAN, C-SCAN, LOOK), sequential vs random I/O test
Seek-time optimization
7. File Management
Storage analyzer: usage by folder and file type
File organization and space management
Product Features
• Experiment runner: one command starts a workload, and Grafana marks it on the graphs
• OS Health Score: one number combining CPU, memory, disk and process health
• Alerting: thrashing, memory leaks, blocked processes
• One-command deploy: docker compose up, fully reproducible
Scope
• Core: syllabus-mapped dashboard, experiment runner, Docker Compose
• Differentiators: scheduling comparison engine, page replacement simulator, Banker's and deadlock demo, disk scheduling simulator
• Polish: alerts and health score
Outcome
A deployable observability tool for studying OS behavior, usable as a teaching aid and as evidence for a paper:
"Observing OS scheduling, memory and I/O behavior under controlled workloads using a Prometheus-based pipeline."
Real-World Relevance
The same tools and concepts are used in DevOps, SRE, cloud engineering and performance tuning.
