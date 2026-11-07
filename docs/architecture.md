# Architecture Technique - Projet InfraReady

## 1. Contexte et Objectifs
Le projet InfraReady vise à standardiser, automatiser et superviser un système d'information hybride composé d'environnements Linux et Windows Server. L'architecture permet l'administration reproductible via Ansible et la surveillance continue via Prometheus et Grafana.

## 2. Inventaire et Rôles des Machines

| Machine | Système d'exploitation | Rôle principal | IP / Sous-réseau |
| :--- | :--- | :--- | :--- |
| **WIN-DC01** | Windows Server 2025 Standard | Contrôleur de domaine AD DS (`infraready.test`), serveur DNS | `192.168.50.10/24` |
| **OPS01** | Ubuntu Server 24.04 LTS | Contrôleur Ansible, Git, Prometheus, Grafana, Node Exporter | `192.168.50.30/24` |

> **Note d'adaptation d'architecture (Section 4.3 du sujet) :**  
> Conformément aux préconisations d'adaptation aux ressources matérielles, les rôles de services Linux, de contrôle Ansible et de supervision ont été consolidés sur la machine `OPS01`. Ce choix évite la surcharge mémoire de l'hyperviseur tout en conservant l'intégralité des objectifs de gestion et de monitoring.

## 3. Matrice des Flux et Ports d'Écoute

| Source | Destination | Port / Protocole | Service / Usage | Justification technique |
| :--- | :--- | :--- | :--- | :--- |
| **Hôte** | `OPS01` | TCP 2222 -> 22 | SSH (PuTTY) | Administration console à distance sécurisée |
| **Hôte** | `OPS01` | TCP 3000 | Grafana HTTP | Accès aux tableaux de bord de métrologie |
| **Hôte** | `OPS01` | TCP 9090 | Prometheus HTTP | Consultation de l'état des cibles (`Targets`) |
| **OPS01** | `WIN-DC01` | TCP 5985 | WinRM (HTTP) | Exécution des commandes et playbooks Ansible |
| **OPS01** | `WIN-DC01` | TCP 9182 | Windows Exporter | Scrape des métriques système Windows par Prometheus |
| **OPS01** | `OPS01` | TCP 9100 | Node Exporter | Collecte locale des métriques système Linux |
| **OPS01** | `WIN-DC01` | UDP/TCP 53 | DNS AD DS | Résolution des noms du domaine `infraready.test` |

## 4. Chaîne de Supervision et Automatisation
* **Automatisation :** `OPS01` pilote `WIN-DC01` sans agent lourd grâce à l'interface WinRM native de Windows, sécurisée avec authentification NTLM.
* **Supervision :** Prometheus interroge périodiquement les agents `node_exporter` et `windows_exporter` toutes les 15 secondes. Les séries temporelles sont agrégées et restituées graphiquement dans Grafana.
