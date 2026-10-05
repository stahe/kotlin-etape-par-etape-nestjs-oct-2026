# Introduction étape par étape à la programmation mobile avec Kotlin

📖 **Lire le tutoriel : [https://stahe.github.io/kotlin-etape-par-etape-nestjs-oct-2026/](https://stahe.github.io/kotlin-etape-par-etape-nestjs-oct-2026/)**

Ce cours vous apprend à écrire une application **mobile Android native** avec le langage [Kotlin](https://kotlinlang.org) 2.2 et [Jetpack Compose](https://developer.android.com/compose), la bibliothèque d'interface déclarative d'Android : une application dont les écrans sont fabriqués **sur le téléphone** à partir des données JSON d'un serveur.

Il suit pas à pas la progression du cours [Introduction étape par étape au framework mobile Flutter](https://stahe.github.io/flutter-etape-par-etape-sept-2026/) (lui-même calqué sur les cours [React](https://stahe.github.io/react-etape-par-etape-sept-2026/), [Vue.js](https://stahe.github.io/vuejs-etape-par-etape-sept-2026/) et [Angular](https://stahe.github.io/angular-etape-par-etape-sept-2026/)) : même plan, mêmes 25 exemples, même serveur, même étude de cas, écrits à la manière d'Android.

| Cours Flutter | Cours Kotlin |
|---|---|
| Dart, des widgets, une méthode `build()` | Kotlin, des fonctions `@Composable` |
| `setState`, `StatefulWidget` | `remember`, `rememberSaveable`, `mutableStateOf` |
| le moteur de Flutter dessine chaque pixel | Compose, la boîte à outils d'Android lui-même (Material 3) |
| `initState`, `dispose` | les effets : `LaunchedEffect`, `DisposableEffect` |
| `InheritedWidget`, provider | `CompositionLocal`, `ViewModel`, `StateFlow` |
| go_router | Navigation Compose (routes `@Serializable`, liens profonds) |
| `Future`, `Stream` | coroutines, `Flow` |
| des dictionnaires JSON, `intl` | `strings.xml`, la langue de l'application (`setApplicationLocales`) — et les mêmes dictionnaires JSON |
| le paquet `http` | OkHttp, et son `CookieJar` pour le cookie du jeton |
| `shared_preferences` | DataStore, `SharedPreferences` |
| `flutter_test` | les tests instrumentés de Compose (sur l'émulateur) |

Le serveur, lui, ne change pas : c'est le serveur JSON de l'application **RdvMedecins** déjà utilisé par les clients React, Vue.js, Angular et Flutter.

## L'approche : de nombreux petits exemples, puis une étude de cas

Le cours s'articule autour de **25 petits exemples**, chacun centré sur une notion. Ils forment un seul projet Android Studio, une activité par exemple, ouvertes depuis un menu.

| Chapitre | Contenu | Exemples |
|---|---|---|
| Premiers pas | un projet Android (Gradle, manifeste, activité), les fonctions `@Composable`, l'état et la recomposition, les événements (gestes, glissement, focus), tous les types de champs, un réducteur (`sealed interface`, `when`), la validation, des champs qui savent se valider, la mise en forme (`java.time`, `NumberFormat`, extensions) | 01–09 |
| Les composants | paramètres, fonctions en paramètres, composition (emplacements, composant générique), cycle de vie (effets), `CompositionLocal`, comportements réutilisables et animations, fenêtre de confirmation (fonction suspendue) et `Snackbar` | 10–16 |
| La navigation | Navigation Compose : routes typées, paramètres, liens profonds, barre d'onglets, redirection, gardes | 17–18 |
| L'asynchrone et l'état partagé | coroutines, `Flow`, anti-rebond, réponses périmées (`mapLatest`), `produceState`, `ViewModel`, `StateFlow`, DataStore, thème sombre, `SaveableStateHolder` | 19–20 |
| Internationalisation | ressources `strings.xml`, paramètres, pluriels, dates, montants, langue de l'application, calendrier traduit | 21 |
| Le serveur, une boîte noire | installation du serveur JSON, son API, 48 exemples `curl`, ce qui change pour un client mobile | – |
| Dialoguer avec le serveur | OkHttp, l'adresse du serveur (émulateur, téléphone, `adb reverse`), le cookie du jeton sur le téléphone, `kotlinx.serialization`, une couche d'accès à l'API, un `ViewModel`, les erreurs du serveur attachées aux champs | 22–25 |

Chaque exemple est présenté avec son code complet, commenté, et des copies d'écran de son exécution.

## Le serveur : une boîte noire

Le serveur est le serveur NestJS des cours précédents, dont les contrôleurs renvoient du **JSON**. Le cours le traite comme une **boîte noire** : on l'installe, on étudie son API, on l'interroge avec `curl` — mais on n'a pas besoin de lire son code (fourni et commenté pour les curieux).

- toutes les erreurs ont la même forme : `{ "statusCode": 409, "cle": "ERRORS.LOGIN_TAKEN", "params": {...}, "champs": {...} }` — des **clés** de traduction, jamais de texte ;
- authentification par jeton JWT dans un cookie `httpOnly` : sur le téléphone, c'est le client HTTP (OkHttp et son `CookieJar`) qui le range et le renvoie ;
- un « mode test » du captcha pour pouvoir interroger l'API avec `curl` (et fabriquer les copies d'écran par des tests automatiques).

## L'étude de cas : le client Android de RdvMedecins

Une application complète de **prise de rendez-vous dans un cabinet médical**, dont **tous** les fichiers sont listés et commentés.

- **Android moderne** : Kotlin 2.2, Jetpack Compose et Material 3, une seule activité, Navigation Compose, `ViewModel`, coroutines, OkHttp, kotlinx.serialization, une architecture en couches (données, état, interface).
- **Trois rôles** : `ADMIN` (gère les médecins et les clients), `DOCTOR` (prend et annule les rendez-vous), `USER` (le patient : réserve pour lui-même, gère son compte).
- **Confidentialité** : un patient ne reçoit jamais le nom des autres patients — le serveur ne l'envoie pas.
- **Tout l'état d'un écran dans sa route** : `Ecrans.Agenda(idMedecin = 1, jour = "2026-10-05")` ; la fenêtre de réservation est une destination « dialog », que le bouton Retour ferme.
- **Validation par le serveur** : les formulaires affichent sous chaque champ les erreurs renvoyées par l'API ; verrou optimiste, homonymes, login déjà pris...
- **Session** : conservée d'un lancement à l'autre (le cookie est rangé sur l'appareil), rétablie au démarrage (`GET /api/auth/moi`), expiration gérée en un seul endroit (réponse 401), retour à l'écran demandé après la reconnexion.
- **Adapté au téléphone** : menu en tiroir (ou `NavigationRail` sur grand écran et à l'horizontale), listes plutôt que tableaux, bouton flottant, clavier qui ne cache pas les champs, survie à la rotation et au changement de langue.
- **Français / anglais**, avec les dictionnaires JSON des autres clients, y compris le calendrier et les textes d'Android lui-même.
- **Copies d'écran automatiques** : un test instrumenté parcourt l'application comme un utilisateur.
- **Déploiement** : la version de production, signée (APK, Android App Bundle).

## Le contenu du dépôt

```
exemples_kotlin/           les 25 petits exemples (un seul projet Android Studio)
rdvmedecins-nestjs-json/   le serveur JSON RdvMedecins (la « boîte noire »)
rdvmedecins_kotlin/        le client Android de l'étude de cas
outils/                    les scripts Windows qui fabriquent les copies d'écran
```

Chaque projet s'ouvre dans Android Studio (`File / Open`), ou se compile en ligne de commande : `gradlew installDebug`. Les applications joignent le serveur à `http://localhost:8080`, après `adb reverse tcp:8080 tcp:8080` (ou une autre adresse : `gradlew installDebug -Pserveur=http://10.0.2.2:8080`).

## Technologies

Kotlin 2.2 · Jetpack Compose (BOM 2026.02) · Material 3 · Navigation Compose 2.9 · Lifecycle / ViewModel 2.9 · DataStore 1.1 · AppCompat 1.7 · OkHttp 4.12 · kotlinx.serialization 1.9 · AndroidSVG 1.4 · Compose UI Test · AGP 9.4 · Gradle 9.6 · Android 8.0 (API 26) à Android 17 (API 37) · côté serveur : NestJS 10 · TypeORM · MySQL 8 / MariaDB · Passport JWT · svg-captcha

## Prérequis

- Les bases du langage Kotlin : le cours [Kotlin](https://stahe.github.io/kotlin-oct-2026/) est une bonne préparation, mais les notions utilisées sont expliquées au fil des exemples. Des bases du protocole HTTP.
- Android Studio (qui fournit le JDK, Gradle, le SDK Android et l'émulateur) ou un téléphone Android ; Node.js et un serveur MySQL (par exemple Laragon sous Windows) pour le serveur JSON. Les instructions d'installation sont données dans le cours.

## Auteur

Ce cours, ses exemples et le client Android ont été rédigés par **Claude**, l'IA d'[Anthropic](https://www.anthropic.com) (octobre 2026), à la demande de **Serge Tahé**.
