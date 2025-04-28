# Révision : Diagrammes de Classes UML (Class Diagrams)

Après avoir vu les cas d'utilisation qui décrivent *quoi* fait le système du point de vue de l'utilisateur, les diagrammes de classes nous montrent la structure *statique* interne du système : quelles sont les "briques" logicielles (les classes), quelles informations elles contiennent (attributs) et ce qu'elles peuvent faire (méthodes), ainsi que comment elles sont reliées entre elles.

## 1. Les Classes : Blocs de Construction

*   Une classe est un modèle pour créer des objets. Elle est représentée par un rectangle divisé en trois parties (optionnelles) :
    *   **Nom de la Classe :** En haut, centré et en gras.
    *   **Attributs (Attributes) :** Au milieu, liste des données que les objets de cette classe possèdent (ex: `nom : String`, `age : Integer`). On précise souvent la visibilité (`+` pour public, `-` pour privé, `#` pour protégé).
    *   **Méthodes (Operations) :** En bas, liste des actions que les objets de cette classe peuvent réaliser (ex: `+ calculerAge() : Integer`, `+ enregistrerAdresse(nouvelleAdresse : String)`).

## 2. Relations entre Classes

Comment les classes interagissent ou sont liées entre elles.

### a. Généralisation / Spécialisation (Héritage - Inheritance)

*   **Concept :** Représente une relation "est un type de" (is-a). Une classe enfant (sous-classe) hérite des attributs et méthodes de la classe parent (super-classe), mais peut ajouter les siens ou modifier ceux hérités.
*   **Objectif :** Réutilisation de code, polymorphisme, création de hiérarchies logiques.
*   **Représentation :** Une ligne pleine avec une **flèche triangulaire vide** pointant vers la **super-classe** (la classe la plus générale).
*   **Exemple :** `Voiture` et `Moto` sont des types de `Vehicule`. `Voiture` et `Moto` héritent des propriétés de `Vehicule` (comme `immatriculation`, `demarrer()`) mais ont leurs spécificités (`nombreDePortes` pour `Voiture`, `cylindree` pour `Moto`).
    ```
       Vehicule <|--- Voiture
                <|--- Moto
    ```

### b. Association

*   **Concept :** Une relation structurelle générale indiquant que des objets de différentes classes sont connectés ou interagissent. C'est la relation la plus courante. On peut spécifier la **multiplicité** (combien d'objets de chaque classe participent à la relation : `1`, `*` (plusieurs), `0..1` (zéro ou un), `1..*` (un ou plusieurs)).
*   **Représentation :** Une ligne pleine entre les deux classes. On peut ajouter un nom à l'association et des rôles aux extrémités.
*   **Exemple :** Un `Client` peut passer plusieurs `Commandes`.
    ```
    Client 1 ---- 0..* Commande
         passe >
    ```

### c. Agrégation (Aggregation - Relation "Faible")

