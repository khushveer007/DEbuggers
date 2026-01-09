This document serves as the comprehensive product documentation for the **Eco-Pulse Orchestrator**, developed for the \< PU CODE HACKATHON 3.0 \>.

# ---

**Product Documentation: Eco-Pulse Orchestrator**

Version: 1.0 (Hackathon Prototype)  
Tagline: Intelligent Kubernetes Scheduling for a Carbon-Neutral Cloud.

## ---

**1\. Executive Summary**

The **Eco-Pulse Orchestrator** is a transformative infrastructure tool designed to decouple cloud computing growth from carbon emissions. It is a custom Kubernetes Scheduler Extender that integrates real-time global energy grid data into the workload placement lifecycle.

By transforming Kubernetes from being "carbon-blind" to "carbon-aware," Eco-Pulse ensures that mission-critical applications run where the energy is cleanest—leveraging wind, solar, and hydro surpluses across geographies. It enables enterprises to actively meet ESG (Environmental, Social, and Governance) goals, achieve FinOps optimization through energy arbitrage, and operate sustainable digital infrastructure without sacrificing performance.

## ---

**2\. Detailed Problem Statement**

### **The Context**

Cloud computing now has a carbon footprint exceeding that of the commercial airline industry. As enterprises move toward multi-region and hybrid cloud architectures, compute resources are often treated as infinite and uniform.

### **The Core Technical Deficiencies**

Current standard Kubernetes schedulers (the component responsible for deciding where to run an application "Pod") make decisions based on a limited set of constraints:

1. **Resource Availability:** Does Node A have enough CPU and RAM?  
2. **Affinity/Anti-affinity:** Should these apps run together or apart?  
3. **Network Latency:** Is the region close enough to the user?

**Crucially missing is Carbon Awareness.** The default scheduler is completely blind to the energy source powering the underlying hardware. It will happily schedule a massive AI training job at noon in a region powered by coal plants, even if another region is currently experiencing surplus solar energy generation.

### **The Negative Outcomes**

* **Unnecessary Emissions:** Massive amounts of avoidable CO2 are generated due to suboptimal geographical placement.  
* **Greenwashing vs. Action:** Companies struggle to move beyond high-level sustainability pledges to concrete, infrastructure-level action.  
* **FinOps Inefficiency:** Renewable surplus energy is often cheaper; ignoring it means missing out on potential cost savings.

## ---

**3\. The Solution: Eco-Pulse Orchestrator**

Eco-Pulse acts as an intelligent "energy broker" sitting between application developers and the raw cloud infrastructure. It implements a **"Follow-the-Sun / Follow-the-Wind"** strategy for digital workloads.

### **Core Value Proposition**

1. **Carbon-Aware Placement (Day 1):** When a new application is deployed, it lands on the greenest available node instantly.  
2. **Dynamic Self-Healing (Day 2 \- Blue Ocean Feature):** Unlike basic solutions that only handle initial placement, Eco-Pulse continuously monitors grid conditions. If a region's energy mix turns "dirty" (e.g., the sun sets and peaker plants turn on), the system detects this drift and proactively migrates workloads to cleaner regions without downtime.

### **Technical Approach: The Scheduler Extender Pattern**

Rather than forking or rewriting the complex Kubernetes source code, we utilize the **Kubernetes Scheduler Extender** pattern. This is an official, non-intrusive way to add custom logic. The default Kube-scheduler is configured to make HTTP calls to our external "Eco-Pulse Brain" service during specific phases of the scheduling cycle to ask for advice.

## ---

**4\. Detailed Technical Architecture & Workflow**

This section details exactly how a Pod gets from a user's laptop to a green server.

### **High-Level Components**

| Component | Type | Role |
| :---- | :---- | :---- |
| **User/GitOps** | Input | Submits a standard Kubernetes Deployment YAML. |
| **K8s API Server** | Control Plane | The central entry point for all cluster commands. |
| **Default Scheduler** | Control Plane | The standard K8s component that manages the binding lifecycle. |
| **Eco-Pulse Brain** | **Custom Go Service** | The Scheduler Extender. It runs as a Pod and exposes HTTP endpoints (/filter, /prioritize). |
| **Carbon Data Layer** | External API | Provides real-time grid intensity (gCO2eq/kWh). (e.g., Electricity Maps). |
| **Prometheus** | Monitoring | Scrapes metrics from the cluster and the Eco-Pulse Brain. |
| **Grafana** | Visualization | Displays the real-time sustainability dashboard. |

