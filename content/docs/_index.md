---
title: Documentation
layout: docs
comments: false
cascade:
  - _target:
      kind: section
    layout: subsection-list
---

This section contains a curated selection of my engineering notes — implementation references, architecture write-ups, and operational guides across cloud infrastructure, Kubernetes, DevOps, and Linux.

In practice, a lot of production documentation involves sensitive details: internal network topology, company-specific workflows, or infrastructure configurations that aren't appropriate to publish. What's here is a deliberate subset — content that's safe to share publicly and representative of how I structure knowledge and document systems.

The focus is on demonstrating documentation quality, engineering thinking, and how I approach building maintainable references over time.

{{< cards >}}
  {{< card link="kubernetes" title="Kubernetes" icon="terminal" subtitle="Workloads, networking, storage, ingress, and GKE patterns." >}}
  {{< card link="cloud" title="Cloud" icon="cloud" subtitle="GCP, multi-cloud architecture, IAM, Cloud SQL, and infrastructure design." >}}
  {{< card link="devops" title="DevOps" icon="refresh" subtitle="CI/CD, GitOps with ArgoCD, GitHub Actions, Docker, and automation." >}}
  {{< card link="linux" title="Linux" icon="terminal" subtitle="System administration, shell scripting, and self-hosted infrastructure." >}}
{{< /cards >}}
