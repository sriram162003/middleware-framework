# AI-Based Robot Interoperability Middleware Framework

> **Mini Project — First Review | Implementation Status: 30%**

A Python middleware that lets robots from any vendor work together — without custom integration code for each pairing.

---

## Project Overview

Modern warehouses and facilities deploy robots from multiple vendors: AMRs, robotic arms, drones, service robots. Each speaks a different protocol. This middleware provides a **universal integration layer** so they all coordinate seamlessly.

```
Enterprise Apps (WMS / MES)
         │
    API Gateway
         │
  ┌──────▼───────┐
  │  MIDDLEWARE  │  ← you are here
  │  ─────────── │
  │  Registry    │   tracks all robots
  │  Event Bus   │   pub/sub backbone
  │  Orchestrator│   assigns tasks
  └──────┬───────┘
         │  adapters
   ┌─────┴──────┐
  AMR_01  ARM_01  DRONE_01
```

---

## Implementation Status

| Phase | Component | Status |
|-------|-----------|--------|
| **Phase 1** | System Architecture | ✅ Complete |
| **Phase 1** | Canonical Robot Model | ✅ Complete |
| **Phase 1** | Middleware Component Structure | ✅ Complete |
| **Phase 1** | Base Adapter Framework | ✅ Complete |
| Phase 2 | External Messaging (MQTT / Kafka) | 🔲 Planned |
| Phase 3 | Resource Coordination (doors, lifts) | 🔲 Planned |
| Phase 4 | AI Optimization Services | 🔲 Planned |
| Phase 5 | Testing & Evaluation | 🔲 Planned |

---

## Repository Structure

```
ai_robot_middleware/
├── models/
│   ├── robot_model.py          # Canonical data model (RobotProfile, RobotState, TaskRequest …)
│   └── __init__.py
├── middleware/
│   ├── server.py               # Top-level server — wires all components
│   ├── core/
│   │   └── registry.py         # Thread-safe robot registry
│   ├── messaging/
│   │   └── event_bus.py        # Pub/sub event bus (internal)
│   ├── adapters/
│   │   ├── base_adapter.py     # Abstract base — all adapters inherit this
│   │   └── simulated_adapter.py# Working simulated AMR adapter
│   └── orchestration/
│       └── engine.py           # Capability-based task orchestrator
├── config/
│   └── config.yaml             # Fleet & middleware configuration
├── tests/
│   ├── test_robot_model.py     # Unit tests — canonical model
│   ├── test_event_bus.py       # Unit tests — event bus
│   └── test_integration.py     # End-to-end integration test
└── requirements.txt
```

---

## Quick Start

```bash
# 1. Clone
git clone https://github.com/YOUR_USERNAME/ai_robot_middleware.git
cd ai_robot_middleware

# 2. Install dependencies
pip install -r requirements.txt

# 3. Run unit tests
python -m pytest tests/test_robot_model.py tests/test_event_bus.py -v

# 4. Run the end-to-end integration demo
python tests/test_integration.py
```

---

## Writing a New Adapter

Subclass `RobotAdapterBase` and implement two methods:

```python
from middleware.adapters.base_adapter import RobotAdapterBase, RobotCommand
from models import RobotState, RobotStatus, Pose, Position, BatteryState

class MyVendorAdapter(RobotAdapterBase):

    def fetch_state(self) -> RobotState:
        data = self._api.get_status()
        return RobotState(
            robot_id=self.profile.robot_id,
            pose=Pose(Position(data["x"], data["y"]), data["heading"]),
            status=RobotStatus(data["mode"]),
            battery=BatteryState(data["battery_pct"]),
        )

    def send_command(self, command: RobotCommand) -> bool:
        if command.command_type == "navigate":
            self._api.navigate_to(command.payload["x"], command.payload["y"])
            return True
        return False
```

---

## Roadmap

- **Phase 2** — MQTT / Kafka external messaging, REST API
- **Phase 3** — Shared resource coordinator (doors, lifts, chargers)
- **Phase 4** — AI scheduling (predictive analytics, anomaly detection)
- **Phase 5** — Large-scale simulation & evaluation

---

## References

- [Open-RMF](https://github.com/open-rmf/rmf)
- [VDA 5050 Standard](https://www.vda.de/en)
- [ROS2 Documentation](https://docs.ros.org/en/humble/)
