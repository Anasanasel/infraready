# InfraReady - Automatisation et Supervision d'Infrastructure Hybride

Ce dépôt contient le code d'automatisation Ansible, les configurations de supervision et la documentation technique du projet d'infrastructure **InfraReady**.

## 1. Vue d'ensemble de l'Infrastructure

L'environnement repose sur un sous-réseau privé isolé (`192.168.50.0/24`) :
* **WIN-DC01 (`192.168.50.10`) :** Contrôleur de domaine Windows Server 2025 (`infraready.test`), serveur DNS.
* **OPS01 (`192.168.50.30`) :** Contrôleur Ansible, passerelle d'accès, métrologie Prometheus et Grafana.

## 2. Structure du Dépôt

```text
ansible-infra/
├── .github/
│   └── workflows/
│       └── validate.yml      # Pipeline CI (syntax-check Ansible automatique)
├── docs/
│   ├── architecture.md       # Rôles des machines et matrice des flux
│   ├── adressage-ip.md       # Plan d'adressage IP et configuration Netplan
│   ├── securite.md           # Filtrage pare-feu et politique de sécurité
│   ├── maintenance.md        # Procédures de mise à jour et snapshots
│   └── incident.md           # Rapport de diagnostic et résolution WinRM/NTLM
├── playbooks/
│   ├── test-windows.yml      # Test de connectivité et création de fichier d'audit
│   └── create-ad-user.yml    # Création automatisée d'utilisateurs Active Directory
├── ansible.cfg               # Configuration du contrôleur Ansible
├── inventory.ini             # Inventaire des machines cibles
└── README.md                 # Présentation générale du projety