### **The "Green Path" Workflow (Step-by-Step)**

#### **Phase 1: Submission & Interception**

1. **User Action:** A user runs kubectl apply \-f my-app.yaml. The request goes to the K8s API Server.  
2. **Scheduler Wake-up:** The Default Scheduler notices an "unscheduled Pod" and adds it to its queue.  
3. **The Scheduling Cycle Begins:** The Default Scheduler looks at all available nodes in the cluster (e.g., 50 nodes across 3 regions: US-East, EU-West, Asia-South).

#### **Phase 2: Filtering (The "Dirty" Check)**

4. **Extender Callout:** Before doing its own checks, the Default Scheduler makes an HTTP POST request to the Eco-Pulse Brain's /filter endpoint, sending the list of all 50 nodes.  
5. **Data Fetch:** The Eco-Pulse Brain receives the list. It identifies the geographic region labels on the nodes. It immediately queries the **Carbon Data Layer** for real-time intensity of those regions.  
6. **Logic Application:**  
   * *Scenario:* US-East is currently at 600g CO2/kWh (coal-heavy). EU-West is at 150g CO2/kWh (wind-heavy). Asia-South is at 300g CO2/kWh.  
   * *Threshold Check:* The Brain has a configurable threshold (e.g., "Reject anything over 500g").  
7. **Response:** The Brain returns a filtered list to the Default Scheduler, completely removing all US-East nodes from consideration.

#### **Phase 3: Prioritizing (The "Greenest" Score)**

8. **Extender Callout:** The Default Scheduler now takes the filtered list (EU-West and Asia-South nodes) and calls the Eco-Pulse Brain's /prioritize endpoint.  
9. **Scoring Logic:** The Brain calculates a score between 0-100 for the remaining nodes based on inverse carbon intensity.  
   * EU-West nodes (cleanest) get a score of **95**.  
   * Asia-South nodes (moderate) get a score of **60**.  
10. **Response:** The Brain returns these scores to the Default Scheduler.

#### **Phase 4: Binding & Execution**

11. **Final Decision:** The Default Scheduler combines these scores with its own internal scoring. Since EU-West has a massively higher score, it is selected as the winner.  
12. **Binding:** The Pod is assigned to a node in EU-West. The Kubelet on that node sees the assignment and starts the Docker container.  
    * *Result:* The workloads run on wind power.

### **Architectural Diagram**

Code snippet

sequenceDiagram  
    participant User  
    participant APIServer as K8s API Server  
    participant DefaultScheduler  
    participant EcoPulseBrain as Eco-Pulse Extender (Go)  
    participant CarbonAPI as Electricity Maps API  
    participant WorkerNode as Green Worker Node

    User-\>\>APIServer: 1\. Deploy Pod request  
    APIServer-\>\>DefaultScheduler: 2\. Notify unscheduled Pod  
      
    note over DefaultScheduler, EcoPulseBrain: Filtering Phase  
    DefaultScheduler-\>\>EcoPulseBrain: 3\. HTTP POST /filter (All Nodes)  
    EcoPulseBrain-\>\>CarbonAPI: 4\. GET Real-time Grid Intensity(Regions)  
    CarbonAPI--\>\>EcoPulseBrain: 5\. Return Data (e.g., US: Dirty, EU: Clean)  
    EcoPulseBrain--\>\>DefaultScheduler: 6\. Return Filtered Nodes (Remove US nodes)

    note over DefaultScheduler, EcoPulseBrain: Prioritizing Phase  
    DefaultScheduler-\>\>EcoPulseBrain: 7\. HTTP POST /prioritize (Filtered Nodes)  
    EcoPulseBrain--\>\>DefaultScheduler: 8\. Return Scored Nodes (EU gets high score)

    DefaultScheduler-\>\>DefaultScheduler: 9\. Select Best Node (EU)  
    DefaultScheduler-\>\>APIServer: 10\. Bind Pod to EU Node  
    APIServer-\>\>WorkerNode: 11\. Start Container  
    WorkerNode--\>\>User: App Running on Green Energy\!

## ---

**5\. Hackathon Technology Stack**

We chose a stack that is open-source, industry-standard for Cloud Native development, and resilient enough for a hackathon environment.

