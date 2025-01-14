# Evaluation C++ 2025 - Sujet n°2

## Consignes
Les documents, l'accès à Internet et l'usage des postes personnels est autorisé. **L'usage d'outils d'intelligence artificielle (Copilot compris) est interdit. Toute communication avec des personnes extérieures ou des stockages externes en vue d'échanger des informations est interdite.**

**La lecture intégrale du sujet est nécessaire avant de commencer à développer.**

Il est important que vous travailliez en suivant une logique incrémentale. **N'essayez pas de TOUT faire directement.**

Le résultat attendu doit être placé dans un dépôt github **privé** nommé `polytech-dijon-3a-cpp`. Vous ajouterez en collaborateurs Monsieur Chassel (`esirem-chassel`) et Monsieur Meunier (`cmeunier-ub`).

Un makefile ou un cmakelist doit être présent, valide et configuré pour générer l'exécutable final (nommé `productionline`) dans un dossier nommé `final`. **Le versionning des binaires n'est pas souhaité**.

## Notions évaluées

- Création de classes
- Héritage
- Algorithme
- Références et pointeurs
- Manipulation de fichiers
- Gestion des exceptions
- Qualité du code 

## Contexte
 On vous demande de virtualiser une chaine d'embouteillage pour du soda. Chaque machine possède un rôle différent (fabriquer des bouteilles, fabriquer du soda, remplir une bouteille, ...).

 Des buffers interconnectent les machines. Ils contiennent les matières premières ou les produits finis en fonction d'où ils se trouvent par rapport à la machines.

 Le visuel ci-dessous présente la chaine de production à virtualiser. Les machines sont illustrées par des rectangles et les buffers par des ronds.

 ```mermaid
 ---
 title: Chaine de production de soda
 config:
    look: handDrawn
 ---
 %%{init: {'theme':'neutre', 'themeVariables':{'fontSize':'10px'}}}%%
 
 flowchart LR
 %% Buffers
 sucre((Sucre))
 eau((Eau))
 aromes((Arômes))
 soda((Soda))
 verre((Verre))
 bouteilles((Bouteilles))
 bouteilles_remplies((Bouteilles remplies))
 bouteilles_fermees((Bouteilles fermées))
 colis(((Colis)))

%% Machines
fab_soda(Fabrication Soda)
fab_bouteilles(Fabrication de bouteilles)
fab_remplissage(Remplissage)
fab_encapsulage(Ecapsulage)
fab_expedition(Expédition)

%% Links
sucre                   --> fab_soda
eau                     --> fab_soda
aromes                  --> fab_soda            --> soda
verre                   --> fab_bouteilles      --> bouteilles
soda                    --> fab_remplissage
bouteilles              --> fab_remplissage     --> bouteilles_remplies
                            fab_remplissage     --> verre
bouteilles_remplies     --> fab_encapsulage     --> bouteilles_fermees
                            fab_encapsulage     --> verre
bouteilles_fermees      --> fab_expedition      --> colis
 ```

 A chaque étape de la chaine, il est possible qu'il y ait de la casse au niveau des bouteilles. Dans ce cas, le verre est renvoyé dans le buffer d'entrée de la fabrication des bouteilles.

 En fin de chaine, les bouteilles de soda sont empactées dans des fichiers représentant les expéditions.

 ## 1. Réflexion

Cette section est présente afin que vous preniez le temps de réfléchir au projet dans sa globalité avant de commencer à coder.

### 1.1 Machines
Chaque machine possède un nom.

Chaque machine peut être connectés à plusieurs (ou aucun) buffers d'entrée et plusieurs (ou aucun) buffers de sortie.

Toutes les machines sont contrôlées de manière "synchrone" par un "même" signal d'horloge. Il faut donc être capable de placer toutes les machines dans une liste afin de parcourir cette dernière et d'envoyer un tick à toutes les machines qui s'y trouvent.

De manière générale, une machine prend ce dont elle a besoin dans ses buffers d'entrée et place le résultat de sa production dans ses buffers de sortie.

Certaines machines ont des tâches complexes à réaliser qui prennent plus d'un tick d'horloge pour être mener à bien. 

