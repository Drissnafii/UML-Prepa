# Révision : Diagrammes de Cas d'Utilisation UML (Use Case Diagrams)

Ce document résume les concepts clés des diagrammes de cas d'utilisation UML, basés sur notre discussion en classe, pour vous aider à préparer votre examen.

## 1. Positionnement des Diagrammes de Cas d'Utilisation

*   **Diagrammes UML :** Il existe deux grandes familles :
    *   **Diagrammes Structurels :** Décrivent la structure statique du système. Exemple : Diagramme de classes.
    *   **Diagrammes Comportementaux :** Décrivent le comportement dynamique du système, comment il réagit et interagit. Exemples : **Diagramme de cas d'utilisation**, Diagramme de séquence.
*   Le **Diagramme de Cas d'Utilisation** appartient donc à la famille des diagrammes **comportementaux**. Son but est de modéliser les interactions entre des entités externes (les acteurs) et le système lui-même, en décrivant les fonctionnalités que le système offre.

## 2. Les Acteurs

Un acteur représente un rôle joué par quelque chose ou quelqu'un qui interagit avec le système. Ce peut être un utilisateur humain, un autre système, ou même un dispositif matériel. On distingue principalement deux types d'acteurs :

### a. Acteur Primaire (Primary Actor)

*   **Définition :** C'est l'acteur qui initie une interaction avec le système pour atteindre un objectif principal. Il a un but précis en utilisant une fonctionnalité du système.
*   **Analogie Théâtrale (comme discuté) :** C'est le **personnage principal**. Il entre en scène avec un objectif clair (raconter son histoire, atteindre un but). L'intrigue principale tourne autour de ses actions et de ses besoins.
*   **Comment l'identifier ?** Posez la question : ***"Qui a besoin d'utiliser le système pour réaliser quelque chose ?"*** ou ***"Qui tire un bénéfice direct et visible de l'exécution du cas d'utilisation ?"***
*   **Exemple (Application Bancaire) :** Le `Client` qui souhaite consulter son solde, effectuer un virement, ou déposer de l'argent. C'est lui qui *demande* quelque chose au système.

### b. Acteur Secondaire (Secondary Actor)

