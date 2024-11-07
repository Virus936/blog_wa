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

En gros, 
- pour 3 éléments, on aurait 3 + 3.2 + 3.2.1
- pour 4 éléments, on aurait 4 + 4.3 + 4.3.2 + 4.3.2.1
- pour 5 éléments, on aurait 5 + 5.4 + 5.4.3 + 5.4.3.2 + 5.4.3.2.1

En généralisant, 
$$\sum_{k=1}^{n} \frac{n!}{(n - k)!}$$

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

La complexité de cette méthode est **O(n * n!)**. On a une complexité de zinzin ! 
Avec cette approche, j’ai vite compris que c’était pas juste un peu lent, c'était l’agonie. Le PC chaufferait, ce serait pire que de miner des cryptos.

Il me faut une autre approche !



### Une meilleure approche : le Trie


Bon, là, il est temps de parler du **Trie** (ça se prononce "traille"). Oublie la méthode naine qui m'a fait perdre un temps fou. Le Trie, c’est comme passer d’un vélo à une moto de compétition. Avec cet algo, on peut gérer les mots de manière beaucoup plus efficace. Si j’avais su, j’aurais pu éviter des crises de nerfs en regardant mon PC galérer à chaque combinaison.

### Qu'est-ce qu'un Trie ?

Selon wikipedia, *en informatique, un trie ou arbre préfixe, est une structure de données ayant la forme d'un arbre enraciné. Il est utilisé pour stocker une table associative où les clés sont généralement des chaînes de caractères. Contrairement à un arbre binaire de recherche, aucun nœud dans le trie ne stocke la chaîne à laquelle il est associé. C'est la position du nœud dans l'arbre qui détermine la chaîne correspondante.*

Bon, comme toute définition technique, c'est facile à comprendre si tu sais ce que c'est... Sinon c'est de l'elfique ! 

Je vais tenter de t'expliquer.

Un Trie, ça fonctionne comme un arbre où chaque nœud représente un caractère d'un mot. En gros, tu construis un chemin pour chaque mot à partir de la racine. Ça permet de rechercher, insérer et vérifier des mots en un temps record. En plus, quand tu veux vérifier si un mot existe ou trouver tous les mots qui partagent un préfixe commun, ça devient un jeu d’enfant. Pas besoin de faire des permutations à gogo, tu parcours simplement l'arbre.

Par exemple, 
Au début, notre arbre ressemble à ça : 

