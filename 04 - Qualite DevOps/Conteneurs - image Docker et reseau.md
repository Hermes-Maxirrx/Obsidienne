# Conteneurs — image Docker et réseau

**Définition.** Une image assemble un runtime et un livrable ; un conteneur est une instance de cette image. `EXPOSE` documente un port interne, alors que `--publish` crée une liaison hôte. **Pourquoi :** tester le jar livré sans reconstruire un autre artefact ni exposer involontairement le service.

**Exemple réel.** `Dockerfile` part de `eclipse-temurin:25-jre`, copie **`build/libs/app.jar`** (préservé par `.dockerignore`), exécute Java sous UID `10001`, documente `EXPOSE 8080` et fixe `SERVER_ADDRESS=0.0.0.0` **dans le conteneur**. `scripts/docker-smoke.py` choisit un port hôte aléatoire lié à `127.0.0.1`, contrôle la réponse `/hello` puis supprime son propre conteneur. Le job `docker` du workflow emploie aussi un bind loopback sur le runner ; Trivy inspecte ensuite l'image. Le tag SHA du commit suit un run, mais sans registre/digest immuable aucune promotion entre runs n'est prouvée.

**Piège.** `EXPOSE 8080` n'ouvre pas un port public. `0.0.0.0` interne n'autorise pas un bind hôte public ; ne pas remplacer un smoke isolé par `docker compose down`, un prune global, ou une publication sur 80. Un tag de base mutable ne garantit pas le même digest demain.

**Questions**
1. Pourquoi `.dockerignore` réinclut-il précisément `build/libs/app.jar` ?
2. Où se situe la différence entre `SERVER_ADDRESS=0.0.0.0` et la publication hôte `127.0.0.1` ?

À relier : [[CI - jobs artefacts et gates]], [[Ansible - inventaires et simulation]], [[CI CD DevSecOps]]. Sources locales : `Dockerfile`, `.dockerignore`, `scripts/docker-smoke.py`, `docs/04-docker-ansible.md`, `VERIFICATION.md`.
