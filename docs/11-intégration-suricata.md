# 11. Intégration Suricata + Agent Wazuh

Suricata est un IDS/IPS réseau open source qui inspecte le trafic et génère des alertes (signatures, anomalies protocolaires). Une fois installé sur une machine (Linux de préférence, physique ou VM avec accès au trafic réseau à surveiller), on relie ses logs à Wazuh pour centraliser la détection réseau avec la détection endpoint (Sysmon) déjà en place.

> ⚠️ Prérequis : Suricata déjà installé et fonctionnel sur la machine (VM Linux recommandée, avec une interface réseau en mode promiscuous si tu veux inspecter tout le trafic du segment).

## 1. Localiser les logs Suricata

Par défaut, Suricata écrit ses alertes au format JSON dans :

```
/var/log/suricata/eve.json
```

Vérifier que Suricata tourne et génère bien des événements :

```bash
sudo systemctl status suricata
tail -f /var/log/suricata/eve.json
```

📸 _Capture d'écran : `eve.json` affichant des événements en temps réel_
`![eve.json en direct](../screenshots/11-suricata/eve-json-tail.png)`

## 2. Installer l'agent Wazuh sur la machine Suricata (si pas déjà fait)

Même principe que pour l'agent Windows (voir module 4), mais pour Linux :

```bash
curl -so wazuh-agent.deb https://packages.wazuh.com/4.x/apt/pool/main/w/wazuh-agent/wazuh-agent_4.x.x-1_amd64.deb
sudo WAZUH_MANAGER='<IP_VM_Wazuh>' dpkg -i ./wazuh-agent.deb
sudo systemctl daemon-reload
sudo systemctl enable wazuh-agent
sudo systemctl start wazuh-agent
```

> 💡 Adapte la commande exacte à la version de Wazuh utilisée — la commande complète et à jour est toujours disponible via `Agents → Deploy new agent` dans le dashboard.

📸 _Capture d'écran : agent Linux visible et actif dans la liste des agents Wazuh_
`![Agent Linux actif](../screenshots/11-suricata/agent-linux-actif.png)`

## 3. Configurer l'agent pour collecter les logs Suricata

Éditer la configuration de l'agent :

```
/var/ossec/etc/ossec.conf
```

Ajouter dans la section `<ossec_config>` :

```xml
<localfile>
  <log_format>json</log_format>
  <location>/var/log/suricata/eve.json</location>
</localfile>
```

Redémarrer l'agent :

```bash
sudo systemctl restart wazuh-agent
```

📸 _Capture d'écran : extrait de `ossec.conf` avec le bloc Suricata ajouté_
`![Config ossec.conf Suricata](../screenshots/11-suricata/ossec-conf-suricata.png)`

## 4. Activer le module Suricata côté Wazuh manager (decoders/rules intégrés)

Wazuh embarque nativement des **décodeurs et règles Suricata** (dans les versions récentes du ruleset). Vérifier leur présence sur le manager :

```bash
sudo find /var/ossec/ruleset/ -iname "*suricata*"
```

Si les fichiers sont présents (`0350-suricata_rules.xml`, decoders associés), aucune action supplémentaire n'est nécessaire : Wazuh saura parser automatiquement le JSON `eve.json` et générer des alertes classées par sévérité.

## 5. Générer un événement de test

Provoquer une alerte simple, par exemple en déclenchant une règle de test Suricata classique (ping ICMP vers une IP de test documentée, ou requête HTTP vers un domaine connu comme "testmynids.org" — utilisé justement pour tester les IDS sans risque).

📸 _Capture d'écran : alerte Suricata générée par l'événement de test_
`![Alerte Suricata de test](../screenshots/11-suricata/alerte-test-suricata.png)`

## 6. Vérifier la remontée dans le dashboard Wazuh

Dashboard Wazuh → `Threat Intelligence` / `Security events` → filtrer par `agent.name` (la machine Suricata) et rechercher les champs `data.suricata.eve.alert.signature`.

📸 _Capture d'écran : alertes Suricata visibles dans le dashboard Wazuh, avec la signature et la sévérité_
`![Alertes Suricata dans Wazuh](../screenshots/11-suricata/suricata-events-dashboard.png)`

## Pourquoi cette intégration est utile

- Sysmon donne la vue **endpoint** (process, fichiers, registre) ; Suricata donne la vue **réseau** (signatures d'exploitation, C2, scans).
- Corréler les deux dans un seul dashboard permet de reconstituer une chaîne d'attaque complète : détection réseau (Suricata) → confirmation endpoint (Sysmon) → décision (isolement, blocage).
- C'est une architecture représentative d'un vrai SOC, où IDS réseau et EDR/agent endpoint alimentent le même SIEM.

## Problèmes fréquents

| Symptôme                                                           | Cause probable                                                                   | Solution                                                      |
| ------------------------------------------------------------------ | -------------------------------------------------------------------------------- | ------------------------------------------------------------- |
| Rien dans `eve.json`                                               | Interface réseau mal configurée (pas en mode promiscuous, ou mauvaise interface) | Vérifier `/etc/suricata/suricata.yaml`, section `af-packet`   |
| Logs présents mais rien dans Wazuh                                 | Bloc `<localfile>` absent/mal placé, ou agent non redémarré                      | Vérifier `ossec.conf`, `systemctl restart wazuh-agent`        |
| Événements dans Wazuh mais non enrichis (pas de signature lisible) | Ruleset Suricata de Wazuh absent/obsolète                                        | Mettre à jour le ruleset du manager (`wazuh-manager` package) |

➡️ Module suivant à définir selon la suite du cours (threat hunting, communication/rapports...).
