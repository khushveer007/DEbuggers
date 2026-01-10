---
stepsCompleted: [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11]
inputDocuments:
  - _bmad-output/planning-artifacts/product-brief-DEbuggers-2026-01-10.md
  - _bmad-output/planning-artifacts/research/technical-consolidated-feasibility-report-2026-01-10.md
  - Details/Extensive Product Brief.md
  - Details/Extensive Product Brief v2.md
  - Details/Extensive Product Brief v3.md
  - Details/Problem 3 Building a Sustainable Cloud-Native.md
documentCounts:
  briefs: 4
  research: 1
  brainstorming: 0
  projectDocs: 1
workflowType: 'prd'
lastStep: 11
---

# Product Requirements Document - DEbuggers

**Author:** Codespace
**Date:** 2026-01-10

## Executive Summary

The **Eco-Pulse Orchestrator** transforms Kubernetes from "carbon-blind" to "carbon-aware," enabling a "Follow-the-Sun / Follow-the-Wind" strategy. It optimizes workload placement for both sustainability and cost (FinOps) by integrating real-time grid data and spot pricing, ensuring mission-critical applications run on the cleanest, cheapest energy without sacrificing performance. This solution addresses the growing carbon footprint of cloud computing and empowers Cloud Platform Engineers and FinOps Leads to meet sustainability goals with concrete, infrastructure-level action.

### What Makes This Special

At the core of Eco-Pulse is the **Net Benefit Algorithm**, a mathematical model (`NB = (Savings + CarbonValue) - (Migration + EgressCost)`) that guarantees every workload migration yields a positive Return on Investment (ROI). Unlike basic tools that simply "pick green," Eco-Pulse rigorously weighs the environmental value and energy savings against data egress costs and latency impacts, ensuring that "going green" is also economically and operationally viable.

## Project Classification

**Technical Type:** `api_backend`
**Domain:** `energy`
**Complexity:** `high`
**Project Context:** Brownfield - extending existing system

## Success Criteria

### User Success

*   **Zero-Config "Green" Deployment:** "Alex" (DevOps) can run `kubectl apply` without modifying manifests, yet automatically achieve carbon-aware placement.
*   **Trust & Verification:** Users can immediately verify impact via the Grafana dashboard, seeing the "Green" gauge active and "Money Saved" counter incrementing.
*   **Performance Confidence:** Application latency remains within SLAs (<50ms penalty), building trust that "green" does not mean "slow".

### Business Success

*   **Sustainability Compliance:** Produce audit-ready data for CSRD/ESG reporting (gCO2 avoided).
*   **FinOps Optimization:** Reduce cloud compute costs by >15% via Spot Instance arbitrage in renewable surplus regions.
*   **Operational Resilience:** Demonstrate zero downtime during "Follow-the-Sun" migrations.

### Technical Success

*   **Scheduling Latency:** Scheduler Extender decision overhead < 200ms per pod.
*   **Net Benefit Validity:** The algorithm correctly blocks migrations where Egress Cost > Energy Savings.
*   **Fail-Open Design:** System defaults to standard Kubernetes scheduling if the Extender or Carbon API is unavailable.

### Measurable Outcomes

*   **Carbon Intensity Reduction:** > 40% vs. baseline (US-East).
*   **Cloud Bill Savings:** > 15% reduction (Spot vs On-Demand).
*   **Migration ROI:** Positive (> $0) for >95% of migrations.
*   **User Latency Penalty:** < 50ms added.

## Product Scope

### MVP - Minimum Viable Product (Hackathon)

*   **Core Logic:** Kubernetes Scheduler Extender (Go) with Net Benefit Algorithm.
*   **Data Source:** Integration with Electricity Maps API and Mock Pricing Service.
*   **Infrastructure:** Simulated Multi-Region Cluster using KinD (3 nodes, region labels).
*   **Visualization:** Grafana Dashboard showing Real-time Carbon Intensity, Money Saved, and Map View.

### Growth Features (Post-MVP)

*   **Real Multi-Cloud Support:** Deployment across actual AWS/GCP regions (not just KinD simulation).
*   **Advanced Spot Orchestration:** Integration with Karpenter or similar tools for dynamic node provisioning.
*   **User Constraints:** Allow users to set "Max Latency" or "Min Cost Savings" policies via CRDs.

