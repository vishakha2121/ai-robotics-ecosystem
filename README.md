# 🤖 AI Autonomous Robotics Ecosystem

A full-stack **multi-agent robotics simulation platform** where four different types of autonomous agents — **Warehouse Robots (AMRs)**, **Drones**, **Humanoids**, and **Industrial Robotic Arms** — collaborate in real time using a **shared memory layer** and a **shared planning engine** powered by **Google Gemini API**.

This project is a **practice-level, CPU-only simulation** (no real hardware, no GPU required). The goal is to demonstrate the core architecture of an autonomous multi-robot ecosystem: shared state, distributed task planning, inter-agent collaboration, and live observability through a modern web UI.

---

## 🎯 Problem Statement

In modern smart warehouses and factories, heterogeneous robots (different shapes, capabilities, sensors) must work together. Each robot has limited knowledge, limited battery, and specific skills. The challenge is:

- **How do robots share what they know?** → Shared Memory
- **How do they decide who does what?** → Shared Planner
- **How do they help each other?** → Collaboration Protocol
- **How do we watch all this live?** → Real-time Dashboard

This project simulates all four.

---

## 🧠 Core Concepts Implemented

### 1. Shared Memory
A centralized in-memory + persisted store (SQLite) where every robot publishes and reads state — position, battery, current task, observations, and inter-robot messages. This mimics a "collective brain".

### 2. Shared Planning Engine
A hybrid planner that combines:
- **Rule-based logic** (priority, proximity, battery thresholds)
- **Gemini API** for natural-language task decomposition and intelligent allocation (with automatic fallback if the API is unavailable)

### 3. Autonomous Agents
Each robot is a simulated autonomous entity with:
- Type-specific behavior (drone flies, arm picks, AMR moves, humanoid assists)
- Battery drain + recharge logic
- Task acceptance, execution, and completion
- Collaboration requests to nearby robots

### 4. Collaboration Protocol
Robots can **hand off** tasks, **request help**, and **share observations** through an event bus. Every collaboration is logged.

### 5. Live Observability
A React dashboard shows:
- All robots on a 2D warehouse map (live movement)
- Task board (pending / in-progress / done)
- Shared memory viewer
- Real-time event stream (WebSocket)
- Gemini planner panel with plan history
- Battery, status, and collaboration analytics

---

## 🏗️ Architecture (High Level)

```text
┌─────────────────────────────────────────────────────────────┐
│                     REACT FRONTEND (Vite)                    │
│  Dashboard │ Robots │ Tasks │ Memory │ Map │ Planner │ Events│
└──────────────────────────┬──────────────────────────────────┘
                           │ REST + WebSocket
┌──────────────────────────▼──────────────────────────────────┐
│                     FASTAPI BACKEND                          │
│  ┌─────────────┐  ┌──────────────┐  ┌────────────────────┐  │
│  │  REST APIs  │  │  WebSocket   │  │  Simulation Loop   │  │
│  └──────┬──────┘  └──────┬───────┘  └─────────┬──────────┘  │
│         │                │                    │             │
│  ┌──────▼────────────────▼────────────────────▼──────────┐  │
│  │              CORE ENGINE (Orchestrator)                │  │
│  │  • Shared Memory Manager                               │  │
│  │  • Robot Engine (state machine per robot)              │  │
│  │  • Planner (rules + Gemini)                            │  │
│  │  • Event Bus                                           │  │
│  │  • Collaboration Handler                               │  │
│  └──────────────────────┬─────────────────────────────────┘  │
│                         │                                    │
│  ┌──────────────────────▼─────────────────────────────────┐  │
│  │         SQLAlchemy ORM  →  SQLite (robotics.db)        │  │
│  │  robots │ tasks │ memory_entries │ events │ plans      │  │
│  └────────────────────────────────────────────────────────┘  │
└──────────────────────────────────────────────────────────────┘
                           │
                    ┌──────▼───────┐
                    │  Gemini API  │  (optional, with fallback)
                    └──────────────┘



                    ai-robotics-ecosystem/
├── backend/
│   ├── app/
│   │   ├── models/          # SQLAlchemy models
│   │   ├── schemas/         # Pydantic schemas
│   │   ├── core/            # engine, planner, memory, event bus
│   │   ├── services/        # business logic
│   │   ├── api/v1/          # REST + WebSocket routes
│   │   └── utils/           # helpers, seed data, logger
│   ├── tests/
│   └── main.py
├── frontend/
│   └── src/
│       ├── components/      # layout, dashboard, robots, tasks, memory…
│       ├── pages/
│       ├── hooks/
│       ├── api/
│       ├── context/
│       └── utils/
├── database/
│   ├── schema.sql
│   └── seed.sql
└── README.md