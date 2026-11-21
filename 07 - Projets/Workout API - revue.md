# Workout API — rapport détaillé après refonte architecture et dépendances

> [!info] Périmètre vérifié
> Dépôt public [`Maxirrx/workout-api`](https://github.com/Maxirrx/workout-api), branche `main`, commit **`46eb14568e640a86e72dc769e4a027479762f896`** examiné le **21 novembre 2026**. Ce rapport remplace l'ancienne revue : le projet a désormais un schéma PostgreSQL, Docker Compose, des tests supplémentaires, `strict: true` et une tentative d'injection de dépendances avec Tsyringe.

## Verdict

**La direction choisie ressemble à une architecture que j'aime : domaine isolé, ports, adaptateurs PostgreSQL, container de dépendances, base jetable et tests.** C'est beaucoup plus structuré que le commit précédent.

Mais le câblage des dépendances n'est pas encore fonctionnel. Aujourd'hui, l'ajout de `tsyringe` est surtout déclaratif : le point d'entrée ne charge pas la configuration, le fichier d'enregistrement n'est pas compilé, le service applicatif crée une règle métier sans lui fournir son port, et les appels asynchrones du repository sont traités comme des booléens synchrones. Cela bloque réellement le cas d'usage de création d'utilisateur, malgré une suite de tests verte.

```mermaid
flowchart LR
  H[HTTP / contrôleur] --> A[Service applicatif]
  A --> D[Règle métier]
  D --> P[Port de lecture utilisateur]
  P --> R[Repository PostgreSQL]
  R --> DB[(PostgreSQL)]

  classDef ok fill:#173b2a,stroke:#52c878,color:#fff
  classDef gap fill:#4b2424,stroke:#f08080,color:#fff
  class D,P,R,DB ok
  class H,A gap
```

Le schéma ci-dessus est la cible cohérente. Dans le commit actuel, `H` est vide, le container n'est pas appelé, et le flux `A → D → P` est incohérent.

## Ce qui s'est amélioré depuis l'ancienne revue

### 1. La structure relationnelle est maintenant concrète

Le projet contient une [migration PostgreSQL](https://github.com/Maxirrx/workout-api/blob/46eb14568e640a86e72dc769e4a027479762f896/src/Data/Sql/Migration/migration_1.sql) avec huit tables : `users`, `muscles`, `exercises`, `workout_templates`, `workout_template_exercises`, `favorites`, `workout_sessions` et `performances`.

C'est un bon découpage :

- les trois muscles facultatifs d'un exercice sont protégés par trois contraintes `CHECK` ;
- `workout_template_exercises` modélise correctement le **plusieurs-à-plusieurs ordonné** entre programme et exercice ;
- `favorites` empêche un même favori en double grâce à sa clé primaire composée ;
- les performances sont rattachées à une séance et à un exercice ;
- les clés étrangères importantes sont indexées.

Le fichier [`docker-compose.yml`](https://github.com/Maxirrx/workout-api/blob/46eb14568e640a86e72dc769e4a027479762f896/docker-compose.yml) raccorde PostgreSQL 17, la migration et les fixtures. Sa configuration a été validée localement avec `docker compose config --quiet`.

### 2. Une partie des anciens défauts métier a été corrigée

Dans [`Exercice.ts`](https://github.com/Maxirrx/workout-api/blob/46eb14568e640a86e72dc769e4a027479762f896/src/Domain/Entity/Exercice.ts#L27-L30), la comparaison des muscles optionnels est désormais protégée contre `undefined`. Le test « un seul muscle » existe aussi dans [`exercice.test.ts`](https://github.com/Maxirrx/workout-api/blob/46eb14568e640a86e72dc769e4a027479762f896/src/test/domainTest/entity/exercice.test.ts#L18-L20). C'est une correction concrète et pertinente.

### 3. Les tests de règles métier progressent

La copie examinée exécute **7 suites et 45 tests** avec succès. La couverture affichée localement est **99,04 % des statements** et **100 % des branches** sur les fichiers effectivement importés par les tests.

Le nouveau test de [`DUserService`](https://github.com/Maxirrx/workout-api/blob/46eb14568e640a86e72dc769e4a027479762f896/src/test/domainTest/service/user.test.ts) montre la bonne idée : une règle métier peut être testée contre un faux port, sans PostgreSQL. C'est exactement le bénéfice recherché par une dépendance inversée.

### 4. `strict` est activé

[`tsconfig.json`](https://github.com/Maxirrx/workout-api/blob/46eb14568e640a86e72dc769e4a027479762f896/tsconfig.json#L2-L19) active enfin `strict: true`, ainsi que les décorateurs nécessaires à Tsyringe. C'est la bonne intention : faire détecter les incohérences de contrat avant l'exécution.

---

# Analyse prioritaire — gestion des dépendances

## P0 — Le service utilisateur traite des `Promise` comme des booléens : toute adresse et tout nom paraissent déjà pris

Dans [`UserService.ts:15-29`](https://github.com/Maxirrx/workout-api/blob/46eb14568e640a86e72dc769e4a027479762f896/src/Infra/Service/UserService.ts#L15-L29), les méthodes `isExistEmail` et `isExistName` appellent des méthodes de repository asynchrones :

```ts
const user = this.IUserRepository.findByEmail(email)
if (user === undefined) return false
return true
```

`findByEmail` renvoie un `Promise<User | undefined>` ([`UserRepository.ts:13`](https://github.com/Maxirrx/workout-api/blob/46eb14568e640a86e72dc769e4a027479762f896/src/Infra/Repository/UserRepository.ts#L13-L29)). Une promesse elle-même n'est jamais `undefined`. Par conséquent, sans `await`, la méthode renvoie toujours `true`.

**Reproduction locale :** avec un faux repository qui renvoie `Promise.resolve(undefined)`, `isExistEmail('new@example.test')` a imprimé `true`.

**Impact :** quand le chemin sera branché, toute inscription sera rejetée par « Il existe déjà un compte qui utilise cette adresse email ». C'est le défaut fonctionnel le plus important du commit.

### Correction

Le port doit porter l'asynchronisme réel :

```ts
export interface UserAvailabilityPort {
  existsByEmail(email: string): Promise<boolean>;
  existsByName(name: string): Promise<boolean>;
}
```

Puis l'adaptateur doit attendre le repository :

```ts
async existsByEmail(email: string): Promise<boolean> {
  return (await this.userRepository.findByEmail(email)) !== undefined;
}
```

La règle métier devient alors explicite :

```ts
if (await this.userAvailability.existsByEmail(user.email)) {
  throw new Error("Cette adresse email est déjà utilisée");
}
```

Ce renommage est utile : un port appelé `IUserService` ne dit pas s'il est une règle métier, un use case ou une lecture de persistance. `UserAvailabilityPort` décrit ce dont le domaine a réellement besoin.

## P0 — La règle métier est construite sans sa dépendance obligatoire

[`DUserService`](https://github.com/Maxirrx/workout-api/blob/46eb14568e640a86e72dc769e4a027479762f896/src/Domain/Service/DUserService.ts#L4-L18) exige un `IUserService` dans son constructeur. Pourtant [`Infra/Service/UserService.ts:31-35`](https://github.com/Maxirrx/workout-api/blob/46eb14568e640a86e72dc769e4a027479762f896/src/Infra/Service/UserService.ts#L31-L35) fait `new DUserService()` sans argument.

Même en contournant temporairement le problème de compilation, l'appel à `create()` échoue à l'exécution avec :

```text
TypeError: Cannot read properties of undefined (reading 'isExistEmail')
```

### Correction

Ne pas instancier une dépendance cachée dans le service applicatif. L'injecter :

```ts
@injectable()
export class CreateUserService {
  constructor(
    @inject("UserRepository") private readonly users: UserRepositoryPort,
    private readonly registrationPolicy: UserRegistrationPolicy,
  ) {}
}
```

Puis la **composition root** crée ou résout l'objet entier. On choisit une seule stratégie :

- soit injection explicite par constructeur, simple et très lisible ;
- soit Tsyringe partout, avec tokens et décorateurs cohérents.

Mélanger `new DUserService()` et un container donne l'impression d'une injection sans obtenir ses bénéfices.

## P0 — Tsyringe est installé mais pas raccordé au programme

Le package ajoute `reflect-metadata` et `tsyringe` ([`package.json:33-38`](https://github.com/Maxirrx/workout-api/blob/46eb14568e640a86e72dc769e4a027479762f896/package.json#L33-L38)). Le registre existe dans [`src/Infra/Config/TSyringe`](https://github.com/Maxirrx/workout-api/blob/46eb14568e640a86e72dc769e4a027479762f896/src/Infra/Config/TSyringe#L1-L14), mais trois problèmes empêchent son usage réel :

1. son nom ne finit pas par `.ts` ; le glob TypeScript `src/**/*` ne le compile donc pas ;
2. [`src/index.ts`](https://github.com/Maxirrx/workout-api/blob/46eb14568e640a86e72dc769e4a027479762f896/src/index.ts#L1-L15) ne l'importe pas ;
3. le point d'entrée fait `new userRepository()` directement, sans `container.resolve(...)`.

Une tentative d'importer `UserService` sans charger d'abord `reflect-metadata` échoue actuellement avec l'erreur de Tsyringe demandant d'ajouter le polyfill en tête du programme.

### Correction

Créer un vrai fichier `src/Infra/Config/container.ts` :

```ts
import "reflect-metadata";
import { container } from "tsyringe";

container.register<UserRepositoryPort>("UserRepository", {
  useClass: PostgresUserRepository,
});
container.registerSingleton<UserAvailabilityPort>("UserAvailability", UserAvailabilityService);
container.registerSingleton(CreateUserService);
```

Et dans le point d'entrée :

```ts
import "reflect-metadata";
import "./Infra/Config/container";
import { container } from "tsyringe";

const createUser = container.resolve(CreateUserService);
```

Le nom exact des classes importe moins que le principe : **un seul endroit concret construit le graphe de dépendances**. Le domaine ne doit pas connaître `pg`, Express ni le container.

## P1 — Les couches sont bien nommées, mais la direction des dépendances reste ambiguë

L'intention est visible : `Domain`, `Infra`, `Repository`, `Service`, `Controller`, `Route`. C'est une bonne base. Mais la séparation est incomplète :

- la règle `DUserService` dépend d'un port nommé `IUserService` ([`IUserService.ts`](https://github.com/Maxirrx/workout-api/blob/46eb14568e640a86e72dc769e4a027479762f896/src/Domain/Port/Out/Service/IUserService.ts#L1-L4)) alors qu'elle a besoin de savoir si un utilisateur existe ;
- le contrat `IUserRepository` est défini dans `Infra/Repository` ([`UserRepository.ts:4-10`](https://github.com/Maxirrx/workout-api/blob/46eb14568e640a86e72dc769e4a027479762f896/src/Infra/Repository/UserRepository.ts#L4-L10)), donc il ne peut pas devenir un port du domaine ;
- l'implémentation PostgreSQL, l'interface et la requête SQL sont réunies dans un seul fichier.

### Solution de découpage

```text
Domain/
  Entity/User.ts
  Port/Out/UserRepositoryPort.ts
  Service/UserRegistrationPolicy.ts

Application/
  CreateUser.ts

Infra/
  Repository/PostgresUserRepository.ts
  Config/container.ts
  Http/...
```

- **Domaine** : règles pures et contrats qu'il exige.
- **Application** : orchestration du cas d'usage, transaction, autorisation future.
- **Infrastructure** : SQL, `Pool`, HTTP, Docker et implémentations des ports.
- **Composition root** : seul endroit autorisé à choisir PostgreSQL plutôt qu'une mémoire de test.

Cette structure reste proche de ton style actuel ; elle clarifie seulement les responsabilités au lieu d'ajouter une abstraction par principe.

## P1 — Le point d'entrée n'est pas encore une API Express

Express est une dépendance de production, mais aucun fichier source ne l'importe à ce commit. Les 14 fichiers de routes et contrôleurs sont vides. [`index.ts`](https://github.com/Maxirrx/workout-api/blob/46eb14568e640a86e72dc769e4a027479762f896/src/index.ts#L1-L15) lance une requête `findAll()`, affiche les utilisateurs dans la console puis ferme le pool.

**Ce n'est pas un problème si c'est une étape de développement**, mais il faut le dire clairement : le dépôt est actuellement un prototype de domaine/persistance, pas encore une API HTTP accessible.

### Solution minimale

Ne pas remplir toutes les routes d'un coup. Faire une tranche verticale :

```text
POST /users
  → validation HTTP
  → CreateUser
  → UserRegistrationPolicy
  → PostgresUserRepository
  → PostgreSQL
```

Ajouter ensuite un test d'intégration contre une base jetable. Une route complète prouve le câblage ; sept fichiers vides ne le prouvent pas.

---

# Persistance, SQL et cohérence métier

## P0 — Les mots de passe sont persistés, relus et affichés en clair

[`User`](https://github.com/Maxirrx/workout-api/blob/46eb14568e640a86e72dc769e4a027479762f896/src/Domain/Entity/User.ts#L1-L13) porte directement `password`. Le repository l'insère tel quel ([`UserRepository.ts:88-101`](https://github.com/Maxirrx/workout-api/blob/46eb14568e640a86e72dc769e4a027479762f896/src/Infra/Repository/UserRepository.ts#L88-L101)), le relit avec `SELECT *` ([`UserRepository.ts:67-80`](https://github.com/Maxirrx/workout-api/blob/46eb14568e640a86e72dc769e4a027479762f896/src/Infra/Repository/UserRepository.ts#L67-L80)) et le point d'entrée fait `console.log(users)` ([`index.ts:8-9`](https://github.com/Maxirrx/workout-api/blob/46eb14568e640a86e72dc769e4a027479762f896/src/index.ts#L4-L12)).

**Impact :** une fuite de base, de logs ou une future réponse HTTP qui réutiliserait ce modèle révélerait des identifiants utilisables. Les données des fixtures sont fictives, mais le chemin de persistance de production n'impose pas de hash.

### Solution

1. Renommer la colonne en `password_hash` dans une migration d'évolution ; ne pas réécrire l'historique d'une base déjà partagée.
2. Introduire un port `PasswordHasher` implémenté par Argon2id ou bcrypt ; l'inscription ne persiste que le hash.
3. Ne sélectionner le hash que pour l'authentification, jamais avec `SELECT *` dans les lectures ordinaires.
4. Retourner des DTO publics sans secret et supprimer le `console.log(users)`.
5. Si des mots de passe réels ont déjà été insérés, les invalider et organiser leur réinitialisation : les hasher après coup ne transforme pas un secret exposé en secret sûr.

## P0 — Les recherches PostgreSQL utilisateur fournissent leurs paramètres dans le mauvais format

[`node-postgres`](https://node-postgres.com/) attend un tableau de valeurs pour une requête paramétrée. Dans [`UserRepository.ts:15-17`](https://github.com/Maxirrx/workout-api/blob/46eb14568e640a86e72dc769e4a027479762f896/src/Infra/Repository/UserRepository.ts#L13-L29), [`:33-35`](https://github.com/Maxirrx/workout-api/blob/46eb14568e640a86e72dc769e4a027479762f896/src/Infra/Repository/UserRepository.ts#L31-L46) et [`:51-53`](https://github.com/Maxirrx/workout-api/blob/46eb14568e640a86e72dc769e4a027479762f896/src/Infra/Repository/UserRepository.ts#L49-L64), le code passe directement `email`, `name` ou l'identifiant au lieu de `[email]`, `[name]` ou `[id]`.

**Impact :** les trois méthodes échouent avant toute requête utile à PostgreSQL. Les placeholders `$1` sont une bonne protection contre l'injection SQL, mais l'appel doit respecter le contrat de la bibliothèque.

### Solution

```ts
const result = await pool.query<UserRow>(
  "SELECT id, name, email, email_verified_at FROM users WHERE email = $1",
  [email],
);
```

Ajouter les tests d'intégration « trouvé », « absent » et « erreur SQL » sur une vraie base jetable ; les tests unitaires de règles ne peuvent pas détecter ce contrat de bibliothèque.

## P1 — La règle d'unicité du nom n'est pas garantie par la base

[`DUserService.ts:15-17`](https://github.com/Maxirrx/workout-api/blob/46eb14568e640a86e72dc769e4a027479762f896/src/Domain/Service/DUserService.ts#L15-L17) promet de refuser un nom déjà utilisé. Mais la migration ne crée une contrainte unique que sur l'e-mail en minuscules ([`migration_1.sql:11`](https://github.com/Maxirrx/workout-api/blob/46eb14568e640a86e72dc769e4a027479762f896/src/Data/Sql/Migration/migration_1.sql#L3-L16)). Deux créations concurrentes peuvent donc insérer le même nom.

Le même problème existe pour l'e-mail : la vérification applicative utilise `WHERE email = $1`, sensible à la casse, tandis que la base interdit `lower(email)` en double. Le pré-contrôle peut dire « disponible », puis l'insert échoue avec une erreur SQL générique.

### Solution

- décider si le nom doit réellement être unique ; si oui, ajouter une contrainte unique normalisée ;
- normaliser l'e-mail au même endroit à chaque fois (`trim().toLowerCase()` ou type `citext`) ;
- traiter la contrainte de base comme l'autorité finale ;
- convertir seulement la violation d'unicité attendue en erreur métier lisible.

Le `SELECT` avant `INSERT` améliore le message, mais **ne remplace jamais** une contrainte unique contre une course concurrente.

## P1 — Le modèle `User` et le repository perdent l'identité persistée

La migration a un `users.id`, et l'insert le retourne ([`UserRepository.ts:88-101`](https://github.com/Maxirrx/workout-api/blob/46eb14568e640a86e72dc769e4a027479762f896/src/Infra/Repository/UserRepository.ts#L88-L101)). Pourtant [`User`](https://github.com/Maxirrx/workout-api/blob/46eb14568e640a86e72dc769e4a027479762f896/src/Domain/Entity/User.ts#L1-L13) ne contient pas d'identifiant et le repository ne le conserve pas.

Conséquences :

- `findById` accepte une variable nommée `name` de type `string` ([`UserRepository.ts:49-64`](https://github.com/Maxirrx/workout-api/blob/46eb14568e640a86e72dc769e4a027479762f896/src/Infra/Repository/UserRepository.ts#L49-L64)) ;
- l'appelant ne peut pas connaître l'identifiant du nouvel utilisateur ;
- il sera difficile de relier de façon sûre l'utilisateur aux exercices, programmes et séances.

### Solution

Définir une identité dans le modèle persistant, par exemple `id?: string` ou `id?: bigint` selon le contrat API choisi. Avec PostgreSQL, éviter de transformer silencieusement un `BIGINT` en JavaScript `number` au-delà de `Number.MAX_SAFE_INTEGER` : `string` est souvent plus robuste aux frontières HTTP/SQL.

## P1 — Le schéma est riche, mais les relations ne sont pas encore portées par les entités ni repositories

La migration décrit correctement les relations programme–exercice, favoris, séance–performance. Mais :

- [`Favorite.ts`](https://github.com/Maxirrx/workout-api/blob/46eb14568e640a86e72dc769e4a027479762f896/src/Domain/Entity/Favorite.ts#L1-L2) est vide ;
- les repositories de ces entités sont vides ;
- `Performance` référence seulement un `Exercice`, sans séance ;
- `WorkoutSession` référence des objets `User` et `WorkoutTemplate`, sans identité persistée ni lien aux performances.

Cela n'est pas un défaut de la migration : elle anticipe les besoins. Mais il faut éviter que SQL et domaine évoluent comme deux modèles indépendants.

Une incohérence plus précise est déjà possible en SQL : `workout_sessions` référence séparément `user_id` et `workout_template_id`, sans garantir que le programme appartient à cet utilisateur ; `performances` référence séparément séance et exercice, sans garantir que l'exercice appartient au programme de la séance ([`migration_1.sql:45-50`](https://github.com/Maxirrx/workout-api/blob/46eb14568e640a86e72dc769e4a027479762f896/src/Data/Sql/Migration/migration_1.sql#L45-L50), [`:64-82`](https://github.com/Maxirrx/workout-api/blob/46eb14568e640a86e72dc769e4a027479762f896/src/Data/Sql/Migration/migration_1.sql#L64-L82)). Les entités ne portent pas non plus ces contrôles.

### Solution

Choisir le prochain cas d'usage réel, par exemple « démarrer une séance depuis un programme », puis faire apparaître les relations nécessaires dans le domaine, le port, l'adaptateur et le test d'intégration. Ne pas implémenter les huit repositories avant qu'un use case les nécessite.

Dans ce use case, vérifier propriétaire du programme et appartenance de l'exercice avant insertion, dans une transaction. Si ces relations deviennent des invariants incontournables, les renforcer aussi en SQL avec des clés candidates/composites ou un trigger contrôlé et testé.

## P2 — La stratégie de migration est adaptée à une première base, pas aux évolutions

Docker Compose monte `migration_1.sql` et `fixtures.sql` dans `/docker-entrypoint-initdb.d/` ([`docker-compose.yml:15-18`](https://github.com/Maxirrx/workout-api/blob/46eb14568e640a86e72dc769e4a027479762f896/docker-compose.yml#L15-L18)). PostgreSQL n'exécute ces scripts qu'à l'initialisation d'un volume vide.

**Conséquence :** modifier `migration_1.sql` ne mettra pas à jour une base qui possède déjà `postgres_data`. C'est une source classique de « chez moi ça marche ».

### Solution

À court terme : documenter qu'une remise à zéro du volume est nécessaire en développement.

À moyen terme : adopter un vrai outil de migrations avec historique (`node-pg-migrate`, `dbmate`, Knex, Prisma Migrate ou SQL versionné exécuté par un runner), une migration immuable par changement et un test sur une base vide. Le choix importe moins que la garantie : une base existante doit pouvoir avancer de version sans suppression manuelle.

---

# Qualité, tests et dépendances npm

## P0 — La build TypeScript échoue alors que Jest est vert

La commande locale `npm test -- --runInBand --ci` réussit : **45/45 tests**. En revanche, `npm run build` échoue immédiatement :

```text
TS5108: Option 'moduleResolution=node10' has been removed.
```

La cause est [`tsconfig.json:4`](https://github.com/Maxirrx/workout-api/blob/46eb14568e640a86e72dc769e4a027479762f896/tsconfig.json#L2-L19), qui utilise `moduleResolution: "node"`, avec TypeScript `^7.0.2` ([`package.json:31`](https://github.com/Maxirrx/workout-api/blob/46eb14568e640a86e72dc769e4a027479762f896/package.json#L22-L38)).

Un contrôle strict effectué en contournant cette option obsolète trouve en plus :

- `UserRepository.ts:74` : paramètre `row` implicitement `any` ;
- `UserService.ts:32` : constructeur `DUserService` appelé sans son argument obligatoire.

Jest passe parce que SWC transforme les tests sans exécuter le contrôle TypeScript complet.

### Solution

1. remplacer `moduleResolution: "node"` par une paire cohérente avec le format choisi, par exemple `module: "Node16"` et `moduleResolution: "Node16"` si le projet reste en CommonJS/Node ;
2. faire passer `npm run build` localement ;
3. ajouter la build dans la CI **avant** de considérer les tests comme verts ;
4. conserver `strict: true` : il révèle précisément les erreurs qui comptent ici.

## P1 — La CI ne vérifie que Jest et installe avec `npm install`

Le workflow [`.github/workflows/jest.yml`](https://github.com/Maxirrx/workout-api/blob/46eb14568e640a86e72dc769e4a027479762f896/.github/workflows/jest.yml#L1-L22) exécute `npm install` puis Jest avec couverture. Il ne lance pas `npm run build`.

### Solution

```yaml
- run: npm ci
- run: npm run build
- run: npm test -- --ci --coverage
- run: npm audit --omit=dev --audit-level=high
```

`npm ci` utilise le lockfile exactement, ce qui rend la CI reproductible. Il faut ensuite décider si l'audit bloque la CI immédiatement ou commence en avertissement, mais le résultat doit être visible.

## P1 — La couverture très haute ne couvre pas encore l'architecture réelle

Les 45 tests et la couverture à 99 % sont un bon signal sur les règles et les fichiers importés. Mais ils ne prouvent pas :

- que le container Tsyringe est chargé ;
- qu'un repository PostgreSQL fonctionne ;
- que la migration et les fixtures sont exécutables ensemble ;
- qu'une création d'utilisateur respecte l'unicité de la base ;
- qu'une route HTTP valide, crée puis relit une ressource.

Les contrôleurs, routes et la plupart des repositories restent vides. Des fichiers sans statements ne font pas baisser le taux de couverture ; un grand pourcentage n'est donc pas un pourcentage de maturité de l'API.

Certains tests positifs donnent aussi un faux sentiment de preuve : par exemple [`user.test.ts:17`](https://github.com/Maxirrx/workout-api/blob/46eb14568e640a86e72dc769e4a027479762f896/src/test/domainTest/service/user.test.ts#L9-L18) fait `await expect(service.canCreateUser(user))` sans matcher, et les assertions asynchrones de validation de mot de passe passent une fonction à `.rejects` au lieu de la promesse retournée ([`user.test.ts:61-105`](https://github.com/Maxirrx/workout-api/blob/46eb14568e640a86e72dc769e4a027479762f896/src/test/domainTest/service/user.test.ts#L53-L105)). Les cas positifs doivent attendre la promesse, puis vérifier explicitement le résultat ou l'absence d'erreur.

### Tests à ajouter dans cet ordre

1. **Unitaires** : `UserRegistrationPolicy` avec un faux port asynchrone ; cas e-mail et nom disponibles/occupés, et erreur du port.
2. **Repository PostgreSQL** : `findByEmail`, `create`, retour d'ID, liste vide `[]`, conflit e-mail et conflit nom.
3. **Migration** : démarrer PostgreSQL jetable, appliquer migration + fixtures, vérifier 8 tables, clés étrangères et contraintes.
4. **Intégration use case** : instancier le vrai container, créer un utilisateur, le relire, vérifier le hash et le conflit.
5. **HTTP** : seulement après une route réelle, vérifier code de statut, corps de réponse et absence de mot de passe dans la réponse.

## P1 — L'audit des dépendances de production relève 7 vulnérabilités

`npm audit --omit=dev` exécuté le 21 novembre 2026 rapporte **7 vulnérabilités dans les dépendances de production : 4 hautes et 3 faibles**. Elles viennent de la chaîne d'Express `4.16.4` : `express`, `body-parser`, `path-to-regexp`, `qs`, `cookie`, `send` et `serve-static`. L'audit signale qu'un correctif est disponible.

La dépendance directe à mettre à jour est [`express`](https://github.com/Maxirrx/workout-api/blob/46eb14568e640a86e72dc769e4a027479762f896/package.json#L33-L38). Elle n'est pas encore importée par le code source, donc la mise à niveau est probablement plus simple maintenant qu'après la création de routes.

### Solution

- ouvrir une branche dédiée « dependency maintenance » ;
- mettre Express à une version corrigée compatible ;
- régénérer le lockfile avec une installation propre ;
- relancer build, tests et audit ;
- ne pas utiliser `npm audit fix --force` sans lire le diff de dépendances et retester.

L'installation locale a aussi affiché des avertissements de dépendances de développement obsolètes (`inflight`, plusieurs `glob`). Ce n'est pas un exploit démontré contre ton code, mais c'est une raison supplémentaire de maintenir régulièrement Jest/SWC et leur arbre transitif.

## P2 — Quelques dépendances/outils sont installés sans contrat actif

- `tslint` est dans les dépendances de développement, mais aucun script `lint` ne l'exécute ([`package.json:6-10`](https://github.com/Maxirrx/workout-api/blob/46eb14568e640a86e72dc769e4a027479762f896/package.json#L6-L10)).
- `express`, `reflect-metadata` et `tsyringe` sont déclarés, mais le chemin d'exécution ne les utilise pas encore.
- `package.json` annonce `main: "index.js"`, alors que TypeScript vise `build/` et qu'il n'y a pas de script `start`.

### Solution

Garder uniquement les dépendances qui ont un rôle exécuté, ou rendre leur rôle explicite dans les scripts : `dev`, `build`, `start`, `test`, `lint`, `migration:up`, `migration:down` quand les fonctions existent réellement. Un manifeste court est plus facile à maintenir et à sécuriser.

Dans cet état, remplacer TSLint par ESLint avec le parser TypeScript est plus cohérent que d'ajouter un script à un outil ancien : l'exécution de TSLint avec le TypeScript verrouillé ne fonctionne pas dans l'environnement examiné. Quel que soit l'outil retenu, la règle est la même : une dépendance de qualité n'a de valeur que si `npm run lint` et la CI l'exécutent.

---

# Autres incohérences à traiter

## P2 — Les validations d'entités restent contournables ou incomplètes

- les propriétés des entités sont publiques et mutables : une séance peut être construite valide puis son état modifié sans revalidation ;
- [`WorkoutSession.ts`](https://github.com/Maxirrx/workout-api/blob/46eb14568e640a86e72dc769e4a027479762f896/src/Domain/Entity/WorkoutSession.ts#L23-L27) ne rejette pas explicitement `Invalid Date` ;
- [`Performance.ts`](https://github.com/Maxirrx/workout-api/blob/46eb14568e640a86e72dc769e4a027479762f896/src/Domain/Entity/Performance.ts#L23-L31) vérifie les durées fractionnaires mais pas les valeurs négatives, le poids ou les répétitions ;
- les factories de test sont exportées depuis les entités de production et `makeMuscleFaker` donne toujours le nom `Biceps`, même pour des identifiants différents ([`Muscle.ts:13-15`](https://github.com/Maxirrx/workout-api/blob/46eb14568e640a86e72dc769e4a027479762f896/src/Domain/Entity/Muscle.ts#L13-L15)).

### Solution

Utiliser des champs privés et des méthodes métier (`finish()`, `reschedule()`, `changeWeight()`), chacune revalidant les invariants. Déplacer les factories dans `src/test/factories/`. Ajouter les tests de dates invalides, de nombres négatifs et de mutation après création.

## P3 — Le repository mélange détails techniques et contrat métier

[`UserRepository.ts`](https://github.com/Maxirrx/workout-api/blob/46eb14568e640a86e72dc769e4a027479762f896/src/Infra/Repository/UserRepository.ts) contient en même temps l'interface, l'implémentation PostgreSQL, les requêtes, le mapping et la conversion des erreurs.

Des choix à revoir :

- `SELECT *` charge aussi le mot de passe même lorsqu'il n'est pas nécessaire ;
- `findAll()` renvoie `undefined` pour une liste vide au lieu de `[]` ;
- les erreurs sont parfois masquées par un message générique et parfois concaténées ;
- le nom de classe `userRepository` commence en minuscule, contrairement aux conventions TypeScript usuelles.

### Solution

Définir un port séparé et un `PostgresUserRepository`. Retourner toujours `User[]` pour une collection. Sélectionner les colonnes nécessaires explicitement. Créer un type de ligne SQL et une fonction `toDomain(row)` testable.

---

# Plan d'amélioration recommandé

## Étape 1 — Faire marcher la colonne vertébrale avant d'ajouter des endpoints

1. Corriger `moduleResolution` et obtenir une build verte.
2. Renommer le fichier Tsyringe en `container.ts`.
3. Réparer les contrats asynchrones `Promise<boolean>`.
4. Injecter la règle `DUserService` au lieu de faire `new DUserService()` sans argument.
5. Charger `reflect-metadata` et le container depuis un vrai point d'entrée.
6. Ajouter un test qui instancie le container et exécute le flux de création avec un fake repository.

**Critère de sortie :** build TypeScript verte et test qui prouve qu'un e-mail absent retourne bien « disponible ».

## Étape 2 — Rendre la persistance fiable

1. Conserver l'identifiant SQL dans les objets persistés.
2. Aligner les contraintes de base et les règles métier sur e-mail et nom.
3. Renommer `password` en `password_hash` et introduire un port de hash.
4. Tester le repository avec PostgreSQL jetable.
5. Mettre en place un mécanisme de migrations versionnées pour les prochaines évolutions.

**Critère de sortie :** création et relecture d'un utilisateur dans PostgreSQL, avec gestion déterministe du conflit d'unicité.

## Étape 3 — Ajouter une seule tranche HTTP complète

1. `POST /users` ;
2. validation du JSON entrant ;
3. use case injecté ;
4. réponse `201` sans mot de passe ;
5. test HTTP avec base jetable ;
6. seulement ensuite, routes programmes/exercices/séances.

**Critère de sortie :** une API réellement démarrable et testée de bout en bout, plutôt qu'un ensemble de routes nommées mais vides.

## Étape 4 — Assainir les dépendances et la CI

1. Mettre Express à jour et résoudre les 7 alertes de production.
2. Remplacer `npm install` par `npm ci` en CI.
3. Ajouter `npm run build` dans le workflow.
4. Ajouter un script de lint réellement exécuté ou retirer TSLint tant qu'il n'est pas utilisé.
5. Ajouter l'audit de dépendances au pipeline avec une politique claire.

---

## Conclusion

Le projet est passé d'une structure surtout théorique à un **vrai début de stack** : règles métier, tests, PostgreSQL, fixtures, Docker et premier essai de ports/adaptateurs. C'est une évolution nette et le sens de l'architecture est bon.

La priorité n'est pas d'ajouter toutes les entités restantes. Il faut d'abord rendre le **graphe de dépendances exécutable et typé** : un seul point de composition, des ports asynchrones cohérents, aucun `new` caché, build dans la CI et une tranche utilisateur complète. Une fois cela solide, le même patron se répliquera proprement pour exercices, programmes, séances et performances.

**Limites de cette revue :** analyse du commit indiqué, exécution locale de Jest, de la couverture, de la build et de l'audit npm ; validation de la configuration Docker Compose. Aucune base de données persistante du projet n'a été modifiée et aucun déploiement HTTP n'a été effectué.

**Pour réviser :** [[Architecture hexagonale - Domaine, ports et limites]] · [[Tests - pyramide et TDD]] · [[Contrats API - HTTP, idempotence et pagination]] · [[Sécurité applicative - identité autorisation et sessions]]
