# AeroPath: Emergency Medical Drone Delivery Planner & Simulator

AeroPath is a Java-based command-line platform designed to plan, schedule, route, and simulate autonomous emergency medical drone deliveries. Built around core Computer Science principles and Object-Oriented Design, AeroPath automates emergency supply missions—such as transporting blood, antivenoms, vaccines, or cardiac defibrillators—from request ingestion to live telemetry tracking.

---

## 1. Overview & Problem Statement

In emergency healthcare, delivery times for critical medical supplies are often compromised by urban road congestion, geographic obstacles, or remote terrain. AeroPath addresses key issues in emergency aerial logistics:

* **Intelligent Prioritization:** Replaces simple first-come-first-served scheduling with a dynamic priority queue that prevents request starvation.


* **Automated Feasibility Engine:** Pre-validates candidate drones based on payload capacity, battery limits, discharge rates, and safety margins before launch.


* **Graph-Based Optimal Routing:** Evaluates flight corridors using Dijkstra's and A* pathfinding algorithms on spatial adjacency graphs.


* **Multithreaded Concurrent Tracking:** Simulates live flight dynamics, position progress, ETAs, and real-time battery drain concurrently.


* **Role-Based Access Control (RBAC):** Enforces fine-grained permissions for Medical Officers, Dispatchers, Operators, Maintenance Staff, and Administrators.



---

## 2. System Architecture

AeroPath uses a clean layered architecture designed for strict separation of concerns. Phase 1 implements a lightweight CLI shell on top of the business engine with CSV-based data access.

```
┌─────────────────────────────────────────────────────────────┐
│  Presentation Layer (CLI Shell)                             │
│  Menus, input handling, console & telemetry formatting      │
├─────────────────────────────────────────────────────────────┤
│  Business Logic Layer                                       │
│  Priority Scheduler · Feasibility Engine · Graph Engine     │
│  Pathfinders (Dijkstra/A*) · Simulation Manager (Telemetry) │
├─────────────────────────────────────────────────────────────┤
│  Data Access Layer (DAO Interfaces & CSV Loaders)           │
│  CSV File Persistence & Logging                             │
└─────────────────────────────────────────────────────────────┘
  Domain Models & Custom Exceptions (Shared across all layers)

```

---

## 3. Core Logic & Algorithmic Foundations

### A. Priority Scheduling Algorithm

Emergency medical requests are processed inside a priority queue driven by an urgency evaluation:

* **Priority Tiers:** CRITICAL (Blood, Antivenom), HIGH (Vaccines, Defibrillators), MEDIUM (Surgical Supplies), and LOW (Routine Pharmaceuticals).


* **Anti-Starvation Decay:** Incorporates submission timestamps so that older, lower-tier requests are not indefinitely postponed while ensuring urgent medical tiers take precedence.



### B. Drone Selection & Feasibility Engine

Candidate drones must be available and meet two mandatory pre-flight conditions:

1. **Payload Capacity:** The request weight must not exceed the drone's maximum rated payload capacity.


2. **Battery & Energy Requirements:** The drone's current battery must cover the round-trip distance, taking into account base flight consumption, payload drag during the outbound leg, empty return drag, and a 15% safety reserve.



When multiple drones qualify, the engine selects the best drone by scoring remaining battery percentage, payload efficiency, and proximity to the request location.

### C. Graph Routing Engine (Dijkstra & A*)

The spatial flight corridor network is modeled as a weighted directed graph where nodes represent geographic locations and edges represent flight paths:

* **Dijkstra's Algorithm:** Computes the guaranteed shortest path across the graph using edge relaxation on a Min-Heap priority queue.


* **A* Search Algorithm:** Accelerates pathfinding toward a target destination by incorporating an admissible Euclidean distance heuristic.



### D. Multithreaded Telemetry Simulation

Active flight missions execute asynchronously on dedicated background simulation threads. During each tick interval, the engine updates and streams key telemetry metrics:

* Total distance traveled and flight progress percentage


* Real-time estimated time of arrival (ETA) based on cruising speed


* Continuous battery depletion based on distance and load



---

## 4. Roles and Permissions Matrix

Access control is strictly enforced via Role-Based Access Control (RBAC):

| System Role | Request Ingestion | Dispatch / Launch | Live Telemetry | Maintenance | Admin Controls |
| --- | --- | --- | --- | --- | --- |
| **Medical Officer** | Yes | No | Read-Only | No | No |
| **Dispatcher** | Yes | Yes | Full | No | No |
| **Drone Operator** | No | Yes | Full | No | No |
| **Maintenance Staff** | No | No | Read-Only | Full | No |
| **Admin** | Yes | Yes | Full | Full | Full |

---

## 5. Current Project Structure (Phase 1 CLI)

```
aeropath/
├── pom.xml
├── README.md
├── data/                      # CSV input data & runtime logs
│   ├── locations.csv
│   ├── edges.csv
│   ├── drones.csv
│   ├── users.csv
│   └── item_types.csv
└── src/
    ├── main/java/com/aeropath/
    │   ├── Main.java
    │   ├── model/             # Enums, Drone, Location, EmergencyRequest, Mission, User roles
    │   ├── exception/         # Domain exception hierarchy (Battery, Payload, Route, Mission)
    │   ├── engine/            # PriorityScheduler, FeasibilityEngine, DroneSelector, Config
    │   ├── graph/             # Graph, Edge, PathFinder, Dijkstra, AStar
    │   ├── simulation/        # TelemetryTask, SimulationManager, TelemetryListener
    │   ├── service/           # MissionService, AuthService, AccessControl, MaintenanceService
    │   ├── data/              # DAO interfaces & CSV implementations
    │   └── cli/               # CliApp, InputHelper, ConsolePrinter, TelemetryPrinter
    └── test/java/com/aeropath/ # JUnit unit & integration tests

```

---

## 6. Getting Started

### Prerequisites

* JDK 17 or higher
* Apache Maven 3.8+
* Git

### Build & Run

```bash
# Clone repository
git clone <repository-url>
cd aeropath

# Build project
mvn clean package

# Run from repository root (to access data/ directory)
java -cp target/classes com.aeropath.Main

# Execute JUnit test suite
mvn test

```

---

## 7. Future Scope & Roadmap (Phase 2 — Planned ~6 Months Out)

Phase 1 focuses exclusively on establishing the core CLI, in-memory data structures, CSV logging, and algorithmic engines. Phase 2 extends the platform by expanding interfaces without altering core business logic:

1. **Database & Persistence Layer:** Replace CSV implementations with JDBC DAO persistence backed by a relational database (SQLite/H2 for local testing, migrating to MySQL for production).


2. **Graphical User Interface (GUI):** Implement an interactive desktop dashboard using JavaFX / Swing for visual mission control, live map grids, role management, and interactive analytics.


3. **Advanced Analytics & Reporting:** Real-time reporting on fleet flight hours, maintenance alert tracking, route efficiency metrics, and telemetry logging.



---

## License

MIT License
