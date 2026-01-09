# **Product Documentation: Eco-Pulse Orchestrator**

**Submission:** \< PU CODE HACKATHON 3.0 \>

**Tagline:** Intelligent, ROI-Positive Kubernetes Scheduling for a Carbon-Neutral Cloud.

## ---

**1\. Executive Summary**

The **Eco-Pulse Orchestrator** is a transformative infrastructure tool designed to decouple cloud computing growth from carbon emissions. It is a custom Kubernetes Scheduler Extender that integrates real-time global energy grid data and spot-market pricing into the workload placement lifecycle.

By transforming Kubernetes from being "carbon-blind" to "carbon-aware," Eco-Pulse ensures that mission-critical applications run where the energy is cleanest **and** cheapest—leveraging wind, solar, and hydro surpluses across geographies. It enables enterprises to actively meet ESG (Environmental, Social, and Governance) goals, achieve FinOps optimization through energy arbitrage, and operate sustainable digital infrastructure with a positive Return on Investment (ROI).

## ---

**2\. Detailed Problem Statement**

### **The Context**

Cloud computing now has a carbon footprint exceeding that of the commercial airline industry. As enterprises move toward multi-region and hybrid cloud architectures, compute resources are often treated as infinite and uniform, ignoring the physical reality of the power grid and the economic reality of regional pricing.

### **The Core Technical Deficiencies**

Current standard Kubernetes schedulers decide where to run an application based solely on:

1. **Resource Availability** (CPU/RAM)  
2. **Constraints** (Affinity/Taints)  
3. **Latency** (Region proximity)

**Crucially missing is "Cost-Carbon Awareness."** The default scheduler will schedule a massive AI training job in a region running on expensive coal power, even if a neighboring region has a surplus of cheap wind energy.

### **The Consequences**

* **Unnecessary Emissions:** Massive amounts of avoidable CO2 are generated due to suboptimal geographical placement.  
* **Financial Waste:** Companies miss out on the lower "Spot Prices" often associated with renewable energy surpluses.  
* **Greenwashing:** Sustainability claims lack concrete, infrastructure-level enforcement.

## ---

**3\. The Solution: Eco-Pulse Orchestrator**

Eco-Pulse acts as an intelligent "Global Broker" for your workloads. It implements a **"Follow-the-Sun / Follow-the-Wind"** strategy for digital workloads.

### **Core Value Proposition**

1. **Carbon-Aware Placement (Day 1):** New applications land on the greenest available node instantly.  
2. **Dynamic Self-Healing (Day 2 \- Blue Ocean Feature):** A background monitor watches grid conditions. If a region's energy mix turns "dirty" (e.g., the sun sets), the system proactively migrates workloads to cleaner regions.  
3. **Economic Viability:** It uses a "Net Benefit Algorithm" to ensure that the cost of moving data (Egress fees) never exceeds the savings in energy bills.

### **Technical Approach: The Scheduler Extender Pattern**

Rather than rewriting Kubernetes, we utilize the **Kubernetes Scheduler Extender** pattern. The default Kube-scheduler makes HTTP calls to our external "Eco-Pulse Brain" service to ask for placement advice based on our custom logic.

## ---

**4\. Technical Architecture & The "Smart Score"**

This section details the decision-making logic that powers the orchestrator.

### **4.1 The Algorithmic Decision Engine (The Smart Score)**

Unlike simple tools that just "pick green," Eco-Pulse uses a mathematical model to determine if a migration is financially and environmentally sound. It calculates a **Net Benefit (NB)** score before every move.

#### **The Formula**

$$NB \= (S\_{energy} \+ S\_{carbon}) \- C\_{migration}$$  
Where:

* **$NB$:** Net Benefit Value. If $\> 0$, the migration is approved.  
* **$S\_{energy}$:** **Energy Cost Savings.**  
  * *Calculation:* $(Price\_{current} \- Price\_{target}) \\times JobDuration$  
  * *Logic:* "Clean" regions often have lower Spot Instance prices due to renewable surplus.  
* **$S\_{carbon}$:** **Carbon Value (ESG).**  
  * *Calculation:* $(Intensity\_{current} \- Intensity\_{target}) \\times EnergyUsage \\times \\alpha$  
  * *Logic:* Represents the monetary value of avoided pollution (Internal Carbon Pricing).  
* **$C\_{migration}$:** **Data Transfer Penalty.**  
  * *Calculation:* $DataSize\_{GB} \\times EgressFee\_{perGB}$  
  * *Logic:* The cost charged by cloud providers to move data between regions (e.g., $0.09/GB).

### **4.2 The "Green Path" Workflow (Step-by-Step)**

#### **Phase 1: Submission & Interception**

1. **User Action:** A user runs kubectl apply \-f my-app.yaml.  
2. **Scheduler Wake-up:** The Default Scheduler detects the pod and adds it to the queue.

#### **Phase 2: Filtering (The "Dirty" Check)**

3. **Extender Callout:** The Scheduler sends the node list to the Eco-Pulse Brain (/filter endpoint).  
4. **Data Ingestion:** The Brain queries:  
   * **Electricity Maps API:** Real-time Grid Intensity.  
   * **Cloud Pricing API:** Real-time Spot Instance pricing.  
5. **Logic:** Nodes in regions exceeding the "Dirty Threshold" (e.g., \>400g CO2) are immediately removed.

#### **Phase 3: Prioritizing (The ROI Calculation)**

6. **Extender Callout:** The Scheduler sends the filtered nodes to the Brain (/prioritize endpoint).  
7. **Scoring:** The Brain ranks the nodes using the **Net Benefit Formula** above. The node that offers the highest combined savings (Money \+ Carbon) receives the highest score (0-100).  
8. **Response:** The scores are returned to Kubernetes.

#### **Phase 4: Binding**

9. **Execution:** Kubernetes binds the Pod to the winning node (e.g., EU-North). The workload begins running on cheap, green energy.

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

*Pseudocode for the prioritization logic inside the Brain:*

Go

func CalculateScore(node Node, job Job) float64 {  
    // 1\. Get Real-time Data  
    carbonIntensity := GetGridData(node.Region) // e.g., 50g  
    spotPrice := GetPricingData(node.Region)    // e.g., $0.40/hr  
      
    // 2\. Calculate projected savings vs current location  
    energySavings := (CurrentPrice \- spotPrice) \* job.Duration  
      
    // 3\. Deduct Migration Cost (Data Egress)  
    migrationCost := job.DataSizeGB \* 0.09   
      
    // 4\. Return Final Net Benefit Score (0-100 scale)  
    netBenefit := energySavings \- migrationCost  
    return NormalizeScore(netBenefit)  
}

### **Step 3: Visualization Setup**

We deploy a Grafana dashboard that visualizes:

* **Live Map:** Regions glowing Red (Dirty) or Green (Clean).  
* **Financials:** Real-time counter of "Dollars Saved" via Spot Arbitrage.  
* **Impact:** Total $CO\_2$ avoided in tons.  
* **Latency:** Real-time scheduling latency to prove performance stability.

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