---
title: About me
comments: false
---

## About me

I'm Sultan — a Cloud Infrastructure Engineer based in Jakarta. I spend most of my time on GCP, managing Kubernetes clusters, writing Terraform, and setting up monitoring. I'm used to working as the only cloud engineer on the team, so I tend to cover a wide range of things — from provisioning to observability to incident response.

This site is where I write about the things I build and the stuff I learn along the way.

---

## What I Do

{{< cards cols="2" >}}
  {{< card
    title="Cloud Infrastructure"
    icon="cloud"
    subtitle="GCP infrastructure — VPCs, GKE, Cloud SQL, IAM. Reduced cloud spend by up to 60% at previous role."
  >}}
  {{< card
    title="Platform & Automation"
    icon="chip"
    subtitle="Terraform modules, Ansible playbooks, GitOps with ArgoCD. If I do it twice, I automate it."
  >}}
  {{< card
    title="Observability & Reliability"
    icon="chart-bar"
    subtitle="Prometheus, Grafana, Loki, Alertmanager. I set up monitoring so I don't get woken up at 3am."
  >}}
  {{< card
    title="Migration & Modernization"
    icon="cube"
    subtitle="Bare metal and legacy VMs to Kubernetes (GKE), Cloud SQL, and managed services."
  >}}
{{< /cards >}}

---

## Experience

{{< tabs >}}

{{< tab name="Ebconnect · 2025–Present" >}}

**Cloud Engineer** — PT. Ebconnect Indonesia
*(On-site at Pertamina Bina Medika IHC, Jakarta — healthcare enterprise)*

- Reduced monthly cloud spend by **100M IDR** within the first quarter by eliminating unused resources, migrating legacy *Cloud SQL* instances off extended support, and right-sizing compute. Identified and proposed CUD commitments projected to save an additional **50M IDR/month**.
- Migrated **5 applications** and databases from bare metal to *Google Kubernetes Engine* and *Cloud SQL*, containerizing legacy monoliths to improve scalability and disaster recovery readiness.
- Managed *HashiCorp Vault* for secrets management across GKE and on-premises services.
- Refactored *Terraform* modules and deployment standards across **5 GCP projects**, improving provisioning consistency and onboarding speed for new workloads.
- Built a merge-request approval workflow using *n8n* and *GitLab* comments, enabling multi-reviewer code review gates without GitLab Premium licensing.
- Took over DevOps responsibilities (*ArgoCD*, *Helm*, *GitLab CI/CD*) after team departure, maintaining GitOps workflows for production deployments across a hybrid hub-and-spoke network topology.
- Building an internal cloud infrastructure documentation site (*Docusaurus*) — addressing tech debt so the next person doesn't have to reverse-engineer everything.

{{< /tab >}}

{{< tab name="Magpie · 2023–2025" >}}

**Cloud Infrastructure Engineer** — PT. Magpie Data Indonesia *(Remote)*
*Sole cloud and infrastructure engineer for a data company (~30 employees).*

- Reduced monthly infrastructure costs by approximately **60%** (~90M to ~30M IDR) by migrating batch-processing orchestration (*Prefect*) from always-on VMs to *Kubernetes* on GKE with spot instances.
- Migrated *Apache Airflow* workloads to GKE after recurring downtime from resource exhaustion on standalone VMs, eliminating unplanned outages and reducing deployment time by **40%**.
- Built and managed a multi-node *Proxmox* cluster (**50+ VMs**) across two sites for data collection workloads, provisioned and configured entirely with *Ansible*.
- Deployed self-hosted *ZeroTier* network controller (ztnet) providing secure internal connectivity for **50+ VMs** and local workstations without recurring VPN licensing costs.
- Built centralized monitoring and alerting using *Prometheus*, *Grafana*, *Loki*, and *Alertmanager* with automated Discord notifications, reducing incident recovery time by approximately **35%**.
- Enforced infrastructure security practices: TLS/HTTPS across all services, self-hosted *Passbolt* for credential management, regular IAM access reviews, and least-privilege policies.
- Resolved critical production incidents including database disk exhaustion and storage overflow; implemented automated log rotation and proactive alerting to prevent recurrence.
- Created and maintained operational runbooks covering infrastructure setup, access procedures, and incident response — documentation still actively used by the team after departure.

{{< /tab >}}

{{< tab name="YABB · 2022" >}}

**Data Analyst Trainee** — Yayasan Anak Bangsa Bisa *(Remote)*

