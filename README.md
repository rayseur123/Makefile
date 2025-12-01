# Makefile
## C'est quoi un Makefile ?
Un **Makefile** est un fichier utilisé par le logiciel make. Son objectif est de créer un fichier (souvent un exécutable) en exécutant différentes commandes shell.
À la différence d’un script shell, **make** permet de lancer certaines commandes seulement si elles sont nécessaires, ce qui évite de tout refaire à chaque compilation.

Ici, on l’utilise pour simplifier la **compilation** de nos fonctions, librairies ou programmes en C / C++.

## C’est quoi un compilateur ?
Un compilateur est un programme permettant de convertir du code (C, C++, etc.) en un fichier utilisable par la machine.
Dans nos exemples, on utilise gcc, qui permet de compiler des fichiers C.

### Comment ça marche, la compilation ?
On va parler ici des quatre grandes étapes de la compilation.

#### Pre processor
Le Pre processor se charge de faire les remplacements textuels :
il enlève les **commentaires**, remplace les **#define** par leur contenu, et les **#include** seront aussi remplacés par ce qu’ils contiennent.

Cette étape peut être visible grace à l'option **-E**, ce qui permet de stoper la compilation à ce stade pour afficher dans le terminal l'état actuel de la compilation.
```
 gcc -E test.c
```

#### Compiling
Le compiling convertit notre code en langage **assembleur**, compréhensible par le microprocesseur.
On peut voir cette étape avec l’option **-S**, qui génère des fichiers **.s**.
```
gcc -S test.c
```

#### Assembling
L'assembling permet, comme son nom l’indique, de convertir le fichier **assembleur** en **langage machine**.
Cette étape produit des fichiers **.o**.
Elle est visible grâce à l’option **-c**.
```
gcc -c test.c
```

#### Linking

Le linking rassemble tous les fichiers **.o** pour former un **exécutable** final.

#### Exemple de compilation
<img width="1666" height="805" alt="image" src="https://github.com/user-attachments/assets/41d0605a-006c-4d88-9f4a-62bc653a9a4b" />


