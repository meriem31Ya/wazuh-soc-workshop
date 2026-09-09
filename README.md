# 🛡️ Atelier Wazuh – Déploiement sur VirtualBox avec agent Windows 10

Ce dépôt documente un atelier pratique de déploiement de **Wazuh** (SIEM open source) via l'appliance OVA officielle, sur **VirtualBox**, avec un agent installé sur une machine **Windows 10**.

L'objectif est de fournir un guide reproductible pour :
- Déployer rapidement un serveur Wazuh (manager + indexer + dashboard) via l'OVA
- Créer une VM Windows 10 comme cible à surveiller
- Installer et enrôler l'agent Wazuh sur cette VM
- Vérifier la remontée des logs et des alertes dans le dashboard

## 📋 Prérequis

| Élément | Lien / Détail |
|---|---|
| VirtualBox (dernière version) | https://www.virtualbox.org/wiki/Downloads |
| OVA Wazuh officiel | https://documentation.wazuh.com/current/deployment-options/virtual-machine/virtual-machine.html |
| ISO Windows 10 | https://www.microsoft.com/fr-fr/software-download/windows10ISO |
| RAM hôte recommandée | 16 Go minimum (8 Go pour Wazuh + 4 Go pour Windows 10) |
| Espace disque | ~80 Go libres |
| CPU | Virtualisation (VT-x/AMD-V) activée dans le BIOS |

## 📁 Structure du dépôt

```
wazuh-workshop/
├── README.md                     # Ce fichier
├── docs/
│   ├── 01-installation-virtualbox.md
│   ├── 02-deploiement-wazuh-ova.md
│   ├── 03-configuration-reseau.md
│   ├── 04-vm-windows10-agent.md
│   ├── 05-installation-sysmon.md
│   ├── 06-verification-alertes.md
│   ├── 07-recommandations.md
│   ├── 08-analyse-hash-malware-1.md
│   ├── 09-etude-domaine-ip.md
│   └── 10-exercice-yara-sigma.md
└── screenshots/
    ├── 01-import-ova/
    ├── 02-config-reseau/
    ├── 03-dashboard-wazuh/
    ├── 04-agent-windows/
    ├── 05-sysmon/
    ├── 06-alertes/
    ├── 08-hash-malware/
    ├── 09-domaine-ip/
    └── 10-yara-sigma/
```

## 🚀 Sommaire de l'atelier

1. [Installation de VirtualBox](docs/01-installation-virtualbox.md)
2. [Déploiement de l'OVA Wazuh](docs/02-deploiement-wazuh-ova.md)
3. [Configuration réseau](docs/03-configuration-reseau.md)
4. [Création de la VM Windows 10 et installation de l'agent](docs/04-vm-windows10-agent.md)
5. [Installation de Sysmon](docs/05-installation-sysmon.md)
6. [Vérification des alertes et du dashboard](docs/06-verification-alertes.md)
7. [Recommandations et bonnes pratiques](docs/07-recommandations.md)
8. [Analyse d'un hash de malware (partie 1)](docs/08-analyse-hash-malware-1.md)
9. [Étude : Analyse d'un domaine et une IP](docs/09-etude-domaine-ip.md)
10. [Exercice : Créer et tester vos règles YARA & Sigma](docs/10-exercice-yara-sigma.md)

## ⚠️ Avertissement

Cet atelier est destiné à un usage **pédagogique et de test en environnement isolé** (lab local). Les identifiants par défaut de l'OVA Wazuh doivent être changés avant toute exposition, même interne.

## 📄 Licence

MIT — libre d'utilisation et de partage, mention de la source appréciée.
