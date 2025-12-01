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
Une règle représente une commande ou un ensemble de commandes shells à executer. Les règles représente le coeur même du makefile, qui parcourera les differentes règles les unes apres les autres pour construire le fichier final.

Elles sont constinuer de leur nom suivie d'un **:** qui trouvera son utiliser plus tard. Puis une tabulation et enfin la ligne a éxecuter. Une règle peux executer plusieurs lignes à la suite.
```
test:
 echo "test1"
 echo "test2"
```

Il est donc possible avec la compilation expliqué plus tot de faire ceci pour obtenir un executable.
```
make:
 gcc test.c
```

### Les variables
Les Makefiles, comme pour un language de programmation possede des variables. Celles ci nous permetant de rendre un makefile plus lisible / modulable. Il faut passer le contenue en variable des que possible.

Voici un exemple plus clair. Supposons que plusieurs fichiers sont necessaire pour construire notre programme.
```
SRCS = test.c test2.c

make:
 gcc $(SRCS) ## synthaxe d'un appel de variable dans un makefile
```

### Les dependances
// expliauer les dependances
### Les règles specifique
Certaine règles ont deja une utiliter dans le makefile, que ce soit par norme ou que le makefile possede des propriete facilitant leur usage. 

#### all
Cette règle represente la base du makefile, ce sera la premiere règle appeler. on lui attribue souvent le nom du fichier en **depedance** ici ce sera la variable **$(NAME)**.
```
all: $(NAME)
```

#### clean
La règle clean nous permet de supprimer tous les fichiers qui n'ont plus d'interet avec l'executable. En general les fichiers **.o** et **.d**.
```
clean:
 rm -f $(OBJ)
```

#### fclean
La règle fclean permet de supprimer l'essemble des fichiers produit par le makefile. Elle appelle donc la règle clean et supprimer aussi l'executable.
```
fclean: clean
rm -f $(NAME)
```

#### re
La règle **re** permet simplement de recompiler l'ensemble du projet en appelant la règle **fclean** puis la règle **all**.
```
re: fclean all
```

#### .PHONY
Ce n'est pas une règle mais permet de specifier les règles existance pour eviter d'etre confondu avec des fichiers.
Ce probleme est possible car si l'on specifi un fichier a make, alors celui ci verifira si la source a besoi d'etre mis a jour.
```
.PHONY: all clean fclean re
```