- Built an interactive Google Data Studio dashboard for global heart disease analysis using Python and SQL
- Delivered all assigned projects on time as part of a five-person team, producing insights for a national health program

{{< /tab >}}

{{< /tabs >}}

---

## Skills

{{< cards >}}
  {{< card title="Cloud & Infrastructure" icon="cloud" subtitle="GCP (primary) · Proxmox · Virtualization" >}}
  {{< card title="Containers & Orchestration" icon="cube" subtitle="Kubernetes (GKE) · Docker · Helm" >}}
  {{< card title="Infrastructure as Code" icon="code" subtitle="Terraform · Ansible" >}}
  {{< card title="Observability" icon="chart-bar" subtitle="Prometheus · Grafana · Loki · Alertmanager" >}}
  {{< card title="CI/CD & GitOps" icon="refresh" subtitle="ArgoCD · GitLab CI · GitHub Actions" >}}
  {{< card title="Security & Networking" icon="shield-check" subtitle="HashiCorp Vault · ZeroTier · VPC peering · OWASP" >}}
{{< /cards >}}

---

## How I Work

{{< cards cols="2" >}}
  {{< card
    title="Maintainability first"
    icon="code"
    subtitle="Simple systems beat clever ones. Future me has to debug this."
  >}}
  {{< card
    title="Document as you build"
    icon="document-text"
    subtitle="If it's not written down, it doesn't exist."
  >}}
  {{< card
    title="Automate the painful path"
    icon="cog"
    subtitle="If I do it more than twice, it becomes a playbook or a module."
  >}}
  {{< card
    title="Observability before scale"
    icon="chart-bar"
    subtitle="Monitoring goes in before traffic does. Can't fix what you can't see."
  >}}
{{< /cards >}}

---

## Certifications

{{< cards cols="1">}}
  {{< card
    title="Professional Cloud DevOps Engineer"
    icon="gcp-icon"
    subtitle="Google Cloud · 2026"
    link="https://www.credly.com/badges/c4e55f90-b133-47ea-ad32-033932713184/public_url"
    tag="GCP"
    tagColor="blue"
  >}}
  {{< card
    title="Professional Cloud Architect"
    icon="gcp-icon"
    subtitle="Google Cloud · 2026"
    link="https://www.credly.com/badges/e39cdf0e-2147-4226-adaa-251f5a5834f8/public_url"
    tag="GCP"
    tagColor="blue"
  >}}
  {{< card
    title="AWS Cloud Practitioner Essentials"
    icon="aws-icon"
    subtitle="Dicoding Indonesia · 2025"
    link="https://www.dicoding.com/certificates"
    tag="AWS"
    tagColor="yellow"
  >}}
  {{< card
    title="Alibaba Cloud Certified Associate"
    icon="alibaba-icon"
    subtitle="Alibaba Cloud · 2024"
    link="https://edu.alibabacloud.com/certification"
    tag="Alibaba"
    tagColor="orange"
  >}}
  {{< card
    title="Associate Cloud Engineer"
    icon="gcp-icon"
    subtitle="Google Cloud · 2024"
    link="https://www.credly.com/badges/8094fe7a-e40b-4b7a-824a-b6523fa71e94/public_url"
    tag="GCP"
    tagColor="blue"
  >}}
{{< /cards >}}

---

## Education

{{< cards cols="1" >}}
  {{< card title="Bachelor of Science (Physics)" icon="academic-cap" subtitle="Diponegoro University · 2018–2022" >}}
{{< /cards >}}

---

## Currently Exploring

{{< cards cols="2" >}}
  {{< card
    title="Kubernetes Reliability"
    icon="cube"
    subtitle="Multi-cluster patterns, advanced networking, and operator development"
  >}}
  {{< card
    title="Platform Engineering"
    icon="chip"
    subtitle="Internal developer platforms, golden paths, and self-service infrastructure"
  >}}
  {{< card
    title="GitOps at Scale"
    icon="refresh"
    subtitle="Progressive delivery, multi-environment promotion, and ArgoCD patterns"
  >}}
  {{< card
    title="Deeper Observability"
    icon="chart-bar"
    subtitle="Distributed tracing, SLOs, and alerting design beyond basic metrics"
  >}}
{{< /cards >}}

---

## About Subaverse

**Sultan Bayu** + **Universe** = Subaverse.

Part engineering journal, part docs hub, part portfolio. Not everything I work on ends up here — a lot of production work is sensitive. What's here is what I choose to share: notes, guides, and projects that represent how I think about infrastructure.