*   **Définition :** C'est un acteur dont le système a besoin pour réaliser le service demandé par l'acteur primaire. Il ne déclenche pas l'interaction lui-même, mais il fournit une aide ou une information nécessaire au système.
*   **Analogie Théâtrale (comme discuté) :** C'est un **personnage de soutien**. Il n'est pas celui qui a initié l'action principale, mais il est essentiel pour que l'histoire du personnage principal se déroule (le confident, le messager, l'obstacle à surmonter). Il est *sollicité* par le déroulement de l'histoire.
*   **Comment l'identifier ?** Posez la question : ***"De qui ou de quoi le système a-t-il besoin (aide, information, validation) pour pouvoir répondre à la demande de l'acteur primaire ?"***
*   **Exemple (Application Bancaire) :**
    *   Si un `Client` (primaire) fait une demande de prêt, le système pourrait avoir besoin d'interroger un `Système d'Évaluation de Crédit` externe (secondaire) pour obtenir le score de crédit.
    *   Lors d'un paiement en ligne via une application e-commerce, le `Système de Paiement` (comme Visa, Mastercard) est un acteur secondaire que l'application doit solliciter pour valider la transaction initiée par le `Client` (primaire).

## 3. Les Relations dans les Diagrammes de Cas d'Utilisation

### a. Association Directe

*   **Définition :** C'est le lien le plus simple, représenté par une ligne pleine entre un acteur et un cas d'utilisation (une fonctionnalité, représentée par une ellipse). Elle signifie simplement que l'acteur participe à ce cas d'utilisation (il l'initie ou interagit avec lui).
*   **Exemple :**
    *   `Client` <------> `Consulter Solde`
*   **Cas des Notifications/Vérifications :** Vous avez mentionné `enseignant ==> creation`, `admin => msg of the action "confermation de creation ....."`. Dans un diagramme de cas d'utilisation, on modélise les *fonctionnalités*.
    *   L'`Enseignant` serait lié par une association directe au cas d'utilisation `Créer un Cours`.
    *   L'`Administrateur` pourrait être lié à un cas d'utilisation comme `Consulter les Notifications` ou `Valider Création Cours`. Si le système envoie *passivement* une notification, ce n'est pas toujours modélisé comme un cas d'utilisation initié par l'Admin, mais l'Admin peut avoir un cas d'utilisation pour *agir* sur cette information. L'association directe montre donc la *capacité* d'interaction.

### b. Relation `<<include>>` (Inclusion)

*   **Définition :** Indique qu'un cas d'utilisation (le cas de base) **doit obligatoirement** inclure le comportement d'un autre cas d'utilisation (le cas inclus) pour pouvoir se réaliser. Le cas inclus est une sous-partie nécessaire du cas de base. C'est utilisé pour factoriser un comportement commun à plusieurs cas d'utilisation ou pour décomposer un cas complexe.
*   **Comment la reconnaître ?** Le cas de base *ne peut pas* se terminer sans exécuter le cas inclus.
*   **Représentation :** Flèche en pointillés depuis le cas de base vers le cas inclus, avec le stéréotype `<<include>>`.
*   **Exemple :** Pour `Effectuer un Virement` ou `Consulter l'Historique`, il faut **toujours** `S'authentifier`.
    *   `Effectuer Virement` ---------> `S'authentifier`
                           `<<include>>`
    *   `Consulter Historique` --------> `S'authentifier`
                            `<<include>>`

### c. Relation `<<extend>>` (Extension)

*   **Définition :** Indique qu'un cas d'utilisation (le cas étendant) **peut optionnellement** ajouter un comportement supplémentaire à un autre cas d'utilisation (le cas étendu ou cas de base) sous certaines conditions (appelées "points d'extension"). Le cas de base peut s'exécuter **sans** l'extension. C'est utilisé pour modéliser des comportements optionnels ou alternatifs.
*   **Comment la reconnaître ?** L'exécution du cas étendant est conditionnelle et non obligatoire pour le cas de base.
*   **Représentation :** Flèche en pointillés depuis le cas étendant vers le cas étendu (cas de base), avec le stéréotype `<<extend>>`.
*   **Exemple (Correction/Validation de votre exemple) :** Votre exemple était **correct** !
    *   Un `Client` effectue une `Passer Commande` (cas de base).
    *   *Après* la commande, le client a l'*option* d'`Imprimer la Facture` (cas étendant). L'impression n'est pas obligatoire pour que la commande soit valide.
    *   Le cas `Imprimer la Facture` *étend* donc le cas `Passer Commande`.
    *   `Imprimer Facture` ---------> `Passer Commande`
                           `<<extend>>`
    *   Autre exemple : Dans le cas `Passer Commande`, on pourrait avoir une extension `Ajouter une Assurance` qui n'est proposée que si le montant dépasse un certain seuil (condition).

## 4. Résumé des Points Clés pour l'Examen

*   **Objectif :** Modéliser les fonctionnalités du système du point de vue de l'utilisateur (`Qui fait Quoi ?`).
*   **Acteur Primaire :** Celui qui initie, qui a un objectif (`Qui a besoin ?`).
*   **Acteur Secondaire :** Celui qui est sollicité par le système pour aider (`Qui aide ?`).
*   **Cas d'Utilisation :** Une fonctionnalité offerte par le système (verbe à l'infinitif + complément).
*   **Association :** Lien simple Acteur <-> Cas d'Utilisation.
*   **`<<include>>` :** Obligatoire. Le cas de base *a besoin* du cas inclus. Flèche : Base -> Inclus.
*   **`<<extend>>` :** Optionnel/Conditionnel. Le cas étendant *ajoute* au cas de base. Flèche : Étendant -> Base.

---

**Petit Exercice de Réflexion :**
Pensez à un distributeur automatique de billets (ATM).
*   Qui est l'acteur primaire ?
*   Quels sont les cas d'utilisation principaux (ex: Retirer Argent, Consulter Solde) ?
*   Y a-t-il un cas d'utilisation qui serait *inclus* dans plusieurs autres (ex: S'identifier avec la carte) ?
*   Y a-t-il un cas d'utilisation qui pourrait être une *extension* (ex: Demander un reçu papier) ?
*   Y a-t-il un acteur secondaire (ex: Système Central de la Banque) ?