### Vision (Future)

*   **Predictive "Weather" Routing:** 24h forecasting to move workloads *before* grid intensity spikes.
*   **Stateful Workload Migration:** Automated replication and failover for databases (Follower-Data patterns).
*   **Hardware-Level Awareness:** Integration with Kepler/eBPF for socket-level power metrics.

## User Journeys

### Journey 1: Alex - The "Invisible Hero" Deployment
**Persona:** Senior DevOps Engineer
**Goal:** Deploy a resource-intensive AI training job without blowing the budget or carbon quota.

Alex is under pressure. The data science team just handed him a massive training workload that needs to run *now*, but the CFO is watching cloud spend like a hawk, and the company just published a "Net Zero" pledge. He dreads the usual dance of manually checking Spot prices in `us-east-1` vs `eu-west-1` and calculating network latency.

He decides to trust Eco-Pulse. He takes the standard manifest and simply runs `kubectl apply -f ai-training-job.yaml`. He winces, waiting for an error or a delay.

**The "Magic" Moment:**
The terminal returns `deployment created` instantly. He checks `kubectl get pods -o wide`. The pods are spinning up in `eu-north-1` (where wind is currently peaking). He didn't have to add a `nodeSelector` or touch a config map. He logs into the dashboard and sees the "Projected Savings" for this specific job: **$450 and 80kg CO2**. He sends a screenshot to the Slack channel with the caption: "Deployed. Under budget. Green."

**Requirements Revealed:**
*   **Transparent Interception:** Scheduler Extender must act on standard pod submissions.
*   **Zero-Latency Feel:** Async data fetching is critical so the `kubectl apply` doesn't hang.
*   **Job-Level Feedback:** Needs to calculate savings *per job* for validation.

### Journey 2: Sarah - Evidence-Based Sustainability
**Persona:** Head of FinOps / Sustainability Lead
**Goal:** Specific, audit-ready data for the Q1 ESG Report.

It's reporting week. Sarah usually spends three days mashing up AWS billing CSVs with generic annual carbon intensity averages. It's inaccurate and she knows it—she's essentially guessing. The board is asking for *concrete* numbers this quarter.

She logs into the Eco-Pulse Grafana Dashboard. Instead of vague averages, she sees a time-series graph: "Real-time Avoided CO2". She drills down to the `ai-team` namespace.

**The "Magic" Moment:**
She sees a spike from Tuesday. The system explains: *"Migrated 500 cores from US-East (Coal) to EU-West (Wind) for 6 hours. Net Savings: 1.2 Tons CO2."* She hits "Export PDF". Her report is done in 5 minutes, backed by data that stands up to audit.

**Requirements Revealed:**
*   **Namespace-level Attribution:** Metrics must be tagged by Kubernetes Namespace.
*   **Historical Retention:** Prometheus/Grafana must store data long enough for quarterly reporting.
*   **"Avoided" Metric Logic:** Must calculate the *delta* between the "default" region and the "green" region to show value.

### Journey 3: Sam - The "Fire Drill" (Reliability Test)
**Persona:** Platform Admin (SRE)
**Goal:** Verify that Eco-Pulse won't take down the production cluster if the internet breaks.

Sam effectively manages the platform's "reliability budget." He likes the *idea* of Eco-Pulse, but he's terrified of a "Scheduler Extender" becoming a single point of failure. If the Extender crashes, no new pods can start. The cluster dies.

He decides to run a "Chaos Monkey" test. He blocks all outbound network traffic from the Eco-Pulse Brain pod, simulating an outage of the Electricity Maps API. He then attempts to scale up a mission-critical Nginx deployment.

**The "Magic" Moment:**
He watches the logs. The Eco-Pulse Brain screams errors: `Failed to fetch grid data`. The urgency rises. But then, he sees the Nginx pods turn `Running`. The system realized it couldn't get green data and immediately fell back to the standard Kubernetes scheduler. The pods launched in the default region. The platform stayed up. Sam nods—it's safe for Prod.

