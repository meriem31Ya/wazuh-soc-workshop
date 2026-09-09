# 4. Création de la VM Windows 10 et installation de l'agent Wazuh

## Téléchargement de l'ISO

Télécharger l'ISO Windows 10 depuis le site officiel Microsoft :
👉 https://www.microsoft.com/fr-fr/software-download/windows10ISO

## Création de la VM

1. VirtualBox → `Nouvelle` machine virtuelle.
2. Type : Microsoft Windows, Version : Windows 10 (64-bit).
3. RAM : 4 Go minimum.
4. Disque : 50-60 Go (dynamique).
5. Monter l'ISO téléchargé dans le lecteur virtuel (`Configuration` → `Stockage` → ajouter le fichier ISO).
6. Appliquer la même configuration réseau que la VM Wazuh (voir étape 3).

📸 *Capture d'écran : assistant de création de VM Windows 10*
`![Création VM Windows 10](../screenshots/04-agent-windows/creation-vm-win10.png)`

7. Démarrer la VM et suivre l'installation standard de Windows 10.
8. Une fois Windows installé, installer les **Guest Additions** VirtualBox (meilleure résolution, presse-papier partagé, etc.).

## Installation de l'agent Wazuh

1. Se connecter au dashboard Wazuh : `https://<IP_VM_Wazuh>`.
2. Menu → `Agents` → `Ajouter un agent (Deploy new agent)`.
3. Choisir `Windows` comme plateforme.
4. Renseigner l'IP/adresse du serveur Wazuh (Wazuh manager) — champ `WAZUH_MANAGER`.
5. Copier la commande PowerShell générée automatiquement par l'interface.

📸 *Capture d'écran : écran "Deploy new agent" du dashboard avec la commande générée*
`![Deploy new agent](../screenshots/04-agent-windows/deploy-agent-dashboard.png)`

6. Dans la VM Windows 10, ouvrir **PowerShell en administrateur**.
7. Coller et exécuter la commande copiée (elle télécharge et installe le package MSI de l'agent, puis configure le manager automatiquement).
8. Démarrer le service agent :
   ```powershell
   NET START WazuhSvc
   ```

📸 *Capture d'écran : installation de l'agent via PowerShell*
`![Installation agent PowerShell](../screenshots/04-agent-windows/install-agent-powershell.png)`

## Vérification de l'enrôlement

Retourner sur le dashboard Wazuh → `Agents` : le nouvel agent Windows doit apparaître avec le statut **Active**.

📸 *Capture d'écran : agent Windows visible et actif dans la liste des agents*
`![Agent actif](../screenshots/04-agent-windows/agent-actif-liste.png)`

## Problèmes fréquents

| Symptôme | Cause probable | Solution |
|---|---|---|
| Agent "Never connected" | Pare-feu Windows bloque le port 1514/1515 | Autoriser les ports sortants TCP/UDP 1514-1515 |
| Agent "Disconnected" après quelques minutes | Mauvaise IP du manager renseignée | Réinstaller l'agent avec la bonne IP, redémarrer le service |
| Aucune commande générée sur le dashboard | Version du navigateur/cache | Vider le cache ou tester avec un autre navigateur |

➡️ Étape suivante : [Installation de Sysmon](05-installation-sysmon.md)