![CleanShot 2024-11-07 at 11 08 18](https://github.com/user-attachments/assets/4585ff71-3217-48d0-adb0-6e25f619bf79)


Ensuite, on insère le mot *art*, on a alors l'arborescence suivante :

![CleanShot 2024-11-07 at 11 08 53](https://github.com/user-attachments/assets/f2b37094-2de0-4a41-82c0-61f3e1e26492)

on insère chine :

![CleanShot 2024-11-07 at 11 09 01](https://github.com/user-attachments/assets/aef1efd9-cf3a-443a-bcef-38de2b4995a5)

on insère chien :

![CleanShot 2024-11-07 at 11 09 11](https://github.com/user-attachments/assets/48ef712e-9ebf-4d0c-882b-28a4e556589c)

Ainsi, il est assez facile de chercher si un préfixe existe (*ch* est un préfixe commun de *chien* et *chine*). Toutes les "sous-branches" de *c-h* sont des mots dont le préfixe est *ch*

### Pourquoi c'est mieux ?

Avec le Trie, le truc de ouf, c'est que tu peux chercher des préfixes comme un pro. Quand tu cherches un mot, tu commences par voir si le préfixe est là. Si tu vois qu’il est pas présent, t’as pas à te faire chier, tu sais que rien ne pourra commencer par cette combinaison de lettres. Donc, tu peux jeter à la poubelle toutes les combinaisons qui vont avec ce préfixe, et ça te fait gagner un temps fou.
Par exemple, avec les lettres N-E-H-I-C, aucun mot ne commence par *NH*, donc inutile de vérifier si les mots NHICE, NHIEC, NHCIE, NHCEI, NHECI, NHEIC, NHIC, NHIE, NHCI, NHCE, NHEI, NHEC, NHE, NHI, NHC. Ça nous fait une économie de 15 mots à ne pas vérifier.

En gros, le Trie t’aide à filtrer les mots dès le départ. Tu te prends pas la tête à explorer des chemins qui mènent nulle part. C’est une façon efficace de réduire l’espace de recherche et d’accélérer le process, parce que tu ne te concentres que sur les préfixes qui pourraient potentiellement donner des mots valides.

### Implémentation du Trie

Voici un aperçu de l'implémentation de cette approche :

```python
class TrieNode:
    def __init__(self, value:Optional[str]) -> None:
        self.value = value
        self.children = {}
        self.end = False



class Trie:
    def __init__(self):
        self.root = TrieNode(None)

    def insert(self, word:str):
        node = self.root
        for char in word:
            if char not in node.children:
                node.children[char] = TrieNode(char)
                node.children[char].parent = node
            node = node.children[char]
        node.end = True

    def resolve(self, chars:str) -> Set[str]:
        response = set()
        def search(node:TrieNode, begin:str, cs: List[str]):
            if node.end:
                response.add(begin)
            for i,c in enumerate(cs):
                if c in node.children:
                    new_cs = (cs[:i] + cs[i+1:])
                    search(node.children[c], begin+c, new_cs)

        search(self.root, '', list(chars.upper()))
        return response
```

Ça fonctionne !!! Fini le jeu !!!

Le temps libre, c’est ce petit espace précieux qu’on se crée au milieu de nos obligations et de notre quotidien. C’est le moment où l’on peut se déconnecter, faire ce qu’on aime vraiment : se plonger dans un bon bouquin, sortir avec des potes, ou même juste glander sur le canap' à binge-watcher une série. C’est un peu comme un souffle de liberté qui nous rappelle qu’on n’est pas que des robots programmés à bosser. En fait, ces moments de détente sont cruciaux pour recharger les batteries, stimuler la créativité et garder le moral. Alors, que ce soit une balade dans le parc ou une soirée jeux vidéo, il faut en profiter à fond !

### Trie vs. Approche naïve

Après avoir bricolé mes deux solutions, il était temps de voir laquelle se démerdait le mieux. Alors, je me suis dit : " Allez, je vais comparer la vitesse de mon algo à la bourrin avec celle du Trie. " Et voilà le verdict :

```python
t = Trie()
dico = set()

with open("gutenberg.txt", "r") as words:
    for word in words.readlines():
        dico.add(remove_accents(word.strip().upper()))
        t.insert(remove_accents(word.strip().upper()))

letters = 'chien'

time_trie = timeit.timeit(lambda: t.resolve(letters), number=1)
print(f'time_trie = {time_trie*1000 :.2f}ms')

time_naive = timeit.timeit(lambda: generate_words(letters, dico), number=1)
print(f'time_naive = {time_naive*1000 :.2f}ms')

if time_naive>time_trie:
    print(f'Trie est {time_naive/time_trie:.2f} fois plus rapide que la solution Naive pour les lettres "{letters}({len(letters)} lettres)"')
else:
    print(f'Naive est {time_trie/time_naive:.2f} fois plus rapide que la solution Trie pour les lettres "{letters}({len(letters)} lettres)"')
```

*gutenberg.txt* est un fichier qui contient environ 300k de mots francais et *remove_accents* est une fonction qui retire les accents. Je vous fais confiance, je sais que vous trouverez ça facilement ailleurs.

Pour les lettres C-I-H-N-E, voilà ce que ça donne :
``` 
time_trie = 0.13ms
time_naive = 0.15ms
Trie est 1.09 fois plus rapide que la solution Naive pour les lettres "chien"
```

QUOI ? C'EST TOUT ???? JE ME SUIS FAIS CHIER POUR RIEN !!!

Je me rend compte que pour l'usage que j'en avais, c'est à dire la résolution de mon *jeu de mot*, utiliser un algorithme sofistiqué tel que l'algorithme Trie, m'a juste fait perdre mon temps... Bon je vais fumer une clope pour me détendre...

5 minutes later...

Après une petite pause pour fumer une clope et laisser redescendre la pression, j'ai réalisé que même si l'amélioration n'était pas énorme pour un petit jeu de mots, j'avais quand même enrichi ma boîte à outils algorithmique.

Voyons maintenant avec plus de lettres :
```
time_trie = 0.17ms
time_naive = 0.66ms
Trie est 3.81 fois plus rapide que la solution Naive pour les lettres : "batman (6 lettres)"
```

```
time_trie*1000 = 0.99ms
time_naive*1000 = 59.21ms
Trie est 59.68 fois plus rapide que la solution Naive pour les lettres : "superman (8 lettres)"
```

```
time_trie = 1.02ms
time_naive = 434.61ms
Trie est 424.55 fois plus rapide que la solution Naive pour les lettres : "supernany (9 lettres)"
```

```
time_trie = 2.21ms
time_naive =4546.19ms
Trie est 2059.54 fois plus rapide que la solution Naive pour les lettres : "superwoman (10 lettres)"
```

```
time_trie = 4.59ms
time_naive =53396.14ms
Trie est 11635.96 fois plus rapide que la solution Naive pour les lettres : "spiderwoman (11 lettres)"
```
### Analyse des résultats

Ce que ces chiffres nous montrent, c'est qu'à partir de 6 lettres, le Trie devient clairement le boss. Pour des mots de plus de 10 lettres, l'approche naïve devient carrément injouable.

C’est vrai que pour mon petit jeu, le Trie peut sembler un peu trop complexe, mais imagine que je devais étendre mon solver pour une appli web utilisée par plein de gens : là, le Trie ferait la différence.

Bon, pour résumer, ce projet m'a carrément aidé à reprendre le contrôle sur ma vie. J'ai réussi à foutre à la porte mon addiction pour ce jeu à la con, Mots de lettres, et, en prime, j'ai regagné du temps que je pensais avoir perdu à jamais. Mais, comme d'hab’, quand on croit avoir réglé un souci, la vie nous balance une autre merde à la tronche. Alors que je pensais être le king, une nouvelle addiction a débarqué : la clope. Ouais, j'ai échangé une obsession pour une autre, mais au moins, cette fois, j'ai boosté mon arsenal algorithmique et appris à gérer mon temps un peu mieux. En gros, la bataille continue, et qui sait, ma prochaine mission pourrait être de trouver un moyen de fumer moins. C'est pas gagné, mais bon, ça fait partie du jeu, non ?
