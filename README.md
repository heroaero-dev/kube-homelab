<div align="center">
  <img src="https://raw.githubusercontent.com/kubernetes/kubernetes/master/logo/logo.svg" width="180" alt="Kubernetes Logo">

  <br><br>

  <img src="https://img.shields.io/badge/Kubernetes-326CE5?style=for-the-badge&logo=kubernetes&logoColor=white">
  <img src="https://img.shields.io/badge/kubeadm-Deployed-blue?style=for-the-badge&logo=kubernetes&logoColor=white">
  <img src="https://img.shields.io/badge/Longhorn-Storage-FF6A5C?style=for-the-badge&logo=longhorn&logoColor=white">
  <img src="https://img.shields.io/badge/Cilium-CNI-FDD835?style=for-the-badge&logo=cilium&logoColor=black">

  <br><br>
</div>

---

# Homelab Kubernetes Cluster – Built From Bare Metal With Kubeadm


This repository documents the evolution of my personal Kubernetes homelab, deployed using **kubeadm**, engineered for growth, failure, learning by best practice.
This cluster is my playground, my lab, and my hands-on path toward mastering Kubernetes while preparing for the **CKA** and ultimately a DevOps/SRE role.

I built this environment from scratch, node-by-node, YAML-by-YAML, because nothing teaches Kubernetes like touching the metal and running the control plane yourself.

---

## 🏗 Cluster Architecture

### Control Plane (Bare Metal)
| Component | Specs |
|----------|-------|
| CPU      | AMD Ryzen 9 3900X (12-core) |
| GPU      | Radeon RX5700XT 8GB |
| Storage  | 1TB NVMe M.2 SSD |
| Board    | MSI MPG X570 Motherboard |

### Worker Nodes
| Device | Specs |
|--------|-------|
| Raspberry Pi CM5 x2 | 8GB RAM / 32GB eMMC each |

---

## 🔧 Core Components

| Component | Purpose |
|----------|----------|
| **kubeadm** | Low-level cluster provisioning, closest to real production environments |
| **Cilium** | CNI powered by eBPF, high-performance networking |
| **Longhorn** | Distributed block-storage for Kubernetes |
| **External-secrets** | External secret managing system |
| **AWS secret-manager** | Stores credentials and API keys |

This setup is not convenience-based. It is **learning-focused and deliberate**.

---

## ❓ Why Kubeadm?

I wanted a real cluster. Not a simplified one.

kubeadm gives me direct access to control plane mechanics, networking challenges, upgrade cycles, storage design, troubleshooting, and the same patterns used in many production clusters. It forces understanding instead of abstractions. Which is exactly what I want as I work toward CKA certification.

This cluster exists to **teach me**, not shield me.

---

## 🧠 Why I Built This

I’ve always loved building systems, whether it be in plumbing, computers, or anywhere else.  
Seeing a finished structure that I assembled piece-by-piece is where I get my dopamine.

Homelabbing gives me that same satisfaction.

Building a repo, a network, a cluster, from scratch, watching it come alive.  
That’s where passion meets progress.

---

## 🏠 What Problems This Solves For Me

- Self-hosting passwords with **Vaultwarden** instead of paying for proprietary solutions  
- Daily practice with outages, debugging, networking, clustering, observability  
- Real-world experience that transfers directly into DevOps/SRE responsibilities  
- A safe place to break things, fix things, and **understand why it broke**

Every problem is a lesson. Every fix is growth.

---

## 🚀 Services Running in This Cluster

| Category | Tools |
|--------|--------|
| Ingress + Networking | Traefik, Cloudflare Tunnel |
| Secrets & Security | External Secrets + SOPS/AGE |
| Monitoring & Metrics | Kube-Prometheus-Stack, Blackbox Exporter, Victoria Logs |
| Databases | PostgreSQL |
| Media & UI | Jellyfin, Homepage Dashboard |

All deployed declaratively and continuously iterated on through this repo.

---

## 📁 Who This Repo Helps

This repository is valuable for:

- Homelabbers wanting **real kubeadm reference material**
- Anyone learning Kubernetes beyond k3s/microk8s abstractions
- CKA-focused learners who want real cluster administration experience
- Devs studying GitOps, storage networking, and internal cluster mechanics

If you want to see a Kubernetes cluster built from the ground up — this is that.

---

## 🛣 Future Roadmap

- Multi-environment layout (**dev  staging  production**)
- Expanded GitOps automation
- More apps, more observability, more experimentation

I plan to keep breaking, learning, and improving.

---


