# 5. Installation de Sysmon (après l'installation de l'agent Wazuh)

Sysmon (System Monitor, Microsoft Sysinternals) enrichit considérablement les logs Windows collectés par l'agent Wazuh : créations de process, connexions réseau, modifications du registre, chargements de DLL, etc. C'est une étape très recommandée pour un atelier de détection réaliste.

> ⚠️ Fais cette étape **après** avoir installé et vérifié que l'agent Wazuh est bien "Active" (voir étape 4). Sysmon ne sert à rien sans un agent qui remonte ses logs.

## 1. Télécharger Sysmon

1. Ouvrir la page officielle Microsoft Sysinternals : https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon
2. Cliquer sur **Download Sysmon** et enregistrer le fichier ZIP (ex : `Sysmon.zip`).

📸 *Capture d'écran : page de téléchargement Sysinternals Sysmon*
`![Téléchargement Sysmon](../screenshots/05-sysmon/telechargement-sysmon.png)`

## 2. Extraire les fichiers

1. Décompresser le ZIP dans un dossier, par exemple : `C:\Tools\Sysmon\`
2. Vérifier la présence de `Sysmon64.exe` (64-bit) et `Sysmon.exe` (32-bit).

## 3. Installer Sysmon

1. Ouvrir **PowerShell en tant qu'Administrateur**.
2. Se placer dans le dossier d'installation, puis lancer l'installation avec la configuration par défaut :

```powershell
cd C:\Tools\Sysmon
.\Sysmon64.exe -accepteula -i
```

📸 *Capture d'écran : installation de Sysmon en ligne de commande*
`![Installation Sysmon](../screenshots/05-sysmon/install-sysmon-powershell.png)`

> 💡 **Pour aller plus loin** : la config par défaut de Sysmon est assez minimaliste. Une fois à l'aise avec les bases, tu peux réinstaller avec une configuration communautaire plus complète (ex : [sysmon-config de SwiftOnSecurity](https://github.com/SwiftOnSecurity/sysmon-config)) via `.\Sysmon64.exe -c sysmonconfig-export.xml`.

## 4. Vérifier l'installation

Vérifier l'état du service :

```powershell
Get-Service -Name Sysmon
```

Le service doit apparaître `Running`.

Consulter l'observateur d'événements :
`Event Viewer → Applications and Services Logs → Microsoft → Windows → Sysmon → Operational`

📸 *Capture d'écran : journal des événements Sysmon dans l'Observateur d'événements*
`![Journal Sysmon](../screenshots/05-sysmon/observateur-evenements-sysmon.png)`

## 5. Vérifications post-installation

* Confirmer la génération d'événements dans le journal `Microsoft-Windows-Sysmon/Operational`.
* Test simple : lancer `notepad.exe` et vérifier la présence d'un **Event ID 1 (Process Create)** dans le journal.

📸 *Capture d'écran : Event ID 1 généré après lancement de notepad.exe*
`![Test Event ID 1](../screenshots/05-sysmon/test-eventid1-notepad.png)`

## 6. Configurer l'agent Wazuh pour collecter les logs Sysmon

Sur la VM Windows, éditer le fichier de configuration de l'agent :
`C:\Program Files (x86)\ossec-agent\ossec.conf`

Ajouter (ou vérifier la présence de) ce bloc dans la section `<ossec_config>` :

```xml
<localfile>
  <location>Microsoft-Windows-Sysmon/Operational</location>
  <log_format>eventchannel</log_format>
</localfile>
```

Puis redémarrer le service agent :

```powershell
Restart-Service WazuhSvc
```

📸 *Capture d'écran : extrait du fichier ossec.conf avec le bloc Sysmon ajouté*
`![Config ossec.conf Sysmon](../screenshots/05-sysmon/ossec-conf-sysmon.png)`

## 7. Vérifier la remontée des événements Sysmon dans Wazuh

Sur le dashboard Wazuh, filtrer les événements par `agent.name` et rechercher `win.system.providerName: Microsoft-Windows-Sysmon`. Tu dois voir apparaître les événements Sysmon, dont l'Event ID 1 du test `notepad.exe`.

📸 *Capture d'écran : événements Sysmon visibles dans le dashboard Wazuh*
`![Événements Sysmon dans Wazuh](../screenshots/05-sysmon/sysmon-events-dashboard.png)`

## Pourquoi Sysmon change la donne

- Les logs Windows natifs (Sécurité, Système) sont souvent trop pauvres pour de la vraie détection.
- Sysmon capture la ligne de commande complète des process créés, les hashs de fichiers exécutés, les connexions réseau sortantes par process, etc.
- C'est un composant central pour tout travail d'analyste SOC / CSIRT : la majorité des cas d'usage de détection (process suspects, exécutions LOLBins, connexions C2...) reposent sur des événements Sysmon.

## Problèmes fréquents

| Symptôme | Cause probable | Solution |
|---|---|---|
| Aucun événement Sysmon dans Wazuh | Bloc `<localfile>` absent ou mal placé dans `ossec.conf` | Vérifier l'indentation XML, redémarrer le service agent |
| Sysmon installé mais pas de journal visible | Installation sans droits administrateur | Relancer PowerShell en tant qu'administrateur |
| `Get-Service -Name Sysmon` renvoie une erreur | Nom du service différent selon la version (Sysmon vs Sysmon64) | Essayer `Get-Service -Name Sysmon64` |
| Trop de bruit / volume de logs élevé | Configuration par défaut trop permissive à long terme | Passer à une config communautaire type `sysmon-config` |

➡️ Étape suivante : [Vérification des alertes et du dashboard](06-verification-alertes.md)
