# OpenTelemetry Observability Platform – Ansible Automation

## Overview

This repository provides an **Ansible-based automation framework** for deploying and managing an **OpenTelemetry Observability Platform**.

The objective is to automate the deployment of a complete observability stack on fresh Linux virtual machines with minimal manual intervention.

The platform is designed around the **OpenTelemetry Agent → Gateway architecture**, where lightweight collectors forward telemetry to a centralized gateway responsible for processing and exporting telemetry to observability backends.

The repository follows a **role-based Ansible design** to promote modularity, reusability, and maintainability.

---

# Solution Architecture

```

```
                 +---------------------------+
                 |   Ansible Control Node    |
                 |  (Ubuntu on Laptop/WSL)   |
                 +------------+--------------+
                              |
                              | SSH
                              |
        -------------------------------------------------
        |                     |                         |
        |                     |                         |
+---------------+     +---------------+       +----------------+
| OTEL Agent VM |     | Gateway VM    |       | Monitoring VM  |
|               |     |               |       |                |
| OTEL Agent    | --> | OTEL Gateway  |       | Prometheus     |
| Node Exporter |     | Jaeger        |       | Grafana        |
| Docker        |     | Docker        |       | Docker         |
+---------------+     +---------------+       +----------------+

```

All services are deployed as Docker containers using Ansible.

---

# Repository Structure

```

otel-ansible/
│
├── ansible.cfg
├── inventory.ini
├── site.yml
│
├── playbooks/
│ ├── deploy-agent.yml
│ ├── deploy-gateway.yml
│ └── legacy/
│
├── roles/
│ ├── base/
│ ├── docker/
│ ├── otel_agent/
│ ├── otel-gateway/
│ ├── prometheus/
│ ├── grafana/
│ ├── node-exporter/
│ ├── exporters/
│ └── splunk/
│
├── files/
│
└── README.md

*******************************************************
Design Principles

The repository is built around the following principles:

Infrastructure automation using Ansible
Role-based deployment
Reusable playbooks
Idempotent configuration
Docker-based services
Modular architecture
Easy deployment on new virtual machines
Deployment Workflow

The deployment follows a sequential approach.

New Linux VM
        │
        ▼
Install Docker
        │
        ▼
Start Docker Service
        │
        ▼
Create Common Docker Network
        │
        ▼
Create Required Directories
        │
        ▼
Copy Configuration Files
        │
        ▼
Copy Docker Compose Files
        │
        ▼
Deploy Containers
        │
        ▼
Validate Deployment


This workflow is common across all supported services.

Role Descriptions
Base

Responsible for preparing the operating system.

Tasks include:

Create required directories
Prepare filesystem
Common initialization tasks
Docker

Responsible for Docker installation.

Tasks include:

Install Docker Engine
Enable Docker service
Start Docker
Create common Docker network
OpenTelemetry Agent

Deploys the OpenTelemetry Collector configured as an Agent.

Tasks include:

Create Agent directories
Copy Collector configuration
Copy Docker Compose file
Start Agent container
OpenTelemetry Gateway

Deploys the OpenTelemetry Gateway.

Tasks include:

Create Gateway directories
Copy Gateway configuration
Copy Docker Compose file
Deploy Gateway container
Prometheus

Deploys Prometheus monitoring server.

Tasks include:

Deploy configuration
Deploy Docker Compose
Start container
Grafana

Deploys Grafana dashboards.

Tasks include:

Copy configuration
Deploy Docker Compose
Start Grafana
Node Exporter

Deploys Node Exporter for host metrics.

Exporters

Deploys additional exporters required by the monitoring platform.

Splunk

Deploys a local Splunk container used for development and testing.

Inventory Structure

The inventory groups hosts based on their responsibilities.

Example:

[otel_agents]

[otel_gateways]

[prometheus]

[grafana]

[node_exporters]

[splunk]

[all:vars]
ansible_user=<user>
ansible_python_interpreter=/usr/bin/python3
Playbooks
site.yml

Primary orchestration playbook.

Responsible for executing the deployment workflow across all required roles.

Role-specific Playbooks

Additional playbooks are available for deploying individual components such as:

OpenTelemetry Agent
OpenTelemetry Gateway

These are useful for targeted deployments during development.

Prerequisites
Ubuntu Linux
Ansible installed
SSH connectivity to target hosts
Passwordless SSH authentication
Internet connectivity for Docker image downloads
Deployment

Deploy the complete platform.

ansible-playbook site.yml

Deploy a specific playbook.

ansible-playbook playbooks/deploy-agent.yml
Validation

After deployment verify:

Docker service is running
Docker network exists
Containers are healthy
OpenTelemetry Agent is exporting telemetry
Gateway is receiving telemetry
Prometheus targets are UP
Grafana dashboards are accessible
Current Status

Current implementation supports:

Docker installation
Docker network creation
OpenTelemetry Agent deployment
OpenTelemetry Gateway deployment
Prometheus deployment
Grafana deployment
Node Exporter deployment
Splunk deployment

The project is under active development and continuously evolving.

Future Enhancements

Planned improvements include:

Dynamic inventories
Ansible Vault
Environment-specific variables
TLS / mTLS configuration
Automated validation tasks
CI/CD integration using GitHub Actions
Terraform integration
AWS deployment automation
High Availability deployment
Automated rollback procedures
Best Practices
Follow role-based architecture.
Keep configuration files separate from playbooks.
Avoid hardcoded values.
Store reusable variables in group variables.
Test deployments on fresh virtual machines.
Commit changes frequently with meaningful messages.
Related Repository

This repository automates the deployment of the observability platform contained in:

otelAgent-gateway-Observability

The observability repository contains:

OpenTelemetry Collector configurations
Docker Compose files
Demo applications
Gateway architecture
Observability backend integration

Together, both repositories provide a complete reference implementation of an enterprise-style OpenTelemetry deployment.

License

MIT License

Author

Manpreet Singh

This repository is part of an ongoing effort to build a production-inspired OpenTelemetry observability platform using Docker, Ansible, Prometheus, Grafana, Jaeger, and Splunk Observability.
