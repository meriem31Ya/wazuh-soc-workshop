# 1. Installation de VirtualBox

## Étapes

1. Télécharger VirtualBox depuis le site officiel : https://www.virtualbox.org/wiki/Downloads
2. Choisir le package correspondant à votre OS hôte (Windows, macOS, Linux).
3. Lancer l'installeur et suivre l'assistant (options par défaut suffisent).
4. Installer également le **VirtualBox Extension Pack** (utile pour l'USB 3.0 et le RDP).
5. Redémarrer la machine si demandé.
6. Vérifier que la virtualisation matérielle (VT-x/AMD-V) est activée dans le BIOS/UEFI — sinon les VM ne démarreront pas correctement.

📸 *Capture d'écran : écran d'accueil de VirtualBox après installation*
`![Accueil VirtualBox](../screenshots/01-import-ova/virtualbox-accueil.png)`

## Vérification

- Ouvrir VirtualBox → `Aide` → `À propos de VirtualBox` pour confirmer la version installée.
- Aucune VM ne doit être présente à ce stade (installation propre).

## Points d'attention

- Sur certains PC portables récents, la virtualisation peut être désactivée par défaut dans le BIOS.
- Si Hyper-V (Windows) est actif, il peut entrer en conflit avec VirtualBox — le désactiver si besoin (`bcdedit /set hypervisorlaunchtype off`, puis redémarrer).

➡️ Étape suivante : [Déploiement de l'OVA Wazuh](02-deploiement-wazuh-ova.md)
