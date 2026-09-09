# 9. Étude : Analyse d'un domaine et une IP

Fiche-mémoire opérationnelle pour l'analyse technique d'une IP ou d'un domaine suspect (perspective Blue Team). Cette étape s'appuie sur les hashs et IOCs identifiés lors des modules précédents (analyse de hash de malware) : un malware contacte souvent des domaines/IPs de commande et contrôle (C2) qu'il faut à leur tour investiguer.

## Objectifs rapides (priorités)

1. **Enrichir l'artefact** : owner, ASN, reverse DNS, géolocalisation, bannières, certificats, historique DNS/WHOIS.
2. **Récupérer réputation & signaux** : rapports d'abus, scanners, bruit vs activité ciblée.
3. **Lister les IOCs associés** : autres IPs, domaines, sous-domaines, URLs, certificats, ASN.
4. **Identifier "first seen / last seen"** et campagnes connues.
5. **Corréler dans SIEM/EDR/proxy/DNS logs** → construire une timeline → décider des actions (containment / blocage / hunts).

## Sources prioritaires

| Source | Utilité |
|---|---|
| **VirusTotal** | Rapport IP/domaine : verdicts multi-moteurs, graphes de relations, commentaires |
| **Shodan** | Bannières, ports, services exposés, historique des bannières |
| **Censys** | Indexation d'hôtes, certificats TLS, métadonnées |
| **SecurityTrails** | Historique DNS & WHOIS (séries temporelles) |
| **AbuseIPDB** | Rapports communautaires d'abus et réputation IP |
| **GreyNoise** | Distinguer scans massifs (bruit) vs activité ciblée |
| **urlscan.io** | Captures et rendu de pages pour URLs/domaines (ressources, redirections) |
| **PassiveTotal / RiskIQ** | Corrélation passive DNS, domaines apparentés, infrastructure liée |
| **crt.sh** | Recherche Certificate Transparency pour trouver les certificats TLS liés au domaine |

📸 *Capture d'écran : rapport VirusTotal pour un domaine/IP suspect*
`![Rapport VirusTotal domaine](../screenshots/09-domaine-ip/virustotal-domaine.png)`

## Workflow pas-à-pas

### Triage rapide (0–10 min)

1. Rapport multi-source VirusTotal pour la cible (IP ou domaine).
2. Réputation communautaire via AbuseIPDB et GreyNoise.
3. Bannières et services exposés via Shodan / Censys.
4. WHOIS / historique DNS avec SecurityTrails.

📸 *Capture d'écran : résultat AbuseIPDB / GreyNoise pour une IP testée*
`![Réputation IP](../screenshots/09-domaine-ip/abuseipdb-greynoise.png)`

### Enrichissement profond (10–60 min)

- **urlscan.io** : captures publiques → ressources, scripts, endpoints POST, redirections, screenshots.
- **crt.sh** : certificats TLS et SANs → découverte de sous-domaines et certificats récents.
- **PassiveTotal / RiskIQ** : pivot sur l'infrastructure (autres domaines sur le même ASN/subnet).
- **Shodan / Censys** : bannières complètes (headers HTTP, versions de services) pour détecter des vecteurs d'exploitation potentiels.
- Rechercher les occurrences dans OSINT / MISP / OTX / blogs / réseaux sociaux pour identifier une campagne connue.

📸 *Capture d'écran : recherche crt.sh sur un domaine, liste des certificats/SANs*
`![Recherche crt.sh](../screenshots/09-domaine-ip/crtsh-recherche.png)`

### Corrélation SIEM / EDR / logs (en parallèle)

1. Rechercher les connexions vers l'IP/le domaine dans : logs DNS, proxy, firewall, NetFlow, événements réseau EDR.
2. Identifier téléchargements HTTP/HTTPS, user-agents suspects, requêtes POST vers endpoints malveillants.
3. Construire une **timeline** reliant événements réseau et événements endpoint.
4. Si la présence est confirmée : isoler le(s) endpoint(s), bloquer domaine/IP (DNS sinkhole, proxy, FW) et lancer la remédiation.