**Requirements Revealed:**
*   **Fail-Open Architecture:** If the Extender errors or times out, the pod must still be scheduled.
*   **Error Observability:** Clear logs and metrics when "Green Mode" is bypassed due to failure.
*   **Chaos Resilience:** System must handle timeouts gracefully (< 200ms).

### Journey Requirements Summary

*   **Core Capabilities:** Zero-touch pod interception, Region-aware carbon/cost calculation, Fail-open error handling.
*   **Data & Analytics:** Per-namespace metrics, Calculate "Avoided" CO2/Cost delta, Long-term history.
*   **UX/Ops:** 200ms latency budget, verifiable fallback behavior.

## Domain-Specific Requirements

### Energy Domain Compliance & Regulatory Overview

While operating in the high-complexity Energy domain, this MVP focuses on **demonstrable safety** and **audit transparency** rather than full certification. The goal is to prove the *capability* to support future regulatory reporting (CSRD/SEC) without implementing the entire legal framework today.

### Key Domain Concerns

*   **Grid Reliability (Safety):** The scheduler must NEVER compromise cluster stability for the sake of sustainability.
*   **Auditability (Greenwashing):** All carbon claims must be backed by traceable, time-series data, not estimates.
*   **Transparency:** The logic for "Green" vs "Dirty" must be mathematically explicit (Net Benefit Score), not a "black box" AI decision.

### Compliance Requirements

*   **Metric Accuracy:** Carbon Intensity must be fetched from a reputable source (Electricity Maps) and stored with timestamps.
*   **Separation of Concerns:** The "Scheduler Extender" pattern ensures that energy logic is decoupled from the core Kubernetes Control Plane.
*   **Data Retention:** Prometheus metrics must be retained for the duration of the demo (or 24h) to show historical trends "Before vs After".

### Industry Standards & Best Practices

*   **Fail-Open Architecture:** If the Carbon API fails, the Grid is unreachable, or the Extender crashes, the system **MUST** default to standard Kubernetes behavior. This is the #1 safety requirement for infrastructure software.
*   **Green Software Principles:** We align with the Green Software Foundation's principle of "Carbon Awareness" (doing more when the energy is clean).

### Required Expertise & Validation

*   **Validation Method:** "Chaos Testing" is required to prove safety. We must demonstrate pulling the plug on the API and seeing the cluster survive.
*   **Domain Knowledge:** Basic understanding of "Marginal Emissions" vs "Average Emissions" (we use Average for MVP simplicity, as provided by the free API).

### Implementation Considerations

*   **API Rate Limits:** The free tier of Electricity Maps has limits. Implementation must cache data to avoid throttling, which could look like a system failure.
*   **Mocking Strategy:** For the hackathon demo, we must have a "Mock Grid Mode" to force "High Carbon" and "Low Carbon" states on demand, proving the scheduler reacts correctly without waiting for real-world weather to change.

## Innovation & Novel Patterns

### Detected Innovation Areas

*   **Algorithmic Carbon-Financial Arbitrage:**
    Unlike tools that optimize for *either* cost (Spot.io) *or* carbon (Carbon Aware K8s), Eco-Pulse optimizes for the **intersection** using the Net Benefit formula:
    `NB = ($Savings + $CarbonValue) - ($MigrationCost + $EgressCost)`
    This quantifies the "exchange rate" between CO2 and USD, allowing for automated, rational decision-making.

*   **"Fail-Open" Sustainability Architecture:**
    Eco-Pulse introduces a "Sustainability Sidecar" pattern to the scheduler. Crucially, it is designed to **fail open**. If the grid API is down, or the latency calculation times out (>200ms), the pod is scheduled normally. This removes the risk penalty usually associated with green experiments.

### Market Context & Competitive Landscape

*   **Existing Players:**
    *   **KEDA / Carbon Aware KEDA:** Scales workloads based on carbon, but doesn't handle *placement* (Region A vs Region B) or cost.
    *   **Karpenter:** Excellent for cost/spot nodes, but blind to carbon intensity.
    *   **Cloud Providers (AWS/GCP):** Offer "Green Region Picker" in console, but no automated programmable enforcement at the pod level.
*   **Eco-Pulse Niche:** The only open-source, policy-driven "Arbitrage Engine" that sits in the critical path of scheduling without adding availability risk.

