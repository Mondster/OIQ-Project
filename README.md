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

### **Assumptions for the Logging and Monitoring Infrastructure Design**

1. **Network Connectivity:**
   - All networks are **interconnected** or at least have access to the **internet**. Direct links to cloud infrastructure are avoided, as they may not be **cost-efficient**.

2. **Firewall Rules:**
   - **Firewall rules** permit **inbound traffic** from the **internet**, allowing external access as needed.

3. **Infrastructure as Code (IaC) Pipeline:**
   - There is an **IaC pipeline** in place, utilizing **Ansible**, **GitLab**, and any suitable **automation tooling** for infrastructure automation and management.

4. **Infrastructure Monitoring:**
   - A dedicated **team** is responsible for **monitoring the health** of the infrastructure to ensure stability and performance.

5. **Log Collection Agents:**
   - There are existing **agents** (e.g., **Snare agents**) sending logs from **Windows** systems.

6. **Private Link for Company Office:**
   - The **Company office** has a **private link** to the **collection infrastructure**, enabling **code deployment via secure data transmission**.
   - The private link also allows **admin users** and **regular users** to access the platform for **monitoring** and **configuring**.

7. **Autoscaling for Cloud Infrastructure:**
   - Since the infrastructure is hosted in the cloud, **autoscaling** can be used for cloud infrastructure to save costs and handle varying loads.

8. **High Availability (HA) and Multi-AZ for Cloud Deployment:**
   - The collection infrastructure deployed in the cloud makes use of **High Availability (HA)** and **Multi-AZ** (Availability Zone) features for enhanced resilience and fault tolerance.

9. **Ansible Access Limited to Management Plane:**
   - **Ansible** is confined to the **management plane**, with no direct access to the **data plane**. It automates the deployment and configuration of the management infrastructure (e.g., **ObservIQ OP (BindPlane)**).

---

### **Requirement 1:**
- **The functionality to collect system logs from multiple data centres and centralise these at a single point before forwarding to a downstream consumer.**

### **Response:**
1. **Lightweight OpenTelemetry (OTel) Agents** are installed on endpoints within the data centers, including those that cannot forward syslogs directly (e.g., **Windows AD** or application servers).

2. **Local Collector Gateways** are deployed in each data center to handle log collection, filtering, and processing. Logs and metrics are sent to these gateways for initial processing.

3. The **local collector gateways** handle:
   - **Batching** the data to optimize transmission.
   - **Filtering** unnecessary or irrelevant logs to reduce bandwidth usage.
   - **Enriching** the data with additional context or metadata if needed.

4. Logs are then securely transmitted via **TLS** to the **central collector gateways**.

5. The **central collector gateways** performs additional processing, such as **ETL (Extract, Transform, Load)** processes, before forwarding the logs to downstream consumers.

6. **ObservIQ OP (BindPlane)** is used to centrally manage the agents and pipelines, ensuring that configuration changes can be pushed out to the agents efficiently.

7. After processing, the logs are forwarded to downstream consumers such as:
   - **SIEM platforms** (e.g., Splunk, Chronicle).
   - **Monitoring platforms** (e.g., Prometheus, Grafana).

### **Requirement 2:**
- **A management server that can communicate with all agents present on the endpoints to push configuration and filtering changes. The management server must be accessible via a UI.**

### **Response:**
1. The **management server** will use **ObservIQ OP (BindPlane)** to manage communication with all agents deployed on the endpoints.

2. The **Web UI** provided by the management server allows users to:
   - **Push configuration updates** to the agents, including log collection settings and filtering rules.
   - **Modify telemetry pipelines** and control data flows between endpoints and the central collector.
   - **Delete or update pipelines** as needed to adapt to changing requirements.
   - **Upgrade agents** remotely, ensuring all endpoints are running the latest versions.
   - **Monitor agent health**, checking for any issues with resource consumption or performance.
   - **Monitor pipeline performance**, ensuring smooth log collection and processing across the infrastructure.

3. **Role-Based Access Control (RBAC)** will be enforced, ensuring that different levels of administrative control can be assigned based on roles:
   - **Admin users** will have full access to manage agents, update configurations, and make system-wide changes.
   - **Regular users** can be assigned permissions to view agent health or manage specific pipelines, without access to critical configurations.
   - This ensures **granular control** and **secure management** of agents and pipelines.

4. The management server will be centrally accessible through a **web interface**, allowing administrators to:
   - **View real-time data** about agent health and log collection performance.
   - **Control deployment pipelines** and respond to issues or adjust configurations quickly.

5. This setup ensures centralized, real-time **control over agents** and their configuration, improving flexibility and reducing the need for manual intervention at each endpoint.

### **Requirement 3:**
- **Upstream sources present in physical data centres as either hardware or virtual machines.**

### **Response:**
1. **OpenTelemetry (OTel) Agents** are capable of collecting logs, metrics, and traces from various upstream sources in physical data centers, whether they are running on **hardware** or **virtual machines**.

2. The **common 5 upstream sources** that are likely to be present in a data center setup include:
   - **Active Directory DS Receiver** – Collects logs and telemetry from **Active Directory Domain Services**, typically deployed in on-premises environments.
   - **Apache Receiver** – Gathers logs and metrics from **Apache HTTP Servers**, widely used as web servers in data centers.
   - **MySQL Receiver** – Monitors **MySQL databases**, frequently deployed in on-premises infrastructure for relational data management.
   - **Nginx Receiver** – Captures logs and metrics from **Nginx**, a common web server and reverse proxy.
   - **vCenter Receiver** – Gathers data from **VMware vCenter**, used for managing virtual machines (VMs) in data center environments.

3. These sources can be monitored and managed via **OTel agents** deployed on physical servers or VMs, ensuring comprehensive log collection and telemetry from critical infrastructure components.

### **Requirement 4:**
- **Downstream consumers hosted in the cloud as SaaS solutions. These support proprietary forwarders that can be hosted as virtual appliances or webhook-like ingestion functionality.**

### **Response:**
1. **Downstream consumers** of logs and metrics, which are hosted as **SaaS solutions** in the cloud, can receive data through **proprietary forwarders** that are either:
   - **Hosted as virtual appliances** within the cloud environment.
   - Leveraging **webhook-like ingestion functionality** to receive data.

2. For **SecOps, SRE (Site Reliability Engineering), and analytics** use cases, the following **top 5 exporters** are likely to be utilized for forwarding logs and metrics to downstream consumers:
   - **ElasticSearch Exporter** – Provides real-time search and analytics, useful for log management and operational analytics.
   - **Splunk HEC Exporter** – A popular exporter in **SecOps** for real-time log monitoring and security event analysis.
   - **Prometheus Remote Write Exporter** – Allows metrics to be forwarded to **Prometheus**, commonly used in **SRE** for infrastructure monitoring and alerting.
   - **Google Cloud Exporter** – Used for exporting data to **Google Cloud Monitoring**, supporting a range of cloud-native analytics and security monitoring tasks.
   - **Datadog Exporter** – A versatile exporter that supports **logs, metrics, and traces** for observability, used widely in **SecOps** and **SRE** for monitoring and troubleshooting.

3. These exporters ensure that logs and metrics from the **central collector** can be sent securely and efficiently to **cloud-based SaaS solutions**, where further analytics, monitoring, and security operations can be performed.

4. The integration with proprietary forwarders supports flexible deployment models, enabling either **virtual appliances** or **webhooks** to be utilized based on the specific requirements of the SaaS solution.

---

