📸 *Capture d'écran : recherche de connexions vers l'IOC dans le dashboard Wazuh*
`![Corrélation Wazuh](../screenshots/09-domaine-ip/correlation-wazuh.png)`

## Outils OSS recommandés (recon / enrichissement)

- **OWASP Amass** — discovery et cartographie de la surface d'attaque (subdomain enumeration, graphing)
- **SpiderFoot** — automatisation OSINT et corrélation (interface web, pipelines d'enrichissement)
- **Recon-ng** — framework modulaire pour l'OSINT et la collecte automatisée
- **theHarvester** — collecte d'emails, sous-domaines, noms et IPs publics
- **Sublist3r** et variantes — énumération rapide de sous-domaines

Ces outils automatisent l'énumération et produisent des listes de sous-domaines/endpoints à enrichir ensuite via Shodan, Censys, VirusTotal et urlscan.

## Enrichissements et pivots techniques utiles

- **WHOIS & historique registrar** — registrant, changements récents, usage de privacy/WHOIS protection, contacts d'abuse
- **Passive DNS** — anciens A/CNAME/MX associés au domaine (PassiveTotal / SecurityTrails)
- **Certificate Transparency (crt.sh)** — SANs non publiés ailleurs (sous-domaines émergents)
- **Pivot ASN / netblock** — recenser les autres hôtes du même ASN ou /24 pour repérer l'infrastructure corrélée
- **JA3 / JA3S** — si tu disposes de métadonnées TLS ou d'un pcap, comparer les empreintes JA3 à des listes connues de malwares

## Priorisation des actions (si la cible est confirmée malveillante)

1. **Contenir** : bloquer domaine/IP au niveau DNS (sinkhole), proxy et firewall.
2. **Hunt** : rechercher toutes les occurrences dans les logs (DNS, proxy, NGFW, EDR).
3. **Remédiation** : isoler les endpoints infectés, effacer les payloads, réinitialiser les credentials si nécessaire.
4. **Signature & partage** : préparer les IOCs (IPs, domaines, certificats) et les diffuser au SOC / feeds de threat intel (MISP/OTX).
5. **Reporting** : produire un rapport avec preuves (screenshots urlscan, exports JSON VirusTotal, sorties Shodan/Censys) et timeline d'investigation.

## Automatisation & bonnes pratiques

- Conserver le **JSON brut** de chaque API consultée (VirusTotal, Shodan, Censys, urlscan) pour traçabilité et reproductibilité.
- Normaliser tous les timestamps en **UTC**, stocker la source + request_id pour chaque appel API.
- Automatiser l'enrichissement : dès qu'un domaine est détecté, lancer automatiquement crt.sh, passive DNS, urlscan, Shodan, et consolider les résultats.
- Utiliser GreyNoise et AbuseIPDB pour filtrer le bruit (scans massifs vs activité ciblée) et prioriser les investigations humaines.
- Conserver une preuve immuable (captures, screenshots, exports JSON) pour audits et reporting.

## Checklist imprimable

- [ ] Récupérer le résumé VirusTotal pour la cible (IP / domaine)
- [ ] Vérifier AbuseIPDB et GreyNoise pour la réputation et la nature du trafic
- [ ] Interroger Shodan / Censys pour les bannières et services exposés
- [ ] Récupérer l'historique DNS / WHOIS via SecurityTrails ou PassiveTotal
- [ ] Utiliser urlscan et crt.sh pour les preuves visuelles et les certificats TLS
- [ ] Corréler dans SIEM / EDR : DNS / Proxy / FW / NetFlow / logs Endpoint
- [ ] Décider des actions : block, hunt, containment, remédiation
- [ ] Documenter & exporter le rapport d'investigation (JSON/PDF)

➡️ Étape suivante : [Exercice : Créer et tester vos règles YARA & Sigma](10-exercice-yara-sigma.md)
