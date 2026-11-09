# Plan d'Adressage IP et Routage Réseau

## 1. Contexte Réseau et Sous-Réseaux
L'infrastructure de laboratoire repose sur un réseau privé isolé garantissant la non-exposition des services d'annuaire et de supervision au réseau externe. Une interface NAT est configurée sur OPS01 afin de permettre le téléchargement des paquets et des collections.

* **Sous-réseau interne :** 192.168.50.0/24
* **Masque de sous-réseau :** 255.255.255.0
* **Domaine DNS interne :** infraready.test
* **Serveur DNS principal :** 192.168.50.10 (WIN-DC01)

## 2. Table d'Attribution des Adresses IP

| Machine | Interface | Rôle / Type | Adresse IP / Préfixe | Passerelle | DNS Primaire | Justification |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **WIN-DC01** | Ethernet0 | Réseau Privé | 192.168.50.10/24 | Aucune | 127.0.0.1 | Contrôleur AD DS & DNS |
| **OPS01** | enp0s3 | Réseau Privé | 192.168.50.30/24 | Aucune | 192.168.50.10 | Ansible & supervision |
| **SRV-LINUX** | Debian / Ubuntu Linux | enp0s3 | **192.168.50.20/24** | - | Serveur applicatif, Volume monté (/mnt/stockage-lab) |


## 3. Configuration Réseau Netplan (OPS01)
Le réseau est géré par Netplan via systemd-networkd :
* `enp0s3` est en IP statique (192.168.50.30/24) sans passerelle pour le réseau local.
* `enp0s8` est en DHCP avec `route-metric: 100` pour la sortie Internet.

## 4. Tests et Vérifications
* Connectivité IP vérifiée par ping vers 192.168.50.10.
* Résolution DNS vérifiée par getent hosts WIN-DC01.infraready.test.
* Transport Ansible validé par la commande win_ping (SUCCESS).
