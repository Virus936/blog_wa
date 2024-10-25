## Introduction

Je vous ai déjà parlé de mon addiction ? À un moment donné, je ne pouvais pas m’empêcher de passer des heures sur un jeu qu'on appelle Mots de lettres. Pour ceux qui ne connaissent pas, c’est un jeu où tu dois former des mots à partir de 5 lettres. Franchement, c’est un concept qui semble simple, mais ce jeu est tellement pourri qu’il te vole ta vie sans que tu t’en rendes compte ! J’y passais facilement 2 à 3 heures par jour, à tourner en rond sur un tas de lettres. Je me suis vraiment senti comme une merde à perdre autant de temps sur une bouse pareille. Frustré par tout ce temps foutu en l'air, j'ai décidé de prendre les choses en main et de développer une solution qui me permettrait de trouver tous les mots en un clin d'œil.


## Une approche naïve pour trouver des mots

Bon, la première idée qui m'est venue à l'esprit, c'était de faire ça à la bourrin. Genre, pas de prise de tête : prendre toutes les lettres, balancer toutes les combinaisons possibles, et voir ce qui passe. Une méthode tellement brute que même un escargot aurait trouvé ça lent, mais sur le coup, ça semblait faisable.

### La méthode de recherche simple (ou comment perdre encore plus de temps)

J’ai donc pris mes 5 lettres et j’ai tenté de générer absolument toutes les combinaisons possibles, des mots d’une lettre jusqu’à cinq, histoire d’être sûr de rien louper. Ensuite, pour chaque combinaison, je vérifiais si ça existait dans le dico. Franchement, j'étais persuadé d'avoir là une bonne solution. Mais que dalle.

Pour bien comprendre le délire, imaginons juste trois lettres : **A**, **B**, et **C**. Voilà toutes les combinaisons possibles que cette approche va générer :

- **Mots d’une lettre** :
  - A, B, C

- **Mots de deux lettres** :
  - AB, AC, BA, BC, CA, CB

- **Mots de trois lettres** :
  - ABC, ACB, BAC, BCA, CAB, CBA

Avec juste trois lettres, on arrive à **13 combinaisons uniques**. Ajoute deux lettres, et ça explose en **325 combinaisons**. Imagine le temps de calcul pour chacune d’elles si on doit vérifier leur existence dans un dictionnaire : l’algo fait passer chaque combinaison dans le dico pour voir si c'est un mot. Et là, le nombre de comparaisons devient vite interminable.

### Le code de l’approche naïve

Voici le code de cette méthode brute :

```python
from itertools import permutations

def generate_words(letters):
    possible_words = []
    for i in range(len(letters)):
        for combination in permutations(letters, i + 1):
            word = ''.join(combination)
            if is_valid_word(word):
                possible_words.append(word)
    return possible_words

def is_valid_word(word):
    return word in dictionary
```

### La complexité en temps

La complexité de cette méthode est **O(n * n!)** :
- Le nombre de permutations pour une liste de `n` lettres est de **n!** (factorielle de n), et pour chaque permutation, on génère un mot et on fait une vérification dans le dictionnaire.
- Chaque mot demande un temps constant pour la vérification (si le dico est un set ou un hash), donc on multiplie tout par `n`, d’où le **O(n * n!)**.

### Pourquoi cette approche est pourrie

Avec cette approche, j’ai vite compris que c’était pas juste un peu lent, c'était l’agonie. Le PC chauffait, le ventilo soufflait comme si j’étais en train de miner des cryptos.
