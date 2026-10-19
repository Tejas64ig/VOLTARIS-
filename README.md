<div align="center">

```
██╗   ██╗ ██████╗ ██╗  ████████╗ █████╗ ██████╗ ██╗███████╗
██║   ██║██╔═══██╗██║  ╚══██╔══╝██╔══██╗██╔══██╗██║██╔════╝
██║   ██║██║   ██║██║     ██║   ███████║██████╔╝██║███████╗
╚██╗ ██╔╝██║   ██║██║     ██║   ██╔══██║██╔══██╗██║╚════██║
 ╚████╔╝ ╚██████╔╝███████╗██║   ██║  ██║██║  ██║██║███████║
  ╚═══╝   ╚═════╝ ╚══════╝╚═╝   ╚═╝  ╚═╝╚═╝  ╚═╝╚═╝╚══════╝
```

### ⚡ Advanced Multithreaded Energy-Crisis Simulation & Smart Grid Decision Support

[![Java](https://img.shields.io/badge/Java-17-ED8B00?style=for-the-badge&logo=openjdk&logoColor=white)](https://openjdk.org/projects/jdk/17/)
[![Maven](https://img.shields.io/badge/Maven-3.9.6-C71A36?style=for-the-badge&logo=apachemaven&logoColor=white)](https://maven.apache.org/)
[![JUnit 5](https://img.shields.io/badge/JUnit-5.10-25A162?style=for-the-badge&logo=junit5&logoColor=white)](https://junit.org/junit5/)
[![SQLite](https://img.shields.io/badge/SQLite-3.46-003B57?style=for-the-badge&logo=sqlite&logoColor=white)](https://sqlite.org/)
[![License](https://img.shields.io/badge/License-MIT-blue?style=for-the-badge)](LICENSE)

[![Tests](https://img.shields.io/badge/Tests-216%20Passing-brightgreen?style=flat-square)]()
[![Lines](https://img.shields.io/badge/Source%20Files-83-blueviolet?style=flat-square)]()
[![Phases](https://img.shields.io/badge/Phases-10%20Complete-success?style=flat-square)]()

> **Educational simulation system only. Not connected to and cannot control any real electrical infrastructure.**

</div>

---

## 🌆 The Problem

Imagine a city on the hottest day of the year:

- 🌡️ A heatwave strikes — electricity demand **surges 35%** as every building blasts AC
- 🏭 The city's largest power plant **trips offline** under overload
- ☀️ Solar panels **overheat and underperform** — output drops 70%
- ⚡ The grid now has a **massive generation deficit**

**Who gets power? Who gets cut off? In what order? How fast can the grid recover?**

Voltaris answers all of these questions in real time — simulating the full crisis lifecycle from detection to recovery, with a live visual dashboard.

---

## 🎯 What is Voltaris?

**Voltaris** is a city-scale electrical grid simulation and decision-support system built entirely in Java. It models:

- **Hundreds of grid nodes** — power plants, solar/wind farms, substations, hospitals, residential zones, industries
- **Real-time energy accounting** — generation, transmission, demand, storage, and loss
- **Crisis detection** — automatic classification of grid health from NORMAL → ADVISORY → WARNING → ALERT → EMERGENCY → BLACKOUT
- **Priority-based load shedding** — intelligent allocation protecting critical services (hospitals, water) while shedding low-priority loads (industry)
- **Economic dispatch** — merit-order generation scheduling to minimize cost at each tick
- **Live Swing dashboard** — interactive 2D topology graph, energy bar charts, incident table, and scenario injection controls

---

## 🖥️ Dashboard Preview

```
┌─────────────────────────────────────────────────────────────────────────────────────┐
│  ⚡ Voltaris — Energy Crisis Simulation Dashboard                        _ □ ✕      │
├──────────────┬────────────────┬───────────────────────────────────────┬─────────────┤
│ GENERATION   │ DEMAND         │ DEFICIT                               │ TICK / TIME │
│ 1,847.3 MW   │ 1,623.9 MW     │ 0.0 MW (0.0%)  ✅                    │ #48  04h00  │
├──────────────┴────────────────┴───────────────────────────────────────┴─────────────┤
│ SIMULATION   │                                                         Active        │
│  RUNNING     │     ●───────────●          ●                           Incidents     │
│              │    /             \         /\        ●                              │
│[  PAUSE  ]   │   ●    ●──────●  ●──────●    ●──────●      Energy     ⚠ GENERA...  │
│              │   |    |      |          \          |      Balance    ⚠ WEATHE...  │
│[ RESUME  ]   │   ●────●      ●──────────●          ●      (MW)                    │
│              │    \             /                                    ▓▓▓▓ 1847     │
│[  STOP   ]   │     ●──────────●     100 nodes · 180 lines           ░░░░ 1623     │
│              │                                                                      │
│ INJECTIONS   │  ● Power Plant  ● Solar  ● Wind  ● Hospital                        │
│[INJ FAULT]   │  ● Residential  ● Battery ● Industrial ● Water                     │
│[ HEATWAVE]   │                                                                      │
└──────────────┴─────────────────────────────────────────────────────────────────────┘
│  CRISIS LEVEL: NORMAL  |  Simulation Time: 04:00:00  |  Tick: #48                  │
└─────────────────────────────────────────────────────────────────────────────────────┘
```

---

## 🏗️ Architecture Overview

```
┌─────────────────────────────────────────────────────────────────┐
│                        VOLTARIS SYSTEM                          │
│                                                                 │
│  ┌──────────────┐    ┌──────────────┐    ┌──────────────────┐  │
│  │  GridSimulator│───▶│ GridEventBus │───▶│ DashboardCtrl    │  │
│  │  (Facade)    │    │  (Observer)  │    │ (Swing EDT)      │  │
│  └──────┬───────┘    └──────────────┘    └──────────────────┘  │
│         │                                                        │
│  ┌──────▼────────────────────────────────────────────────────┐  │
│  │                    TICK PIPELINE                           │  │
│  │  1. SimulationClock.nextTick()                            │  │
│  │  2. DemandForecastEngine.forecastAll()  [time-of-day]     │  │
│  │  3. EconomicDispatchEngine.dispatch()   [merit order]     │  │
│  │  4. EnergyAccountingEngine.computeTick() [MW accounting]  │  │
│  │  5. RecoveryEngine.executeShedding()   [load balancing]   │  │
│  │  6. CrisisDetectionEngine.assess()     [health report]    │  │
│  │  7. Publish events → GridEventBus                         │  │
│  └───────────────────────────────────────────────────────────┘  │
│                                                                 │
│  ┌──────────────────────┐    ┌──────────────────────────────┐  │
│  │   DOMAIN MODEL       │    │   INFRASTRUCTURE             │  │
│  │  • PowerGrid (graph) │    │  • SQLite (persistence)      │  │
│  │  • PowerPlant        │    │  • Jackson (JSON scenarios)  │  │
│  │  • RenewableGen      │    │  • SLF4J + Logback           │  │
│  │  • EnergyConsumer    │    │  • JMH (benchmarks)          │  │
│  │  • BatteryStorage    │    │  • DijkstraPathFinder        │  │
│  │  • GridIncident      │    │  • SyntheticGridGenerator    │  │
│  └──────────────────────┘    └──────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────┘
```

---

## ✨ Key Features

### 🔌 Grid Simulation Engine
- **100-node, 180-line** synthetic city grid generated from seed (fully reproducible)
- Discrete-time simulation at configurable intervals (default: 5 min/tick)
- Supports power plants, solar farms, wind farms, substations, battery storage
- Transmission line capacity enforcement, fault injection, and rerouting

### ⚡ Energy Accounting
- Per-tick MW generation/demand/allocation tracking with full audit trail
- Battery charge/discharge with configurable efficiency factors (92% charge, 90% discharge)
- Generation ramp-rate constraints (plants can't change output instantaneously)
- Renewable output fluctuation via weather conditions (SUNNY → STORMY → WINDY)

### 🚨 Crisis Detection (6 Levels)
| Level | Threshold | Response |
|---|---|---|
| 🟢 NORMAL | 0% deficit | None |
| 🔵 ADVISORY | < 5% deficit | Monitor |
| 🟡 WARNING | 5–15% deficit | Alert operators |
| 🟠 ALERT | 15–30% or critical at risk | Automated recovery |
| 🔴 EMERGENCY | > 30% deficit | Emergency shedding |
| ☠️ BLACKOUT | > 60% deficit | Full system response |

### 🏥 Priority-Based Load Shedding
```
Priority 1 — CRITICAL   → Hospitals, ICUs           [NEVER shed]
Priority 2 — ESSENTIAL  → Water Treatment Plants    [NEVER shed]
Priority 3 — HIGH       → Schools, Data Centres     [Last resort]
Priority 4 — MEDIUM     → Residential Areas         [Reduced first]
Priority 5 — LOW        → Commercial Zones          [Shed early]
Priority 6 — DEFERRABLE → Industrial Loads          [Shed first]
```

### 📊 Live Dashboard Panels
| Panel | Location | Shows |
|---|---|---|
| **Metrics Bar** | Top | Generation, Demand, Deficit, Batteries, Tick/Time |
| **Grid Topology** | Centre-left (60%) | 2D interactive node-edge graph with color-coded node types |
| **Energy Balance** | Centre-right (40%) | Live bar chart: Generation vs Demand vs Allocated |
| **Incident Table** | Right sidebar | Active incidents with type, severity, status |
| **Control Panel** | Left sidebar | Start/Stop/Pause + scenario injection buttons |

---

## 🎮 Scenario Playbook

### Scenario 1: Normal Operation
```
Click [ START ] → Watch the grid run at ~95% supply reliability
Generation: ~1,800 MW  |  Demand: ~1,620 MW  |  Deficit: ~0%
```

### Scenario 2: Plant Failure
```
Click [ INJECT FAULT ] → Select a plant → Confirm
→ Plant output drops to 0 MW instantly
→ Crisis detection fires: WARNING or ALERT
→ Remaining plants ramp up, batteries discharge
→ Industrial zones partially shed to protect hospitals
```

### Scenario 3: Heatwave
```
Click [ HEATWAVE ]
→ All regional demand spikes +35%
→ Solar output drops to 20% (panels overheat)
→ Wind slightly increases (+10%)
→ Combined demand surge creates ALERT or EMERGENCY
→ Load shedding activates in priority order
```

### Scenario 4: Cascading Crisis
```
Click [ HEATWAVE ] then [ INJECT FAULT ]
→ Double crisis: high demand + lost generation
→ May trigger EMERGENCY or BLACKOUT classification
→ Battery reserves depleted, recovery engine maxed
```

---

## 🛠️ Design Patterns Used

| Pattern | Where | Purpose |
|---|---|---|
| **Observer** | `GridEventBus` | Decouples simulation engine from UI and subsystems |
| **Strategy** | `RecoveryStrategy` | Hot-swap load-shedding algorithm (Priority vs Proportional) |
| **Factory Method** | `PowerPlant.online()`, `GridNode.online()` | Clean object creation with validated defaults |
| **Singleton** | `DatabaseManager` | One SQLite connection per JVM |
| **Facade** | `GridSimulator` | Single entry point over 6 complex subsystems |
| **Chain of Responsibility** | `EnergyAccountingEngine` | Priority-ordered consumer allocation |
| **Template Method** | `RecoveryStrategy` | Common shedding flow, swappable algorithm |

---

## 📁 Project Structure

```
voltaris/
├── src/main/java/com/voltaris/
│   ├── app/                    # Entry point (VoltarisApp.java)
│   ├── config/                 # GridConfig (small/medium/large presets)
│   ├── domain/
│   │   ├── consumer/           # EnergyConsumer
│   │   ├── generation/         # PowerPlant, RenewableGenerator
│   │   ├── grid/               # PowerGrid, SimulationStatus
│   │   ├── node/               # GridNode, NodeType, OperationalStatus, PriorityLevel
│   │   └── storage/            # BatteryStorage
│   ├── energy/                 # EnergyAccountingEngine, GridEnergyBalance
│   ├── crisis/                 # CrisisDetectionEngine, ImbalanceDetector, CrisisLevel
│   ├── dispatch/               # EconomicDispatchEngine (Merit Order)
│   ├── recovery/               # RecoveryEngine, LoadSheddingPlan, Strategies
│   ├── grid/
│   │   ├── graph/              # GridGraph, GridEdge
│   │   └── routing/            # DijkstraPathFinder, PathResult
│   ├── simulation/
│   │   ├── bus/                # GridEventBus (Observer)
│   │   ├── clock/              # SimulationClock, SimulationTick
│   │   ├── event/              # 12 sealed GridEvent subtypes
│   │   ├── forecast/           # DemandForecastEngine (time-of-day patterns)
│   │   ├── generator/          # SyntheticGridGenerator (seeded, reproducible)
│   │   └── incident/           # GridIncident, IncidentType/Severity/Status
│   ├── persistence/            # SQLite DatabaseManager, Repositories
│   ├── benchmark/              # JMH Benchmarks
│   ├── ui/
│   │   ├── panels/             # GridTopologyPanel, EnergyBalancePanel, etc.
│   │   └── theme/              # VoltarisTheme (dark industrial palette)
│   └── exception/              # VoltarisException hierarchy
├── src/main/resources/
│   └── scenarios/              # heatwave_crisis.json, plant_failure.json
├── src/test/java/              # 216 JUnit 5 tests
└── pom.xml
```

---

## 🚀 Getting Started

### Prerequisites
- **Java 17+** (tested with Microsoft JDK 17.0.20)
- **Maven 3.8+**

### Build & Run

```bash
# Clone the repository
git clone https://github.com/Tejas64ig/voltaris.git
cd voltaris

# Compile and run all 216 tests
mvn test

# Build the executable fat-jar (includes all dependencies)
mvn package -DskipTests

# Launch the dashboard
java -jar target/voltaris-1.0.0-SNAPSHOT.jar
```

### Quick Test
```bash
# Run only core engine tests
mvn test -Dtest="PowerPlantTest,EnergyAccountingEngineTest,DijkstraPathFinderTest"

# Run crisis detection tests
mvn test -Dtest="CrisisLevelTest"

# Run recovery engine tests
mvn test -Dtest="RecoveryEngineTest"
```

---

## 📐 Core Domain Model

```
                    PowerGrid (graph)
                    /              \
           GridNode[]          TransmissionLine[]
          /    |    \
    PowerPlant  BatteryStorage  EnergyConsumer
         |            |              |
   currentOutputMW  currentChargeMWh  currentAllocatedMW
   rampRateMW/min   chargeEfficiency  minimumRequiredMW
   operationalStatus dischargingRate  priorityLevel
```

### Energy Flow (Per Tick)

```
 [Plants]──┐
 [Solar] ──┼──→ totalGenerationMW
 [Wind]  ──┘         │
 [Batteries]────→ + dischargeMW
                      │
                 availableForAllocation
                      │
            ┌─────────▼──────────┐
            │ Priority Allocator │  (Chain of Responsibility)
            └──┬──┬──┬──┬──┬───┘
               │  │  │  │  │
            CRIT ESS HIGH MED LOW
            Hosp Water Sch Res Ind
                      │
              unservedDemandMW → CrisisDetection → Events
```

---

## ⚙️ Configuration

Edit `SimulationConfig` for custom runs:

```java
// Default: 100-node medium city, 288 ticks (24 sim hours), 200ms/tick
SimulationConfig config = SimulationConfig.defaultConfig();

// Fast test: small grid, 24 ticks, no delay
SimulationConfig config = SimulationConfig.fastTest();

// Custom
SimulationConfig config = new SimulationConfig(
    "My Custom Scenario",   // name
    5.0,                    // minutes per tick
    144,                    // max ticks (12 sim hours)
    150,                    // ms between ticks (UI refresh rate)
    42L,                    // seed (reproducible)
    true,                   // enable crisis detection
    true,                   // enable economic dispatch
    true,                   // enable load shedding
    GridConfig.medium()     // grid size
);
```

### Grid Size Presets

| Preset | Nodes | Lines | Plants | Use Case |
|---|---|---|---|---|
| `GridConfig.small()` | 20 | 30 | 2 | Fast testing |
| `GridConfig.medium()` | 100 | 180 | 5 | Full simulation |

---

## 📊 Performance (JMH Benchmarks)

```
Benchmark                              Mode   Score   Units
GridGenerationBenchmark.generateSmall  avgt   3.2     ms/op
GridGenerationBenchmark.generateMedium avgt   18.7    ms/op
PathfindingBenchmark.plantToHospital   avgt   0.41    ms/op
PathfindingBenchmark.mediumGridTop3    avgt   2.8     ms/op
```

Run benchmarks:
```bash
java -cp target/voltaris-1.0.0-SNAPSHOT.jar com.voltaris.benchmark.GridGenerationBenchmark
```

---

## 🔬 Event System (Observer Pattern)

Voltaris uses a **12-type sealed event hierarchy** — all simulation activity flows through `GridEventBus`:

```java
GridEvent (sealed interface)
├── SimulationTickEvent      — heartbeat every tick
├── NodeFaultEvent           — node status → FAULT/OFFLINE
├── LineFaultEvent           — transmission line failure
├── WeatherUpdateEvent       — regional weather change
├── DemandChangeEvent        — consumer demand revision
├── GenerationChangeEvent    — plant/renewable output change
├── BatteryStateEvent        — charge/discharge event
├── IncidentCreatedEvent     — new incident registered
├── IncidentResolvedEvent    — incident closed
├── CrisisAlertEvent         — WARNING+ crisis classified
├── RecoveryActionEvent      — recovery measure taken
└── LoadSheddingEvent        — consumer load reduced
```

Subscribe from anywhere:
```java
simulator.eventBus().subscribe(CrisisAlertEvent.class, event -> {
    System.out.printf("CRISIS: %s — %.1f MW deficit (%.1f%%)%n",
        event.crisisLevel(), event.deficitMW(), event.deficitRatio() * 100);
});
```

---

## 💾 Persistence (SQLite)

Every simulation run is automatically persisted:

```sql
-- simulation_runs  — one row per run
-- simulation_ticks — one row per tick (energy balance snapshot)
-- incidents        — all incidents with lifecycle status
```

Access via repositories:
```java
SimulationRepository repo = new SimulationRepository();
List<Map<String, Object>> history = repo.findAllRuns();
```

DB file: `voltaris_simulation.db` (created in working directory on first run).

---

## 🧪 Test Coverage

```
Test Suite                     Tests   Status
──────────────────────────────────────────────
GridNodeTest                   18      ✅ Pass
TransmissionLineTest           18      ✅ Pass
PowerGridTest                  20      ✅ Pass
PowerPlantTest                 19      ✅ Pass
RenewableGeneratorTest         16      ✅ Pass
EnergyConsumerTest             19      ✅ Pass
BatteryStorageTest             18      ✅ Pass
EnergyAccountingEngineTest     14      ✅ Pass
DijkstraPathFinderTest         15      ✅ Pass
SyntheticGridGeneratorTest     15      ✅ Pass
SimulationClockTest             8      ✅ Pass
GridEventBusTest                8      ✅ Pass
GridIncidentTest                8      ✅ Pass
CrisisLevelTest                 6      ✅ Pass
RecoveryEngineTest              8      ✅ Pass
EconomicDispatchEngineTest      6      ✅ Pass
──────────────────────────────────────────────
TOTAL                         216      ✅ ALL PASS
```

---

## 🧰 Tech Stack

| Technology | Version | Purpose |
|---|---|---|
| **Java** | 17 | Core language (records, sealed interfaces, var) |
| **Maven** | 3.9.6 | Build, dependency management |
| **Java Swing** | JDK built-in | Desktop dashboard (dark theme) |
| **JUnit 5** | 5.10.2 | Unit + integration tests |
| **SLF4J + Logback** | 2.0.13 / 1.5.6 | Structured logging |
| **Jackson** | 2.17.1 | JSON scenario loading |
| **SQLite JDBC** | 3.46.0 | Simulation persistence |
| **JMH** | 1.37 | Microbenchmarks |

---

## ⚠️ Important Disclaimers

> **This is an educational simulation system only.**
> - It does NOT connect to any real electrical grid or infrastructure
> - Energy flow calculations use simplified models for demonstration purposes
> - Real power grid management requires licensed electrical engineering software
> - The priority allocation rules are configurable simulation defaults, not real utility policy

---

## 👨‍💻 Author

**Tejas** — [@Tejas64ig](https://github.com/Tejas64ig)

Built as a comprehensive Java internship/placement portfolio project demonstrating:
- Advanced multithreading and concurrency
- Graph algorithms and network modeling
- Event-driven architecture
- Clean architecture and design patterns
- Real-world simulation engineering

---

<div align="center">

**⚡ Voltaris — Because every megawatt matters ⚡**

*Star ⭐ this repo if you found it useful!*

</div>
