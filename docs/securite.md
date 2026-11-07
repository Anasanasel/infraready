# Politique de Sécurité et Gestion des Accès

## 1. Périmètre et Principes Directeurs
Conformément aux règles du projet InfraReady, l'ensemble des opérations d'administration respecte le principe du moindre privilège, le cloisonnement des flux réseau et l'interdiction stricte d'exposer des secrets en clair dans le système de gestion de version.

## 2. Gestion des Comptes et Privilèges
* **Contrôleur de domaine (WIN-DC01) :** 
  * Compte d'administration locale et de domaine restreint aux opérations d'infrastructure.
  * Création d'utilisateurs de test standard (`testuser@infraready.test`) sans privilèges d'administration de domaine.
* **Serveur d'exploitation (OPS01) :**
  * Connexion interactive par compte utilisateur dédié (`anas`) disposant d'un accès `sudo` contrôlé.
  * Désactivation de l'accès root direct en SSH.

## 3. Matrice de Filtrage Pare-Feu et Ports d'Écoute
Les ports ouverts sont strictement limités aux besoins de communication inter-machines :

| Port / Protocole | Service | Sens du flux | Règle de sécurité / Justification |
| :--- | :--- | :--- | :--- |
| **TCP 22 (NAT 2222)** | OpenSSH | Hôte -> OPS01 | Administration console sécurisée par clé / mot de passe fort |
| **TCP 5985** | WinRM (HTTP) | OPS01 -> WIN-DC01 | Exécution Ansible via authentification NTLM |
| **TCP 9182** | Windows Exporter | OPS01 -> WIN-DC01 | Scrape Prometheus autorisé uniquement depuis l'IP d'OPS01 |
| **TCP 9100** | Node Exporter | Interne OPS01 | Scrape local des métriques système Linux |
| **TCP 9090** | Prometheus | Hôte -> OPS01 | Consultation métrologie (restreinte au réseau de TP) |
| **TCP 3000** | Grafana | Hôte -> OPS01 | Interface visuelle de métrologie |

## 4. Sécurisation du Transport WinRM et Authentification
* Le listener WinRM sur WIN-DC01 est restreint aux sous-réseaux locaux du laboratoire.
* L'authentification utilise le protocole NTLM adossé au domaine Active Directory (`infraready.test`), éliminant l'usage de comptes invités non sécurisés.

## 5. Protection des Données Sensibles (Git & Ansible)
* **Inventaire sécurisé :** Aucun mot de passe de production ou de domaine n'est versionné en clair. La variable `ansible_password` est paramétrée sous forme de variable d'environnement ou injectée via Ansible Vault.
* **Filtre `.gitignore` :** Exclusion systématique des journaux, des fichiers temporaires `.retry` et des fichiers d'état locaux.
* **Permissions des clés :** Les tokens d'accès GitHub (PAT) sont limités aux scopes strictement nécessaires (`repo`, `workflow`) et ne sont jamais stockés dans les dépôts publics.
