# Elevator System Design

## 🧠 Problem Statement

Design a real-time elevator system capable of handling multiple elevators in a building. The system should:

- Handle user requests made from **inside** and **outside** the elevator.
- Assign the best-suited elevator for a request based on current position, state, and time.
- Support elevators moving up, down, halting, and being inactive.
- Handle dynamic user requests at any moment.
- Reroute requests if an elevator goes out of service.

Assume:
- Elevator speed is **1 floor per second**.
- The system simulates time in seconds.
- Each elevator has a **fixed capacity**.
- Requests are processed in real-time every second.

---

## 🧭 My Thought Process

Here’s how I approached the design step-by-step:

- **Two Types of Requests**: A user can press a button inside an elevator (internal request) or outside (external request).  
  → This is modeled using a single `Request` class with a `bool isInternal` flag.

- **Elevator Speed**: We simulate each elevator moving 1 floor per second.

- **System Ticks**: Every second, the system checks for new requests, updates elevator states, and determines if any elevator should halt or move.

- **States of Elevator**: Each elevator can be in one of several states:
  - `MOVING_UP`, `MOVING_DOWN`, `HALT`, `INACTIVE`.

- **Request Assignment**: The system uses an **Elevator Assignment Strategy** to determine which elevator should handle a request. This can be based on:
  - Current elevator state
  - Distance to requested floor
  - Time to reach

- **Request Processing**: Each elevator maintains:
  - `upStops` (min-heap)
  - `downStops` (max-heap)
  - Capacity
  - Current direction
  - Requests it has accepted
  
  It continues in the current direction until there are no more stops, then switches direction.

- **Rerouting**: If an elevator goes down (marked inactive), its requests are redistributed to active elevators based on minimal reach time.

- **Priority**: When routing, elevators that are active and idle (not handling any requests) are preferred first.

---

## ⚙️ Main Classes Overview

| Class | Responsibility |
|-------|----------------|
| `Request` | Represents a user request with floor, direction, and type (`isInternal` flag). |
| `ElevatorSystem` | Manages all elevators, time simulation, request assignment, rerouting, and overall orchestration. |
| `Elevator` | Represents a single elevator. Tracks floor, direction, state, capacity, and handles execution of requests. |
| `RequestAssignmentStrategy` | Strategy pattern used to determine the best elevator to assign a request to. |

---

## 🧩 Design Patterns Used

### ✅ Strategy Pattern
Used for **elevator assignment logic**. This helps if we want to change how elevators are assigned dynamically (e.g., time-based, distance-based, idle-first).

### ✅ State Pattern (Conceptually applied)
Each elevator manages its state (`MOVING_UP`, `MOVING_DOWN`, `HALT`, etc.). While not fully implemented as a separate `State` class, the logic mimics state transitions.

### ❌ Observer Pattern
Was initially considered (for decoupling request inputs), but removed in final design to avoid overengineering. We simplified by letting requests be pushed directly into the system queue.

---

## 🔄 Simulation Flow

```text
System Time Tick Loop:
┌─────────────────────────────┐
│ Check for new requests      │
│ Route request to elevator   │
│ Each elevator takes 1 step  │
│   - Update state            │
│   - Move floor (if needed)  │
│   - Halt if at drop/pick    │
└─────────────────────────────┘
