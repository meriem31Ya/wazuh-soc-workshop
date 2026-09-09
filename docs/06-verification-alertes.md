# 5. Vérification des alertes et du dashboard

## Exploration du dashboard

1. Se connecter au dashboard : `https://<IP_VM_Wazuh>`.
2. Menu principal → `Security events` (ou `Threat Hunting` selon la version) pour voir les logs remontés par l'agent Windows.

📸 *Capture d'écran : vue Security events avec logs de l'agent Windows*
`![Security events](../screenshots/06-alertes/security-events.png)`

## Générer un événement de test

Pour valider que la chaîne de collecte fonctionne de bout en bout, générer un événement simple sur la VM Windows :

- Tenter une connexion avec un mauvais mot de passe (déclenche un log d'échec d'authentification Windows, event ID 4625).
- Vérifier son apparition dans le dashboard sous quelques secondes/minutes.

📸 *Capture d'écran : alerte correspondant à l'échec d'authentification*
`![Alerte test](../screenshots/06-alertes/alerte-echec-auth.png)`

## Filtres et règles

- Explorer `Management` → `Rules` pour voir les règles de détection actives.
- Utiliser la barre de recherche du dashboard pour filtrer par `agent.name`, `rule.level`, ou `rule.description`.

📸 *Capture d'écran : filtrage des événements par agent*
`![Filtrage agent](../screenshots/06-alertes/filtrage-par-agent.png)`

## Modules complémentaires à explorer (optionnel)

- **File Integrity Monitoring (FIM)** : surveillance de fichiers/dossiers critiques.
- **Vulnerability Detection** : scan des vulnérabilités connues sur l'agent.
- **Security Configuration Assessment (SCA)** : audit de configuration selon des benchmarks (CIS).

➡️ Étape suivante : [Recommandations et bonnes pratiques](07-recommandations.md)
