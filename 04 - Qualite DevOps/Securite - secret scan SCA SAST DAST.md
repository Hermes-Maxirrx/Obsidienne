# Sécurité — secret scan, SCA, SAST, DAST

**Définitions et pourquoi.** Le *secret scan* cherche des identifiants exposés dans le contenu et l'historique ; la **SCA** identifie des vulnérabilités connues des composants ; le **SAST** examine les chemins risqués du code sans exécuter l'application ; le **DAST** examine ses réponses en fonctionnement. Ces contrôles couvrent des périmètres différents et aucun « zéro alerte » ne garantit l'absence de risque.

**Exemples réels.** `.github/workflows/ci-cd.yml` prévoit Gitleaks CLI sur l'historique (`fetch-depth: 0`, `--redact`) et non l'action sous conditions de licence organisation ; `scripts/secret-fixture.py` teste le moteur hors Git avec un marqueur synthétique. Trivy `rootfs build/libs` inspecte le jar : l'essai `fs` avec zéro paquet avait été rejeté, puis un vrai avant/après Tomcat/Jackson est documenté dans `docs/preuves/scans.json` ; `trivy image` inspecte aussi l'OS/JRE. Le job `codeql` et `scripts/check_sarif.py` décrivent une analyse SAST **conditionnelle** : le parseur testé localement n'est pas un scan CodeQL. ZAP baseline sur loopback a produit **code 2 / WARN et trois types d'alertes** (en-tête nosniff, cacheabilité, Cross-Origin-Resource-Policy) ; `fail_action: true` reste strict dans le workflow.

**Piège.** Un rapport vide n'est pas une SCA propre ; un WARN n'est pas PASS. Ne pas ignorer une CVE/alerte, ajouter un secret de démonstration à Git, ou déclarer CodeQL exécuté à partir du YAML. Le complément de `VERIFICATION.md` rapporte un échec DAST sur PR sans journal distant accessible : ne pas attribuer sa cause à une alerte locale précise.

**Questions**
1. Pourquoi `trivy rootfs build/libs` était-il une preuve plus utile que l'essai `fs` sans paquets ?
2. Quelle distinction entre l'alerte ZAP locale et une conclusion sur la sécurité globale de l'application ?

À relier : [[CI - jobs artefacts et gates]], [[GitHub - protections et prerequis prives]], [[CI CD DevSecOps]]. Sources locales : `docs/05-securite.md`, `docs/preuves/scans.json`, `VERIFICATION.md`, `.github/workflows/ci-cd.yml`, `SOURCES.md`.