### 1.2 Buffers
Lors de son initialisation, un buffer se voit attribué un nom, un stock initial (un certain nombre d'éléments qui préremplissent le buffer) et une valeur de stock maximal que le buffer ne peut pas dépasser.

Un buffer dispose de cinq fonctionnalités :
- Stocker quelque chose (push)
- Fournir quelque chose (pop)
- Indiquer s'il est vide (isEmpty)
- Indiquer si le stock est plein (isFull)
- Donner l'état de son stock sous la forme d'une chaine de caractères (getStatus)

Pour simplifier les choses, les éléments utilisés dans les différents processus de fabrication (verre, bouteille, soda, ...) seront représentés par une lettre minuscule (exemple : une unité de soda sera représentée par un `'s'`).

### 1.3 Programme principal

Au lancement, la chaine de production doit être initialisée (création et préremplissage des buffers, création et interconnection des machines).

Puis l'utilisateur doit appuyer sur une touche pour envoyer **un** signal d'horloge aux machines.

A chaque étape, l'état de chaque buffer doit être affiché.

## 2. Travail à réaliser

### 2.0 Dépôt GitHub

+ Créez votre dépôt GitHub comme indiqué dans les consignes.

> [!WARNING]Attention
> **Pensez bien à réaliser un commit à chaque question**

### 2.1 Programme de test

+ Codez un main vide et testez la compilation de votre programme pour vérifier que tout fonctionne correctement.

### 2.2 Classe Buffer

+ Créez une classe `Buffer` répondant au critères du paragraphe [1.2](#12-buffers).
+ Testez le bon fonctionnement de votre classe Buffer dans votre programme de test (exemple : vider le contenu d'un premier buffer dans un second et afficher l'état de chaque buffer après l'opération)

### 2.3 Classe Machine
La classe `Machine` sera la classe de base dont hériteront toutes les machines de la chaine de production.

Comme vu au paragraphe [1.1](#11-machines), une instance de `Machine` possède un nom et peut être connectés à plusieurs buffers d'entrée et de sortie.

+ Créez la classe `Machine`
+ Ajoutez deux méthodes `addInput` et `addOutput` permettant de connecter la machine à ses buffers
+ Ajoutez une méthode abstraite `tick` qui permet à la machine d'avancer d'une étape dans son processus de fabrication.

### 2.4 Classe MachineMakeBottles

+ Créez la classe `MachineMakeBottles` qui prend une unité de verre (v) et fournit une bouteille vide (b) au bout de deux ticks.
+ Testez le bon fonctionnement de votre machine dans le programme de test.

### 2.5 Classe MachineMakeSoda

+ Créez la classe `MachineMakeSoda` qui prend deux unités de sucre (z), trois unités d'eau (e) et une unité d'âromes (a) pour produire deux unités de soda (s) en trois ticks.
+ Testez le bon fonctionnement de cette machine.
  
### 2.6 Classe MachineFillBottle

+ Créez la classe `MachineFillSoda` qui prend une bouteille (b) et une unité de soda (s) pour fournir une bouteille remplie (r) à chaque tick.
+ Testez le bon fonctionnement de cette machine

### 2.7 Première interconnexion
+ Créez une chaine de production rassemblant les trois machines précédemment créées et testez le fonctionnement global de la chaine.
+ Le programme affichera l'état des buffer à chaque étape.
  
### 2.8 Problèmes de buffer
Il est possible qu'un buffer d'entrée soit vide et ne puisse pas fournir une machine en matière première. De la même façon, il est possible qu'un buffer de srotie soit plein et ne puisse pas réceptionner la production d'une machine.

+ Prenez en compte ses problématiques dans les machines précédentes.
+ Levez une exception lorsque ce type de problèmes survient.
+ Capturez l'exception dans le programme de test de la chaine de production afin d'afficher le problème à l'utilisateur.

### 2.9 Classe MachineMakeEncapsulation

+ Créez la classe `MachineMakeEncapsulation` qui prend une bouteille remplie (r) et fournit une bouteille fermée (f) à chaque tick.
+ Testez le bon fonctionnement de cette machine.
+ Couplez cette machine à votre chaine de production.

### 2.10 Classe MachinePrepareShipping

+ Creéez la classe `MachinePrepareShipping` qui prend un nombre aléatoire (N) de bouteilles fermées (f) et les place dans un fichier nommé de manière incrémentale ("colis-1.txt" pour le premier, "colis-2.txt" pour le second, ...). La valeur de N est regénérée à chaque nouveau colis. La machine peut empaqueter deux bouteilles par tick. Le fichier est créé lorsque toutes les bouteilles nécessaires ont été récupérées dans le buffer d'entrée.
+ Testez le bon fonctionnement de cette machine.
+ Couplez cette machine à votre chaine de production.

### 2.11 Risque de casse

Lors des opréations de fabrication de bouteilles, d'embouteillage, d'encapsulage et de préparation de colis, il est possible que des bouteilles soient cassées :
- Fabrication : 20%
- Embouteillage : 5%
- Encapsulage : 5%
- Préparation de colis : 10%

Chaque bouteille cassée ajoute une unité de verre au buffer d'entrée de la machine qui fabrique les bouteilles.

+ Intégrez le risque de casse à vos machines et adaptez votre chaine de production.
+ Lorsque 'une bouteille est cassée, un message doit être remonté à l'utilisateur.
+ Testez le bon fonctionnement de votre chaine de production complète.

### 2.12 Classe ProductionLine
 
+ Avez-vous pensé à créer une classe `ProductionLine` ? Sinon, il est grand temps de le faire pour avoir une `main` tout propre !

### 2.13 Peaufinage
Bien que ce soit un peu tard, mieux vaut tard que jamais...

+ Vérifiez que votre code est bien structuré et qu'il n'y a pas de redondance, que les variables et méthodes sont biens nomméees. Dans le cas contraire, apportez les améliorations nécessaires.