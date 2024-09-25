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


