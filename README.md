# OIQ-Project

# SecOps Security Analytics Tech Challenge - DevOps Focus

## Scenario

You have been tasked to support a project aimed at rolling out logging agents to a wide set of server endpoints and building a new syslog collection infrastructure. Your role is as a **DevOps Engineer**, responsible for the infrastructure layer of this solution.

### Application Requirements

The application should include the following functionalities:

- Collect system logs from multiple data centres and centralize these at a single point before forwarding them to a downstream consumer.
- Provide a management server capable of communicating with all agents on the endpoints to push configuration and filtering changes. The management server must also be accessible via a UI.
- Support upstream sources located in physical data centres, either as hardware or virtual machines.
- Support downstream consumers hosted in the cloud as SaaS solutions. These solutions can utilize proprietary forwarders hosted as virtual appliances or webhook-like ingestion functionality.

Your deliverable is a design to support the above requirements.

## Considerations

While working on this solution, the following factors should be considered:

- **Scalability** and **cost efficiency**.
- **Stability** and **resilience**.
- **Patching** and **maintenance** strategies.
- The **technologies** used for hosting and building the solution.
- Any **assumptions** made during the design process.

## Response Format

You may choose to provide your solution in one of the following formats:

- A simplified solution built in a personal lab environment and demonstrated.
- A design diagram detailing the architecture, with the ability to discuss design decisions.
- A write-up detailing the design and approach, with a readiness to explain the decisions made.

---

### **Design Overview**

#### **1. Log Collection Data Flow (3-Tier Architecture - Data Plane):**
- **Tier 1 (Client):** OTel agents collect logs and metrics from endpoints.
- **Tier 2 (Application):** Local gateways deployed in each data center filter, batch, and forward logs.
- **Tier 3 (Data Layer):** The central collector processes the logs and forwards them to downstream systems (e.g., SIEMs, Prometheus, Splunk).

#### **2. Agent and Collector Management (2-Tier Architecture - Control Plane):**
- **Tier 1 (Client/UI):** A **Web UI** for managing agents, configurations, and pipeline deployments (e.g., **ObservIQ OP (BindPlane)**).
- **Tier 2 (Management Server):** The backend management server communicates with agents and local gateways to push configurations, monitor agent health, and manage updates.
- **Ansible** operates here, managing the **deployment** and **configuration** of the management server and any required updates to the Web UI and management infrastructure.

### **Agent and Collector Management (Control Plane)**
- The **management layer** focuses on the configuration, deployment, and monitoring of the agents and pipelines. It ensures the infrastructure is running smoothly.
- **Ansible** operates within the Control Plane, automating the **deployment** and **management** of the management infrastructure (e.g., **ObservIQ OP (BindPlane)**) without directly managing the data collection agents after initial deployment of the agent.
  
### **Log Data Flow (Data Plane)**
- The **Data Plane** is responsible for the **collection, processing, and forwarding** of logs, metrics, and traces from the agents to the local or central collector then to downstream consumers.
- The **ObservIQ OP (BindPlane)** in the Control Plane manages the configuration of agents and collectors in the Data Plane.

Separating the **Data Plane** from the **Control Plane**, to provide distinct purposes:

1. **Data Plane (Log Collection & Processing):**
   - This layer handles the collection, processing, and forwarding of logs, metrics, and traces.
   - A **3-tier architecture**:
     - **Tier 1 (Client):** OTel agents deployed on endpoints collect logs and metrics.
     - **Tier 2 (Application Layer):** Local collector gateways handle processing such as filtering and batching.
     - **Tier 3 (Data Layer):** The central collector gateway performs final aggregation and forwards the logs to downstream consumers.

2. **Control Plane (Agent & Pipeline Management):**
   - This layer manages the **agents and pipelines**, handling updates, configuration management, and monitoring.
   - A **2-tier architecture**:
     - **Tier 1 (Client/UI Layer):** The **Web UI** (e.g., **ObservIQ OP (BindPlane)**) allows administrators to configure and manage agents and pipelines.
     - **Tier 2 (Management Layer):** The **Management Server** handles communication with agents and gateways, pushing configuration updates and monitoring agent health.
     - **Ansible** automates the **management plane** tasks, such as deploying updates and ensuring infrastructure consistency, but doesn’t directly interact with the Data Plane.

### **Logical Separation the Two Architectures**

1. **Different Responsibilities:**
   - The **Data Plane** focuses on log collection and processing.
   - The **Control Plane** ensures that agents and infrastructure components are managed, configured, and updated but doesn’t directly interact with the log data.
   - **Ansible** handles only the **Control Plane**, ensuring that management servers are correctly deployed and maintained.

2. **Scalability:**
   - The **Data Plane** needs to scale to process large volumes of logs, while the **Control Plane** (managed by Ansible) scales to manage agent configurations and updates.
   - Separating them allows independent scaling of log processing infrastructure and management tools.

3. **Failure Isolation:**
   - Separation ensures that any issues in the **Data Plane** (e.g., log processing failures) don’t affect the ability to manage agents and configurations in the **Control Plane**.
   - **Ansible’s role** in the Control Plane is to ensure the management infrastructure remains stable and responsive.

4. **Simplicity and Maintenance:**
   - The **Control Plane** (2-tier) remains simple, focusing on management, updates, and configurations via **Ansible**.
   - The **Data Plane** (3-tier) focuses on log collection and processing, ensuring performance and scalability without interference from management tasks.

### **Example Setup:**

- **Agent and Pipeline Management (Control Plane):**
  - **Web UI:** A dashboard for managing agent deployments, configurations, and updates (e.g., **ObservIQ OP (BindPlane)**).
  - **Management Server:** Handles communication with agents and gateways, pushing configuration changes and monitoring health.
  - **Ansible:** Automates the deployment and maintenance of the **management infrastructure** but does not directly interact with log collection.

- **Log Collection and Processing (Data Plane):**
  - **OTel Agents:** Collect logs and metrics from endpoints.
  - **Local Gateways:** Deployed in each data center to filter, process, and forward logs.
  - **Central Collector:** Collects logs, processes them, and forwards them to downstream consumers (e.g., SIEMs, Prometheus).

---

























