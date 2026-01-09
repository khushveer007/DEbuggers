Here is the **comprehensive, updated Product Documentation**. I have taken the Extensive Product Brief you uploaded and expanded the **Technical Architecture** section to include the **detailed mathematical formulas** (Net Benefit, Energy Arbitrage, and Carbon Value) that govern the scheduler's logic.

You can copy this directly into your project's README.md or submit it as your final documentation.

# ---

**Product Documentation: Eco-Pulse Orchestrator**

Submission: \< PU CODE HACKATHON 3.0 \>  
Version: 1.0 (Hackathon Prototype)  
Tagline: Intelligent, ROI-Positive Kubernetes Scheduling for a Carbon-Neutral Cloud.

## ---

**1\. Executive Summary**

The **Eco-Pulse Orchestrator** is a transformative infrastructure tool designed to decouple cloud computing growth from carbon emissions. It is a custom **Kubernetes Scheduler Extender** that integrates real-time global energy grid data and spot-market pricing into the workload placement lifecycle.

By transforming Kubernetes from being "carbon-blind" to "carbon-aware," Eco-Pulse ensures that mission-critical applications run where the energy is cleanest and cheapest—leveraging wind, solar, and hydro surpluses across geographies. It enables enterprises to actively meet ESG (Environmental, Social, and Governance) goals, achieve FinOps optimization through energy arbitrage, and operate sustainable digital infrastructure with a positive Return on Investment (ROI).

## ---

**2\. Detailed Problem Statement**

### **The Context**

Cloud computing now has a carbon footprint exceeding that of the commercial airline industry. As enterprises move toward multi-region architectures, compute resources are often treated as infinite and uniform, ignoring the physical reality of the power grid.

### **The Core Technical Deficiencies**

Current standard Kubernetes schedulers decide where to run an application based solely on:

1. **Resource Availability:** (CPU/RAM)  
2. **Constraints:** (Affinity/Taints)  
3. **Latency:** (Region proximity)

**Crucially missing is "Cost-Carbon Awareness."** The default scheduler will schedule a massive AI training job in a region running on expensive coal power, even if a neighboring region has a surplus of cheap wind energy.

### **The Consequences**

* **Unnecessary Emissions:** Massive amounts of avoidable CO2 are generated due to suboptimal geographical placement.  
* **Financial Waste:** Companies miss out on the lower "Spot Prices" often associated with renewable energy surpluses.  
* **Greenwashing:** Sustainability claims lack concrete, infrastructure-level enforcement.

## ---

**3\. The Solution: Eco-Pulse Orchestrator**

Eco-Pulse acts as an intelligent "Global Broker" for your workloads. It implements a **"Follow-the-Sun / Follow-the-Wind"** strategy.

### **Core Value Proposition**

1. **Carbon-Aware Placement (Day 1):** New applications land on the greenest available node instantly.  
2. **Dynamic Self-Healing (Day 2):** A background monitor watches grid conditions. If a region's energy mix turns "dirty" (e.g., the sun sets), the system proactively migrates workloads to cleaner regions.  
3. **Economic Viability:** It uses a "Net Benefit Algorithm" to ensure that the cost of moving data (Egress fees) never exceeds the savings in energy bills.

## ---

**4\. Technical Architecture & The "Smart Score"**

This section details the decision-making logic that powers the orchestrator.

### **4.1 The Algorithmic Decision Engine (The Smart Score)**

Unlike basic tools that just "pick green," Eco-Pulse uses a mathematical model to determine if a migration is financially and environmentally sound. It calculates a **Net Benefit (NB)** score before every move.

#### **The Core Formula**

The decision to migrate is governed by the inequality:

$$NB \= (S\_{energy} \+ S\_{carbon}) \- C\_{migration}$$  
**Logic:**

* If **$NB \> 0$**: **MIGRATE** (The move is profitable and green).  
* If **$NB \\le 0$**: **STAY** (The move is too expensive).

#### ---

**Equation Breakdown**

1\. Energy Cost Savings ($S\_{energy}$)  
This component calculates the direct cash savings on the cloud bill by moving to a cheaper region.

$$S\_{energy} \= (P\_{current} \- P\_{target}) \\times T\_{hours}$$

* **$P\_{current}$**: The hourly cost of the server in the current (dirty) region (e.g., $1.00/hr).  
* **$P\_{target}$**: The hourly cost in the target (clean) region. Clean regions often have lower Spot Prices due to renewable surplus (e.g., $0.40/hr).  
* **$T\_{hours}$**: The estimated remaining duration of the job (e.g., 10 hours).

2\. Carbon Value ($S\_{carbon}$)  
This component quantifies the "ESG Value" of the avoided pollution.

$$S\_{carbon} \= (I\_{current} \- I\_{target}) \\times E\_{kwh} \\times \\alpha$$