*   **Concept :** Une forme spécifique d'association représentant une relation "fait partie de" ou "a un" (part-whole), mais où la partie peut exister **indépendamment** du tout. C'est une agrégation dite "faible" ou "partagée". La destruction du "tout" n'entraîne pas forcément la destruction des "parties".
*   **Représentation :** Une ligne pleine avec un **losange vide** du côté de la classe "tout" (l'agrégat).
*   **Exemple :** Un `Departement` contient des `Professeurs`. Si le département est dissous, les professeurs existent toujours et peuvent être rattachés ailleurs.
    ```
    Departement <>---- * Professeur
    ```

### d. Composition (Relation "Forte")

*   **Concept :** Une forme plus forte d'agrégation où la partie **ne peut pas exister sans** le tout. Le "tout" est responsable de la création et de la destruction de ses "parties". La durée de vie de la partie est liée à celle du tout.
*   **Représentation :** Une ligne pleine avec un **losange plein/noir** du côté de la classe "tout" (le composite).
*   **Exemple :** Une `Voiture` est composée d'un `Moteur` et de `Roues`. Si la voiture est détruite, son moteur spécifique et ses roues spécifiques le sont aussi (conceptuellement, ils ne peuvent appartenir à une autre voiture ou exister seuls). La multiplicité côté "tout" (Voiture) est souvent `1` ou `0..1`.
    ```
    Voiture <#>---- 1 Moteur
            <#>---- 4 Roue
    ```

## 3. Classes d'Association (Analogie Table Pivot)

*   **Concept :** Parfois, une simple ligne d'association ne suffit pas car la relation *elle-même* porte des informations ou a un comportement. C'est fréquent dans les relations Plusieurs-à-Plusieurs (`*..*`). Dans une base de données, cela correspond souvent à une **table de jonction** (ou table pivot).
*   **Représentation :** Une classe (la classe d'association) reliée par une **ligne pointillée** à la ligne d'association principale entre les deux classes de base.
*   **Exemple (Client, Event, Reservation) :**
    *   Un `Client` peut réserver plusieurs `Events`.
    *   Un `Event` peut être réservé par plusieurs `Clients`.
    *   La réservation elle-même a des informations : `dateReservation`, `statutPaiement`, `numeroSiege`. Ces informations n'appartiennent ni uniquement au `Client` (il a plusieurs réservations), ni uniquement à l'`Event` (il a plusieurs participants). Elles appartiennent à la *relation* entre un `Client` et un `Event`.
    *   `Reservation` devient une **classe d'association**.
    ```
           +-------------------+ *         * +-------------+
           |      Client       |-------------|    Event    |
           +-------------------+             +-------------+
                    |
                    |  (ligne pointillée)
                    |
           +------------------------+
           |      Reservation       |
           +------------------------+
           | - dateReservation: Date |
           | - statutPaiement: String|
           | - numeroSiege: String   |
           +------------------------+
    ```

## 4. Conflits et Placement des Méthodes : Où mettre le comportement ?

C'est une question fondamentale en conception orientée objet, guidée par le principe de **responsabilité** (Single Responsibility Principle - SRP) et le principe de l'**Expert en Information** (Information Expert - GRASP). En bref : **une méthode doit être placée dans la classe qui possède la majorité des informations nécessaires pour l'exécuter.**

### a. Exemple : `reserver()` pour un `Client` et un `Event`

*   **Option 1 : `Client.reserver(Event)` ?**
    *   *Pour :* Le client est l'acteur qui initie l'action. C'est intuitif.
    *   *Contre :* Le `Client` a-t-il les informations sur la disponibilité de l'`Event` ? Connaît-il les règles de tarification ? Comment gère-t-il le statut de la réservation ? Probablement pas. La classe `Client` est responsable des informations sur le client (nom, adresse...). Ajouter la logique de réservation la surchargerait (violation du SRP) et nécessiterait qu'elle accède à beaucoup de données d'`Event` et de `Reservation` (couplage élevé).
*   **Option 2 : `Event.ajouterReservation(Client)` ?**
    *   *Pour :* L'`Event` connaît sa capacité, peut-être ses règles.
    *   *Contre :* L'`Event` devient responsable de la création d'objets `Reservation`. Est-ce son rôle principal ? Quid des infos spécifiques à la réservation (siège, paiement...) ?
*   **Option 3 : `Reservation.creer(Client, Event)` (ou constructeur `new Reservation(Client, Event)`) ?**
    *   *Pour :* La classe `Reservation` est l'experte concernant les données d'une réservation. Logique.
    *   *Contre :* Qui *appelle* cette création ? La `Reservation` ne s'auto-crée pas magiquement.
*   **Option 4 (Souvent préférable) : Une classe de Service/Gestionnaire (`GestionnaireReservations`, `ReservationService`)**
    *   *Méthode :* `gestionnaire.creerReservation(Client, Event)`
    *   *Pour :* Cette classe *coordonne* l'action. Elle peut vérifier la disponibilité auprès de l'`Event`, créer l'objet `Reservation` (qui contient les données propres à la résa), et lier le `Client` et l'`Event`. Cela respecte mieux le SRP pour `Client`, `Event` et `Reservation`.

*   **Conclusion `reserver()` :** La logique métier complexe de la réservation est mieux placée dans une classe dédiée (Service) ou potentiellement partagée entre `Event` (disponibilité) et `Reservation` (détails de la réservation). Mettre `reserver()` directement dans `Client` est rarement la meilleure solution car le Client n'est pas l'expert en information pour cette opération.

### b. Exemple : `conduire()` pour une `Voiture` et un `Utilisateur`

*   **Option 1 : `Utilisateur.conduire(Voiture)` ?**
    *   *Pour :* L'utilisateur *effectue* l'action de conduire.
    *   *Contre :* L'`Utilisateur` ne connaît pas la mécanique interne de la voiture (comment le moteur tourne, comment les freins fonctionnent, la consommation...). Il ne *possède pas* l'information nécessaire pour *implémenter* la conduite.
*   **Option 2 : `Voiture.demarrer()`, `Voiture.accelerer(int pourcentage)`, `Voiture.freiner()`, `Voiture.tournerVolant(Angle)` ?**
    *   *Pour :* La `Voiture` est l'**experte en information**. Elle connaît son état (vitesse, niveau de carburant, état du moteur) et sait comment ses composants réagissent. Les méthodes représentent les capacités intrinsèques de la voiture. C'est la voiture qui *sait* comment accélérer.
    *   *Contre :* Aucun inconvénient majeur du point de vue de la responsabilité.

*   **Conclusion `conduire()` :** Les méthodes décrivant les *actions de la voiture* (`accelerer`, `freiner`, etc.) appartiennent **à la classe `Voiture`**. Un objet `Utilisateur` peut *appeler* ces méthodes (ex: `maVoiture.accelerer(50)`), agissant comme un déclencheur, mais la logique elle-même réside dans la `Voiture`. L'action "conduire" est une *collaboration* où l'Utilisateur interagit avec les capacités de la Voiture.

**Règle Générale :** Cherchez la classe qui a le plus de **données** nécessaires à une opération. C'est généralement là que la méthode doit résider pour minimiser le couplage (dépendances entre classes) et maximiser la cohésion (une classe fait une chose bien définie).