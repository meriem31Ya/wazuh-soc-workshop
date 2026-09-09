# 2. Déploiement de l'OVA Wazuh

## Téléchargement

Récupérer l'appliance virtuelle officielle Wazuh (format OVA, tout-en-un : manager + indexer + dashboard) depuis la documentation officielle :
👉 https://documentation.wazuh.com/current/deployment-options/virtual-machine/virtual-machine.html

> ⚠️ L'OVA contient l'ensemble de la stack Wazuh préconfigurée. C'est l'option la plus rapide pour un atelier/lab, mais elle est déconseillée en production (préférer une installation manuelle multi-nœuds).

## Étapes d'import

1. Ouvrir VirtualBox → `Fichier` → `Importer un appareil virtuel`.
2. Sélectionner le fichier `.ova` téléchargé.
3. Vérifier les paramètres proposés :
   - RAM : minimum 4 Go (8 Go recommandés pour un usage confortable)
   - CPU : 2 vCPU minimum
   - Disque : conserver le format proposé (généralement dynamique)
4. Cliquer sur `Importer` et attendre la fin de l'extraction.

📸 *Capture d'écran : fenêtre d'import de l'appareil virtuel avec les paramètres*
`![Import OVA](../screenshots/01-import-ova/import-ova-parametres.png)`

## Premier démarrage

1. Sélectionner la VM Wazuh importée → `Démarrer`.
2. Attendre la fin du boot (services Wazuh manager, indexer et dashboard qui démarrent).
3. Une fois le login affiché en console, noter l'adresse IP attribuée à la VM (affichée généralement au login, ou via `ip a` après connexion).

📸 *Capture d'écran : console de la VM au démarrage avec l'IP affichée*
`![Boot Wazuh VM](../screenshots/01-import-ova/boot-console-ip.png)`

## Identifiants par défaut

- Utilisateur console : `wazuh-user` (mot de passe généré/affiché au premier boot — **à noter immédiatement**)
- Dashboard web : `admin` / mot de passe généré (souvent affiché en console ou dans la documentation de la version téléchargée)

> 🔐 **Recommandation** : changez ces identifiants dès le premier accès (voir [06-recommandations.md](06-recommandations.md)).

➡️ Étape suivante : [Configuration réseau](03-configuration-reseau.md)