### Validation Approach

*   **The "Equation Test":** We validate the innovation by feeding the scheduler scenarios where "Green" is "Expensive" (high data egress). The system *must* reject the migration, proving it respects the Net Benefit logic over blind idealism.
*   **The "Unplug Test":** We validate the "Fail-Open" innovation by severing the network connection to Electricity Maps. The system *must* successfully schedule pods in the default region, proving safety.

### Risk Mitigation

*   **"Bad Math" Risk:** If the $CarbonValue is set too high, we bankrupt the user. *Mitigation:* Cap the "Carbon Credit" value in the config map (e.g., max $100/ton).
*   **"Thundering Herd" Risk:** If a region becomes green, everyone migrates at once. *Mitigation:* Random jitter and `max_migration_rate` limits.

## API Backend Specific Requirements

### Project-Type Overview

Eco-Pulse is a **Kubernetes Scheduler Extender Service**. It operates as a synchronous webhook called by the Kube-Scheduler Control Plane. It must be stateless, highly performant, and fail-safe.

### Technical Architecture Considerations

*   **Communication Protocol:** HTTP/1.1 (Synchronous).
*   **Serialization:** JSON (Strict K8s Schema).
*   **State Management:** Stateless. Configuration loaded from K8s ConfigMaps (hot-reloading enabled). Data fetched from Redis (cached).

### Endpoint Specifications

| Method | Path       | Purpose                          | Input                 | Output                        |
| :----- | :--------- | :------------------------------- | :-------------------- | :---------------------------- |
| `POST` | `/filter`  | Core logic to approve/deny nodes | `ExtenderArgs` (JSON) | `ExtenderFilterResult` (JSON) |
| `GET`  | `/health`  | Liveness/Readiness probe         | None                  | `200 OK`                      |
| `GET`  | `/metrics` | Prometheus metrics scrape target | None                  | Prometheus Text Format        |

### Authentication Model

*   **Internal (Scheduler -> Extender):** Plain HTTP (MVP). Security handled via Kubernetes NetworkPolicies (out of scope for app code).
*   **External (Extender -> APIs):** API Key auth for Electricity Maps, injected via Kubernetes Secrets.

### Implementation Considerations

*   **Hard Timeout Safety:** The generic `DoFilter()` function must wrap logic in a context with a **190ms timeout**. If the context cancels, the handler MUST return `{ "NodeNames": [all_nodes] }` (Allow All) and log the failure.
*   **Concurrency:** Go routines must be used carefully. The service must handle bursts of pod creation ("Thundering Herd") without exhausting memory.

## Project Scoping & Phased Development

### MVP Strategy & Philosophy

**MVP Approach:** **"Steel Thread" Problem Solver**
We will build a single, unbreakable path from "Pod Submission" to "Green Placement." We will mock the *infrastructure* (using KinD) but build the *logic* (Net Benefit Algorithm) for production reality. This ensures the **solution** to the problem is demonstrated without getting bogged down in real-world cloud complexity.
**Resource Requirements:** 2 Developers (1 Backend/Go, 1 Platform/Grafana).

### MVP Feature Set (Phase 1 - The Hackathon)

**Core User Journeys Supported:**
1.  **Alex's Zero-Touch Deployment:** Standard pods seamlessly landing on green nodes.
2.  **Sarah's Audit:** Real-time dashboard showing "Avoided CO2" for those specific pods.
3.  **Sam's Safety Test:** System failing open when the "grid" is down.

**Must-Have Capabilities:**
*   **CORE:** Go-based Scheduler Extender with `POST /filter` endpoint.
*   **LOGIC:** Net Benefit Calculation (`(Savings + Carbon) - Cost`).
*   **DATA:** Electricity Maps API integration (with caching/mocking).
*   **OPS:** Basic Grafana Dashboard with 3 key gauges (CO2, $, Latency).
*   **INFRA:** KinD Cluster with 3 nodes simulating 3 regions (e.g., `us-east`, `eu-west`, `eu-north`).

### Post-MVP Features (The Vision)

**Phase 2 (Growth - Real Cloud):**
*   Actual AWS/GCP Multi-Region connectivity.
*   Advanced Spot Instance orchestration (integrating with Karpenter).
*   User Policy CRDs (allow users to define `maxLatency` per workload).

