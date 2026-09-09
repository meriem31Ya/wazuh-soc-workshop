# 3. Configuration réseau

Pour que la VM Wazuh et la future VM Windows 10 (agent) puissent communiquer, il faut choisir un mode réseau adapté dans VirtualBox.

## Mode recommandé : Réseau interne + NAT, ou Bridge

### Option A — Mode Bridge (le plus simple pour un atelier)

1. Sélectionner la VM Wazuh → `Configuration` → `Réseau`.
2. Adaptateur 1 : `Accès par pont (Bridge)`, sélectionner la carte réseau physique de l'hôte.
3. Faire de même pour la VM Windows 10.
4. Les deux VM obtiendront une IP sur le même réseau que votre machine hôte (via DHCP du routeur) et pourront se joindre directement.

📸 *Capture d'écran : configuration réseau en mode Bridge*
`![Config réseau Bridge](../screenshots/02-config-reseau/bridge-config.png)`

### Option B — Réseau interne isolé (recommandé pour un lab cloisonné)

1. Créer un `Réseau interne` (ex: `intnet-wazuh`) commun aux deux VM.
2. Ajouter un second adaptateur en NAT sur chaque VM pour l'accès Internet (mises à jour, téléchargements).
3. Configurer des IP statiques sur le réseau interne pour éviter les changements d'adresse.

📸 *Capture d'écran : configuration réseau interne + NAT*
`![Config réseau interne](../screenshots/02-config-reseau/reseau-interne-nat.png)`

## Vérification de connectivité

Depuis la VM Wazuh (ou l'hôte), tester l'accès au dashboard :
```
https://<IP_de_la_VM_Wazuh>
```

Depuis la future VM Windows 10, un `ping` vers l'IP de la VM Wazuh doit fonctionner une fois les deux VM démarrées.

## Points d'attention

- En mode NAT simple (par défaut), les VM ne peuvent pas se joindre entre elles sans redirection de ports — éviter ce mode pour cet atelier.
- Notez bien l'IP finale de la VM Wazuh, elle sera nécessaire pour enrôler l'agent Windows.

➡️ Étape suivante : [VM Windows 10 et installation de l'agent](04-vm-windows10-agent.md)
