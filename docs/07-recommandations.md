# 6. Recommandations et bonnes pratiques

## Sécurité

- **Changer immédiatement** les mots de passe par défaut (console `wazuh-user` et dashboard `admin`) après le premier démarrage de l'OVA.
- Ne jamais exposer le dashboard Wazuh directement sur Internet sans reverse proxy + authentification renforcée (MFA si possible).
- Restreindre les ports 1514/1515 (agents) et 55000 (API) au réseau du lab uniquement via le pare-feu de la VM ou de l'hôte.
- Renouveler les certificats auto-signés générés par défaut si l'atelier doit durer dans le temps.

## Performance / ressources

- Allouer au minimum 4 vCPU / 8 Go de RAM à la VM Wazuh si vous ajoutez plusieurs agents (au-delà de 5-10 agents, les performances de l'OVA tout-en-un se dégradent).
- Surveiller l'espace disque : l'indexer (OpenSearch) peut grossir vite selon le volume de logs — prévoir un disque extensible.
- Pour un usage prolongé, envisager une architecture éclatée (manager / indexer / dashboard sur des nœuds séparés) plutôt que l'OVA tout-en-un.

## Bonnes pratiques pédagogiques (pour un atelier reproductible)

- Faire des **snapshots VirtualBox** après chaque étape clé (post-import OVA, post-configuration réseau, post-enrôlement agent) pour permettre de revenir en arrière facilement pendant l'atelier.
- Documenter systématiquement l'IP obtenue par chaque VM (elle peut changer selon le mode réseau choisi).
- Prévoir un jeu de données de test (ex: scénario d'attaque simulée simple) pour illustrer concrètement la détection.

## Pour aller plus loin

- Ajouter un second agent (Linux) pour comparer la remontée de logs multi-OS.
- Intégrer Wazuh avec un outil de threat intelligence (MISP) ou de gestion d'incidents (TheHive).
- Explorer les règles personnalisées (`local_rules.xml`) pour créer des détections spécifiques à l'atelier.

➡️ Étape suivante : [Analyse d'un hash de malware (partie 1)](08-analyse-hash-malware-1.md)

## Ressources utiles

- Documentation officielle Wazuh : https://documentation.wazuh.com/
- Forum communautaire Wazuh : https://groups.google.com/g/wazuh
- VirtualBox – documentation réseau : https://www.virtualbox.org/manual/ch06.html
