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

## Makefile guide
### le règles
ne règle représente une commande, ou un ensemble de commandes shell, à exécuter.
Les règles constituent le **cœur** du Makefile : make les parcourt et les exécute pour construire le fichier final.

Elles sont composées de leur nom, suivi d’un **:**, puis d’une ou plusieurs lignes de commandes précédées d’une **tabulation**.
Une règle peut exécuter plusieurs lignes à la suite.
```
test:
 echo "test1"
 echo "test2"
```

Avec ce que nous avons vu sur la compilation, il est donc possible d’obtenir un exécutable très simplement :
```
make:
 gcc test.c
```

### Les variables
Les Makefiles, comme les langages de programmation, possèdent des variables.
Elles rendent le Makefile plus lisible et modulable.
Dès que possible, il faut mettre les valeurs réutilisables dans des variables.

**Exemple :** supposons que plusieurs fichiers sont nécessaires pour construire notre programme.
```
SRCS = test.c test2.c

make:
 gcc $(SRCS) ## synthaxe d'un appel de variable dans un makefile
```

// rajouter les nom standard de variable

### Les dépendances
Comme expliqué précédemment, make évite de reconstruire une cible si ce n’est pas nécessaire.
Une règle peut donc avoir des dépendances : la cible sera reconstruite uniquement si l’une de ses dépendances est plus récente qu’elle.
```
cible: dependances
 commande
```
Make remontera automatiquement dans les dépendances pour vérifier si elles aussi doivent être reconstruites.

### Les règles specifique
Certaines règles ont déjà une utilité ou une convention d’usage dans les Makefiles, que ce soit par habitude, par norme ou parce que make leur donne un comportement particulier.

#### all
C’est la règle de base d’un Makefile.
Elle est souvent la première appelée.
On lui indique en dépendance ce que l’on souhaite construire, en général la variable **$(NAME)**.
```
all: $(NAME)
```

#### clean
La règle **clean** permet de supprimer tous les fichiers temporaires qui ne servent plus une fois l’exécutable construit (en général les fichiers **.o** et **.d**).
```
clean:
 rm -f $(OBJ)
```

#### fclean
La règle **fclean** supprime l’ensemble des fichiers produits par le Makefile.
Elle appelle **clean**, puis supprime aussi l’**exécutable**.
```
fclean: clean
rm -f $(NAME)
```

#### re
La règle **re** permet de recompiler complètement le projet.
Elle appelle d’abord **fclean**, puis **all**.
```
re: fclean all
```

#### .PHONY
**.PHONY** n’est pas une règle.
Elle permet de spécifier que certaines cibles ne représentent pas de fichiers, ce qui évite que make les confonde avec des fichiers portant le même nom.
Sans **.PHONY**, si un fichier nommé “clean” existe, la règle **clean** ne s’exécuterait pas.
```
.PHONY: all clean fclean re
```
