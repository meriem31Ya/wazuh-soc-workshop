# 10. Exercice : Créer et tester vos règles YARA & Sigma

**Objectif :** écrire, déboguer et valider des règles YARA et Sigma en utilisant des outils en ligne (éditeur + convertisseur) et un pack d'échantillons de test.

> 📦 Le cours fournit un ZIP contenant des samples pour tester tes règles. Place-le dans un dossier `samples/` à la racine de cet exercice (non versionné dans le dépôt — voir `.gitignore`).

## Structure de travail suggérée

```
yara-sigma-exercice/
├── rules/
│   ├── yara/
│   │   └── YourRule.yar
│   └── sigma/
│       └── your_rule.yml
└── samples/          # fichiers de test (non versionnés)
```

## Partie 1 — Règles YARA

### Qu'est-ce que YARA ?

YARA permet d'écrire des règles de détection basées sur des motifs (chaînes de caractères, séquences d'octets, conditions logiques) pour identifier des familles de malwares ou des fichiers suspects — que ce soit sur disque ou en mémoire.

### Outils en ligne pour s'entraîner

- **YARA Playground / Online tester** : permet d'écrire et tester une règle sans installation locale
- Éditeur avec coloration syntaxique pour repérer rapidement les erreurs de syntaxe

📸 *Capture d'écran : éditeur YARA en ligne avec une règle en cours d'écriture*
`![Éditeur YARA en ligne](../screenshots/10-yara-sigma/editeur-yara-online.png)`

### Exemple de règle YARA simple

```yara
rule Suspicious_PowerShell_EncodedCommand
{
    meta:
        description = "Détecte une commande PowerShell encodée en base64"
        author = "Atelier Wazuh"
        date = "2026-09"

    strings:
        $enc1 = "-EncodedCommand" nocase
        $enc2 = "-enc " nocase
        $ps = "powershell" nocase

    condition:
        $ps and ($enc1 or $enc2)
}
```

### Tester la règle en local

```bash
yara -r rules/yara/YourRule.yar samples/
```

- `-r` : recherche récursive dans le dossier `samples/`
- Le nom du fichier scanné s'affiche à côté du nom de la règle si elle matche

📸 *Capture d'écran : résultat de `yara -r` en ligne de commande sur le dossier samples*
`![Résultat scan YARA CLI](../screenshots/10-yara-sigma/yara-cli-resultat.png)`

### Débogage courant

| Erreur | Cause probable |
|---|---|
| `syntax error, unexpected...` | Accolade ou parenthèse manquante, section `condition:` mal formée |
| Règle qui ne matche jamais | Chaîne mal orthographiée, `nocase` manquant, condition trop stricte |
| Règle qui matche tout | Condition trop permissive (ex: `any of them` sur une seule chaîne courante) |

## Partie 2 — Règles Sigma

### Qu'est-ce que Sigma ?

Sigma est un format générique de règles de détection pour les logs (indépendant du SIEM). Une règle Sigma peut ensuite être **convertie** vers le langage de requête d'un SIEM précis (Wazuh, Splunk, Elastic, Microsoft Sentinel...).

### Outil de conversion en ligne

- **Uncoder.io** (ou équivalent) : colle une règle Sigma YAML, choisis la plateforme cible, récupère la requête convertie.

📸 *Capture d'écran : conversion d'une règle Sigma vers une requête cible via un convertisseur en ligne*
`![Conversion Sigma en ligne](../screenshots/10-yara-sigma/sigma-conversion-online.png)`

### Exemple de règle Sigma simple

```yaml
title: PowerShell Encoded Command Execution
id: 8e1c8f00-0000-4c00-9000-000000000001
status: experimental
description: Détecte l'exécution de PowerShell avec une commande encodée en base64
logsource:
    category: process_creation
    product: windows
detection:
    selection:
        Image|endswith: '\powershell.exe'
        CommandLine|contains:
            - '-EncodedCommand'
            - '-enc '
    condition: selection
level: medium
tags:
    - attack.execution
    - attack.t1059.001
```

### Tester la règle Sigma

1. Valider la syntaxe YAML (indentation stricte, pas de tabulations).
2. Convertir la règle vers le format cible avec l'outil en ligne.
3. Si tu as déjà des événements Sysmon dans Wazuh (voir module 5), rejouer manuellement la requête convertie sur le dashboard pour vérifier qu'elle matche bien les événements attendus (ex : lancement de `notepad.exe` avec une commande encodée simulée).

📸 *Capture d'écran : requête convertie exécutée dans le dashboard Wazuh, résultats correspondants*
`![Requête Sigma convertie testée dans Wazuh](../screenshots/10-yara-sigma/sigma-test-wazuh.png)`

## Checklist de l'exercice

- [ ] Écrire une règle YARA détectant un pattern simple (chaîne, import suspect, etc.)
- [ ] Tester la règle YARA avec `yara -r` sur le pack de samples fourni
- [ ] Corriger les faux positifs / faux négatifs identifiés
- [ ] Écrire une règle Sigma équivalente ou complémentaire (basée sur un événement de process_creation)
- [ ] Convertir la règle Sigma vers le format de la plateforme cible
- [ ] Vérifier que la règle convertie matche bien les événements attendus dans Wazuh
- [ ] Documenter chaque règle (meta/description, faux positifs connus, tags MITRE ATT&CK)

## Bonnes pratiques

- Toujours documenter le **but** de la règle et ses **limites connues** (faux positifs attendus).
- Taguer les règles Sigma avec les techniques **MITRE ATT&CK** correspondantes — ça facilite la priorisation et le reporting.
- Versionner tes règles dans `rules/` (mais jamais les samples malveillants bruts — garder `samples/` hors du dépôt Git, voir `.gitignore`).

➡️ Étape suivante : Techniques d'analyse des logs (Windows, Linux, Réseau)
*(Module à ajouter dès que son contenu sera disponible.)*
