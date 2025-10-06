Déploiement et CI/CD — Guide rapide

Résumé
------
Ce dépôt contient deux variantes du site : `spotlight-js` (JavaScript) et `spotlight-ts` (TypeScript). Le site déployé est `spotlight-js` via Azure Static Web Apps.

Ressources
----------
- Azure Static Web App : jcmoyongo-spotlight-2025
- URL publique : https://purple-dune-07cb9c40f.1.azurestaticapps.net
- Repo GitHub : https://github.com/jcmoyongo/jcmoyongo

Étapes et commandes utiles
-------------------------
1) Créer / lier la Static Web App (si nécessaire)
```powershell
az staticwebapp create \
  --name jcmoyongo-spotlight-2025 \
  --resource-group rg-jcmoyongo-spotlight \
  --location eastus2 \
  --source https://github.com/jcmoyongo/jcmoyongo \
  --branch main \
  --app-location "spotlight-js" \
  --output-location ".next" \
  --sku Free \
  --login-with-github
```

2) Définir une variable d'application (app setting) requise au build/runtime
```powershell
az staticwebapp appsettings set \
  --name jcmoyongo-spotlight-2025 \
  --resource-group rg-jcmoyongo-spotlight \
  --setting-names NEXT_PUBLIC_SITE_URL="https://purple-dune-07cb9c40f.1.azurestaticapps.net"
```

3) Forcer un rebuild (commit vide)
```powershell
git commit --allow-empty -m "Trigger rebuild after config" && git push origin main
```

4) Vérifier le statut du workflow
- GitHub → Repo → Actions → sélectionnez le run récent et examinez les logs.

5) Récupérer l'URL publique via Azure CLI
```powershell
az staticwebapp show --name jcmoyongo-spotlight-2025 --resource-group rg-jcmoyongo-spotlight --query defaultHostname -o tsv
```

Notes de dépannage
------------------
- Erreurs fréquentes sur build:
  - Imports sensibles à la casse (Linux builder) : corriger les noms de fichiers/images.
  - Variables d'environnement manquantes : ajouter comme app setting ou dans le workflow `env`.
  - ESLint / React errors : corriger les apostrophes non échappées ou règle ESLint.

Sécurité
--------
- Si vous avez créé un Personal Access Token (PAT) temporaire pour effectuer des pushes, révoquez-le après utilisation.

Contact & suivi
---------------
- Pour toute erreur de build, copiez les logs de la job Actions et partagez-les ici pour diagnostic.