**Phase 3 (Expansion - Intelligence):**
*   Predictive "Weather Routing" (schedule for tomorrow's wind).
*   Stateful workload migration (Live Migration for DBs).

### Risk Mitigation Strategy

*   **Technical Risk (Latency):** Addressed by **Hard Timeout (190ms)** in code.
*   **Market Risk (Trust):** Addressed by **Real-time Dashboard** showing the math.
*   **Market Risk (Trust):** Addressed by **Real-time Dashboard** showing the math.
*   **Resource Risk (Time):** Addressed by **Mocking Grid Data** if API integration proves flaky/slow during dev.

## Functional Requirements

### Core Scheduling Logic
*   **FR1:** The system can intercept Pod creation requests via the Kubernetes Scheduler Extender API (`POST /filter`).
*   **FR2:** The system can read pod resource requirements (CPU/RAM) from the input request.
*   **FR3:** The system can determine the eligible regions for a workload based on the node list provided by Kubernetes.
*   **FR4:** The system can return a filtered list of nodes to the Kubernetes Scheduler.

### Cost-Carbon Arbitrage (The "Net Benefit" Engine)
*   **FR5:** The system can fetch real-time Carbon Intensity (gCO2eq/kWh) for each eligible region.
*   **FR6:** The system can fetch (or mock) the real-time Spot Price for the required instance type in each region.
*   **FR7:** The system can calculate the "Net Benefit Score" for each region using the formula: `(Price Delta + Carbon Delta) - (Latency Penalty + Egress Cost)`.
*   **FR8:** The system can select the region with the highest positive Net Benefit Score.

### System Reliability & Safety
*   **FR9:** The system can enforce a hard timeout (190ms) on all scheduling decisions.
*   **FR10:** The system can "Fail Open" (allow all nodes) if the decision logic times out or errors.
*   **FR11:** The system can "Fail Open" if the Electricity Maps API is unreachable.
*   **FR12:** The system can cache grid data to prevent API throttling.

### Observability & Audit
*   **FR13:** The system can expose Prometheus metrics for "Carbon Avoided" (difference between default and selected region).
*   **FR14:** The system can expose Prometheus metrics for "Estimated Cost Savings".
*   **FR15:** The system can expose technical metrics (Request Latency, Error Rate, Fail-Open Count).
*   **FR16:** The system can log every scheduling decision (Winner vs Losers) for debugging.

### Configuration & Mocking
*   **FR17:** The system can read configuration (API Keys, Carbon Price Weight) from a Kubernetes ConfigMap.
*   **FR17:** The system can read configuration (API Keys, Carbon Price Weight) from a Kubernetes ConfigMap.
*   **FR18:** The system can be switched to "Mock Mode" to force specific Grid states (High/Low Carbon) for demonstration purposes.

## Non-Functional Requirements

### Reliability (Fail-Open)

*   **NFR1 (Fail-Open):** The system MUST default to "Allow All" (standard scheduling) if the backend service returns a 5xx error, times out, or is unreachable.
*   **NFR2 (Circuit Breaking):** The Scheduler Client configuration should use a `timeoutSeconds` of 1s to prevent the Kube-Scheduler from hanging indefinitely if the Extender is partitioned.

### Performance (Latency Budget)

*   **NFR3 (Response Time):** The Extender Service MUST return a decision within **190ms** (99th percentile).
*   **NFR4 (Concurrency):** The service MUST support processing at least 50 concurrent pod scheduling requests without crashing (Memory Usage < 500MB).

### Observability (Audit Trail)

*   **NFR5 (Decision Logging):** Every decision (Target Node, Region Selected, Net Benefit Score) MUST be logged in a structured JSON format to stdout for auditability.
*   **NFR6 (Metric Retention):** Prometheus metrics for "Carbon Saved" MUST be persisted for at least 24 hours to support the live demo/presentation.

### Security (Basic Hygiene)

*   **NFR7 (Secrets Management):** External API Keys (Electricity Maps) MUST be injected via Kubernetes Secrets and NOT hardcoded in the container image or ConfigMap.