| Layer | Technology | Justification |
| :---- | :---- | :---- |
| **Language** | **Go (Golang)** | The native language of Kubernetes. Essential for high-performance, low-latency scheduler extensions. |
| **Local Infrastructure** | **KinD (Kubernetes in Docker)** | Allows running a multi-node K8s cluster entirely within Docker containers on a laptop. Perfect for simulating multi-region setups without cloud costs. |
| **Data Source** | **Electricity Maps API (Free Tier) / JSON Mock** | The gold standard for real-time grid data. We also utilize a local JSON mock server to ensure demo reliability if venue Wi-Fi fails. |
| **Monitoring** | **Prometheus** | The standard CNCF metrics tool. It easily scrapes the custom carbon metrics our Go brain exposes. |
| **Visualization** | **Grafana** | Connects to Prometheus to build professional, real-time dashboards to prove value to judges. |
| **Container Runtime** | **Docker** | Used to build the Eco-Pulse Brain container and run the KinD cluster. |

## ---

**6\. Implementation Guide & Setup (Proof of Feasibility)**

This outlines the steps taken to build the working prototype in the hackathon timeframe.

### **Prerequisites**

* Docker installed and running.  
* Go (1.19+) installed.  
* kubectl, kind, and helm CLIs installed.

### **Step 1: Simulating the Multi-Region Cloud**

We use KinD to create a 3-node cluster and label the nodes to simulate geography.

Bash

\# 1\. Create cluster config  
cat \<\<EOF | kind create cluster \--config=-  
kind: Cluster  
apiVersion: kind.x-k8s.io/v1alpha4  
nodes:  
\- role: control-plane  
\- role: worker \# Simulates US-East (Dirty)  
\- role: worker \# Simulates EU-North (Clean)  
EOF

\# 2\. Label the nodes geographically  
kubectl label node kind-worker region=us-east-1  
kubectl label node kind-worker2 region=eu-north-1

### **Step 2: Developing the "Brain" (Go Extender)**

We develop a Go HTTP server with the required endpoints. (Simplified pseudocode logic below):

Go

// main.go snippet  
func filterHandler(w http.ResponseWriter, r \*http.Request) {  
    // 1\. Decode K8s request containing list of nodes  
    // 2\. Loop through nodes, grab "region" label  
    // 3\. Call Carbon API for that region  
    // 4\. If intensity \> THRESHOLD, add to "failed\_nodes" list  
    // 5\. Encode response and send back to K8s  
}

func prioritizeHandler(w http.ResponseWriter, r \*http.Request) {  
     // 1\. Decode node list  
     // 2\. Get carbon intensity for regions  
     // 3\. Score \= (MaxIntensity \- NodeIntensity) \* 10  
     // 4\. Send back scores  
}

*This Go app is dockerized and pushed to a local registry.*

### **Step 3: Wiring the Brain to Kubernetes**

We must instruct the K8s control plane to use our new extender.

1. Create a scheduler-config.yaml file defining the extender webhook.  
2. Patch the existing Kube-Scheduler Deployment to use this new configuration file and point to our Go application's service URL.

### **Step 4: Visualization Setup**

Using Helm to rapidly deploy the monitoring stack.

Bash

helm repo add prometheus-community https://prometheus-community.github.io/helm-charts  
helm update  
helm install monitoring prometheus-community/kube-prometheus-stack

We then configure Grafana dashboards to query Prometheus for metrics exposed by our Go app, such as eco\_pulse\_carbon\_intensity\_current{region="eu-north"}.

## ---

**7\. Business Impact & Future Scope**

### **Business Value**

* **Regulatory Assurance:** Future-proofs infrastructure against emerging carbon taxation and mandatory reporting in the EU and US.  
* **FinOps Synergy:** Often aligns with lower energy costs by utilizing off-peak renewable surpluses.  
* **Brand Reputation:** Demonstrates verifiable commitment to sustainability, a key differentiator for modern consumers and B2B clients.

### **Future Roadmap**

* **AI-Driven Predictive Scheduling:** Move from reactive (real-time weather) to predictive scheduling using ML models to forecast grid intensity 24 hours out, preemptively moving heavy batch jobs before the sun sets.  
* **Hardware-Level Awareness:** Integrate with tools like Kepler to utilize eBPF for measuring actual CPU power draw at the socket level, combining grid data with hardware efficiency data.  
* **Stateful Workload Support:** Implement complex orchestration to handle the graceful migration of databases using synchronized replication before shifting compute resources.