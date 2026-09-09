# Comment publier ce dépôt sur GitHub

## 1. Créer le dépôt sur GitHub

1. Va sur https://github.com/new
2. Nom suggéré : `wazuh-atelier-virtualbox` (ou ce que tu préfères)
3. Visibilité : **Public**
4. Ne coche PAS "Initialize with README" (tu as déjà le tien)
5. Clique `Create repository`

## 2. Pousser le contenu local vers GitHub

Depuis ce dossier (`wazuh-workshop/`), exécute :

```bash
git init
git add .
git commit -m "Initial commit: atelier Wazuh sur VirtualBox"
git branch -M main
git remote add origin https://github.com/<TON-USER>/<TON-REPO>.git
git push -u origin main
```

Remplace `<TON-USER>` et `<TON-REPO>` par ton nom d'utilisateur GitHub et le nom du dépôt créé.

## 3. Ajouter tes captures d'écran au fur et à mesure

À chaque étape de l'atelier, dépose tes captures dans le bon sous-dossier de `screenshots/` (voir la liste dans chaque fichier `docs/0X-*.md`), puis :

```bash
git add screenshots/
git commit -m "Ajout captures d'écran : étape X"
git push
```

## 4. (Optionnel) Ajouter une licence

Sur GitHub : `Add file` → `Create new file` → nomme-le `LICENSE` → GitHub te proposera un template (MIT recommandé pour un partage libre).

## 5. (Optionnel) Activer GitHub Pages

Si tu veux que le README/les docs soient lisibles comme un mini-site web :
`Settings` → `Pages` → source = branche `main`, dossier `/ (root)` ou `/docs`.
