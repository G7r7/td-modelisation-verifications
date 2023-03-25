# TD Modélisation et Vérification

## 1. Notions de bases (rappels et/ou introduction)

### 1.1 Question de cours

Définition d'une machine a état fini (FSM) de Mealy :

C'est un automate fin avec sorties utilisé pour concevoir des programmes. Les sorties dépendent des états courants et des symboles d'entrée.\

a) Une FSM est déterministe lorsque la relation de transition est une fonction S x I => S. C'est-à-dire pour chaque état et chaque symbole d'entrée, il existe un seul état suivant.

b) Une FSM est partiellement specifiée quand certaines transitions ne sont pas définies pour certains états et symboles.

c) Une FSM est fortement connectée lorsque tous ses états sont accessibles les un des autres via des transitions.

d) Une FSM est minimale si elle a le nombre minimum d'états nécéssaires pour représenter le comportement spécifié.

## 2. Modélisation avec les automates

### Exercice 1

#### Question 1

Cet automate semble décrire un horloge à affichage numérique (hh:mm).\
L'état initial est l'état `E0`. On doit d'abord afficher les heures.\
Si le premier chiffe de l'heure actuelle est 0 ou 1 (< 20h) on part vers l'état `H1`.\
Sinon on part vers l'état `H2`.\
Dans l'état `H1`on peut saisir les unités des heures entre 0 et 9 (00h à 19h).\
Dans l'état `H2`on peut saisir les unités des heures entre 0 et 3 (20h à 23h).\
On passe ensuite à l'état `H`quand on a fini d'afficher les heures.\
On affiche le caractère `:` et on passe à l'état `M1` pour afficher les dizaines des minutes.\
On affiche un chiffre entre 0 et 5 pour les dizaines et on passe à l'état `M2`.\
Finalement on saisit la valeur des unités des minutes entre 0 et 9 et on arrive à l'état final `M`.\

#### Question 2

S (états) = {E0, H1, H2, H, M1, M2, M}

I (alphabet) = {0,1,2,3,4,5,6,7,8,9,:}

delta (transitions) = {(E0,H1,{0,1}), (E0, H2, {2}), (H1,H, {0..9}), (H2,H, {0,1,2,3}), (H,M1, {:}), (M1, M2, {0..5}), (M2, M, {0..9})}

S0 (état initial) = {E0}

Sf (état final) = {M}

| State | 0  | 1  | 2  | 3  | 4  | 5  | 6 | 7 | 8 | 9 | :  |
|-------|----|----|----|----|----|----|---|---|---|---|----|
| E0    | H1 | H1 | H2 |    |    |    |   |   |   |   |    |
| H1    | H  | H  | H  | H  | H  | H  | H | H | H | H |    |
| H2    | H  | H  | H  | H  |    |    |   |   |   |   |    |
| H     |    |    |    |    |    |    |   |   |   |   | M1 |
| M1    | M2 | M2 | M2 | M2 | M2 | M2 |   |   |   |   |    |
| M2    | M  | M  | M  | M  | M  | M  | M | M | M | M |    |
| M     |    |    |    |    |    |    |   |   |   |   |    |

#### Question 3

```JavaScript
function automate(input: string){ // Exemple: "13:08"
  let currentNode: Etat = E0;
  for (let char in input) { // On itère sur les caractères de l'entrée
    const transition = currentNode.transitions[char];
    currentNode = transition.to;
    console.log(`${currentNode} ${char}`);
  }

  if (currentNode == EF) {
    print("La suite de caractères en entrée est reconnue")
  }else{
    throw new Error("Chaine de caractères non reconnue");
  }
}
```

#### Question 4

Pour traiter les erreurs, on peut en cas de lecture d'un caractère inattentdu faire une transition vers l'état courant. Ainsi on laisse une autre chance de lire un caractère valide.

#### Question 5


## 3. Methodes de génération de tests à partir de machines à états finis

### Exercice 1

#### Question 1

| Input (TS) | ababab |
|------------|--------|
| Output     | 010010 |

#### Question 2

| State       | S0 | S1 | S2 | S0 | S1 | S2 | S0 | S1 | S2 |
|-------------|----|----|----|----|----|----|----|----|----|
| Input (TS)  | a  | a  | a  | b  | b  | b  | ab | ab | ab |
| Output      | 0  | 1  | 0  | 1  | 0  | 0  | 01 | 10 | 00 |
| Output State| S0 | S1 | S2 | S2 | S0 | S1 | S2 | S0 | S1 |

DS = a.b

#### Question 3

| Input (TS) | ababab |
|------------|--------|
| Output     | 010010 |

La sortie est correcte cependant à la fin du tour de tranisition on ne revient pas à l'état initial. On peut considérer que l'implémentation est mauvaise.

#### Question 4

| State       | S0 | S1 | S2 |
|-------------|----|----|----|
| Input (TS)  | ab | ab | ab |
| Output      | 01 | 10 | 00 |
| Output State| S2 | S2 | S1 |

Lorsqu'on applique la DS on voit que chacun des états produits une sortie différente. Cependant si on regarde les états de sortie on voit que S1 fini dans S2 alors que dans l'implémentation S1 fini dans S0. On peut conclure que l'implémentation I est fausse.