* **$I\_{current}$**: Carbon Intensity of the current region in $gCO\_2/kWh$ (e.g., 500g \- Coal).  
* **$I\_{target}$**: Carbon Intensity of the target region (e.g., 50g \- Wind).  
* **$E\_{kwh}$**: Total energy the pod will consume ($kW \\times Hours$).  
* **$\\alpha$ (Alpha)**: The "Internal Price of Carbon" (e.g., $0.005 per gram). This converts environmental impact into a dollar value for the formula.

3\. Migration Cost ($C\_{migration}$)  
This component calculates the penalty for moving data (the "Egress Fee").

$$C\_{migration} \= D\_{GB} \\times F\_{egress}$$

* **$D\_{GB}$**: The size of the data that needs to be transferred (in Gigabytes).  
* **$F\_{egress}$**: The cost charged by the cloud provider to move data out of a region (Standard AWS fee is approx. $0.09/GB).

### ---

**4.2 The Workflow**

1. **Interception:** A user deploys a Pod. The Default Scheduler pauses and calls the **Eco-Pulse Extender**.  
2. **Data Ingestion:** The Extender queries:  
   * **Electricity Maps API:** For real-time Grid Intensity ($gCO\_2/kWh$).  
   * **Cloud Pricing API:** For real-time Spot Instance pricing.  
3. **Filtering:** Nodes in regions exceeding the "Dirty Threshold" (e.g., \>400g $CO\_2$) are immediately removed.  
4. **Scoring:** The remaining nodes are ranked using the **Net Benefit Formula** above.  
5. **Placement:** Kubernetes binds the Pod to the highest-scoring (Greenest & Cheapest) node.

## ---

**5\. Hackathon Technology Stack**

We chose a stack that is open-source, industry-standard, and resilient.

| Layer | Technology | Justification |
| :---- | :---- | :---- |
| **Language** | **Go (Golang)** | The native language of Kubernetes. Essential for high-performance scheduling logic. |
| **Infrastructure** | **KinD (K8s in Docker)** | Simulates a multi-region cloud cluster locally on a laptop (Zero cost). |
| **Data Source** | **Electricity Maps API** | The gold standard for real-time grid emissions data. |
| **Metrics** | **Prometheus** | Scrapes custom metrics (e.g., carbon\_saved\_total, migration\_roi). |
| **Visualization** | **Grafana** | Displays the real-time "Sustainability & FinOps" dashboard. |
| **Workload** | **Nginx / AI Job** | Stateless pods used to demonstrate rapid, low-cost migration. |

## ---

**6\. Implementation Guide & Setup**

### **Step 1: Simulating the Multi-Region Cloud**

We use KinD to create a 3-node cluster and label nodes to simulate geography.

Bash

\# Label nodes to represent AWS Regions  
kubectl label node kind-worker region=us-east-1  \# Dirty (Coal)  
kubectl label node kind-worker2 region=eu-north-1 \# Clean (Wind)

### **Step 2: The Logic Implementation (Go)**

*Pseudocode for the prioritization logic:*

Go

func CalculateScore(node Node, job Job) float64 {  
    // 1\. Get Real-time Data  
    carbonIntensity := GetGridData(node.Region) // e.g., 50g  
    spotPrice := GetPricingData(node.Region)    // e.g., $0.40/hr  
      
    // 2\. Calculate projected savings vs current location  
    energySavings := (CurrentPrice \- spotPrice) \* job.Duration  
      
    // 3\. Calculate Carbon Value (Alpha \= $0.01 per gCO2)  
    carbonDelta := CurrentIntensity \- carbonIntensity  
    carbonValue := carbonDelta \* job.EnergyUsage \* 0.01

    // 4\. Deduct Migration Cost (Data Egress)  
    migrationCost := job.DataSizeGB \* 0.09   
      
    // 5\. Return Final Net Benefit  
    return (energySavings \+ carbonValue) \- migrationCost  
}

### **Step 3: Visualization**

We deploy a Grafana dashboard that visualizes:

* **Live Map:** Regions glowing Red (Dirty) or Green (Clean).  
* **Financials:** Real-time counter of "Dollars Saved" via Spot Arbitrage.  
* **Impact:** Total $CO\_2$ avoided in kilograms.

## ---

**7\. Business Impact & Future Scope**

### **Business Value**

* **Tangible ROI:** We prove that sustainability isn't a cost center; it's a cost *saver* via energy arbitrage.  
* **Regulatory Assurance:** Prepares businesses for emerging carbon taxes and mandatory ESG reporting (EU CSRD).  
* **Brand Reputation:** Demonstrates verifiable commitment to sustainability.

### **Future Roadmap**

1. **AI-Driven Forecasting:** Move from reactive (current weather) to predictive (24h forecast) scheduling to move workloads *before* a grid gets dirty.  
2. **Stateful Support:** Implement "Follower Database" patterns to minimize data egress costs for stateful applications.  
3. **Hardware Awareness:** Integrate with **Kepler** to measure per-pod CPU power draw (Watts) for 100% accurate carbon calculations.