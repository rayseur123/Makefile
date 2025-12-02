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
A savoir qu'un compilateur peut reprendre la compilation si on lui fournit les fichiers nécessaires.
```
gcc test.o test2.o
```

#### Pre processor
Le Pre processor se charge de faire les remplacements textuels :
il enlève les **commentaires**, remplace les **#define** par leur contenu, et les **#include** seront aussi remplacés par ce qu’ils contiennent.

Cette étape peut être visible grace à l'option **-E**, ce qui permet de stopper la compilation à ce stade pour afficher dans le terminal l'état actuel de la compilation.
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
Cette étape sera alors utile dans le Makefile pour eviter de recompiler l'ensemble des fichiers il recompilera seulement les .o concernés.

#### -o
Une option utile à connaître est **-o**, qui permet de prendre le contenu du fichier à sa gauche et de le stocker dans le fichier de droite.
Cela nous permet donc de choisir le nom de notre exécutable.
```
gcc test.c -o test # test sera alors l'executable
```

#### -I
-I permet d'indiquer à gcc où trouver les headers, ce qui permet d'éviter de spécifier leur chemins dans les includes.
```
gcc -Iinclude test.c -o test
```

#### -L / -l
- -L permet d'indiquer le repertoir ou se trouve les library.
- -l permet d'indiquer quel sont les library à utiliser.
Il faut donner à -l un fichier qui commence par **lib**.
```
gcc test.c -L libs -lft # compile avec la library libft dans le dossier libs
``` 

#### Linking

Le linking rassemble tous les fichiers **.o** pour former un **exécutable** final.

#### Exemple de compilation
<img width="1666" height="805" alt="image" src="https://github.com/user-attachments/assets/41d0605a-006c-4d88-9f4a-62bc653a9a4b" />

## Makefile guide
### le règles
Une règle représente une commande, ou un ensemble de commandes shell, à exécuter.
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

#### Les variables specifique

- NAME : Représente le nom cible de l'executable ainsi que sa règle pour la construire.
- SRCS : Représente l'ensemble des fichiers sources
- OBJS : Représente l'ensemble des .o
- CC : Représente le compilateur utilisé
- FLAGS : Représente les flags utilisés

#### les variables: = vs :=
- **=** à chaque utilisation de la variable, sa valeur est réévaluée, ce qui permet de tenir compte d’éventuelles modifications d’autres variables.
- **:=** la valeur est fixée une seule fois au moment de la définition.

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

#### %.o: %.c
Cette règle permet de construire n'importe quel **.o** via son équivalent **.c**
```
%.o: %.c
	$(CC) $(FLAGS) -c $< -o $@
```
### Les variables automatiques
Les variables automatiques sont des variables permetant de rendre le makefile plus modulable.

- **$@** Représente le nom de la règle
- **$<** Représente la premiere dépendance
- **$^** Représente l'ensemble des dépendances

```
test.o: test.c
	$(CC) $(FLAGS) -c $< -o $@ 
```
- **$<** représente test.c -
- **$@** représente test.o.

## Makefile de base
```
NAME = test
SRCS = test1.c test2.c
OBJS = $(SRCS:.c=.o)
CC = gcc
FLAGS = -Wall -Werror -Wextra

all: $(NAME)

$(NAME): $(OBJS)
	$(CC) $(OBJS) -o $(NAME)

%.o: %.c
	$(CC) $(FLAGS) -c $< -o $@

clean:
	rm -f $(OBJS)

fclean: clean
	rm -f $(NAME)

.PHONY: all clean fclean
```

## Les fichiers .d
Les fichiers **.d** permettent de gérer automatiquement les dépendances liées aux fichiers **.h**.
Un fichier **.d** associé à un fichier **.o** permet de vérifier si tous les fichiers **.h** inclus sont plus anciens que le **.o**, et donc si une recompilation est nécessaire.

Pour les utiliser, il suffit d’ajouter les options **-MMD -MP** aux flags de compilation, puis d’**inclure** tous les fichiers **.d**.
### Exemple:
```
DEPS := $(test.o=.d)

%.d: %.o
	
CPPFLAGS = -MMD -MP
-include $(DEPS)
```
La variable **DEPS** contient l’ensemble des fichiers **.o** convertis en fichiers **.d**.
Les options **-MMD -MP** demandent au compilateur de générer automatiquement les dépendances dans ces fichiers **.d**.
L’instruction -include permet d’ajouter le contenu des fichiers **.d** dans le Makefile, sans provoquer d’erreur s’ils n’existent pas encore.

## Les fonction
Make donne accès à un bon nombre de fonctions utiles pour améliorer votre Makefile.
Voici un résumé de celles que j’utilise le plus.

### dir / notdir
Elles permettent respectivement de récupérer le répertoire et le nom du fichier à partir d’un chemin.
```
$(dir src/main.c) # src/
$(notdir src/main.c) # main.c
```

### addprefix
Elle permet de concaténer un préfixe avec un ou plusieurs éléments.
```
$(addprefix src/,file.c file2.c) # src/file.c src/file2.c
```

### addsufix
Elle permet de concaténer un suffixe avec un ou plusieurs éléments.
```
$(addsuffix .o,file file2) # src/file.o src/file2.o
```

### if
La fonction if permet d’évaluer une condition et de choisir une valeur si la condition est vraie ou non.
```
SRCS := $(if $(mod),$(SRCS_BONUS),$(SRCS))
```

## Expressions conditionel
Les Makefiles permettent d’utiliser des directives conditionnelles pour exécuter un bloc d’instructions selon certaines conditions.
Ces directives peuvent encadrer plusieurs lignes d’assignations ou de commandes.
Exemple:
```
ifeq ($(debug),1)
    $(info Mode debug activé)
    FLAGS := -g -O0
endif

```

# Makefile complet
```
NAME := miniRT

# ---------------------------------------------------------#
#                       DIRECTORIES                        #
# ---------------------------------------------------------#

SRCS_DIR := srcs/

# ---------------------------------------------------------#
#                       SOURCE FILES                       #
# ---------------------------------------------------------#

TEST_SRCS := test.c \
		test2.c \

MAIN_SRCS := main.c \

# ---------------------------------------------------------#
#                       BUILD SOURCE                       #
# ---------------------------------------------------------#

SRCS =	$(addprefix $(SRCS_DIR), $(TEST_SRCS)) \
		$(addprefix $(SRCS_DIR), $(MAIN_SRCS)) \


# ---------------------------------------------------------#
#                           LIBS                           #
# ---------------------------------------------------------#

LIBS_DIR := libs/

LIBS_TARGET :=  libft/libft.a \
				libtest/libtest.a  \

LIBS_NAMES := $(patsubst %.a,%,$(notdir $(LIBS_TARGET)))

LIBS_INCLUDES := $(addprefix $(LIBS_DIR), $(addsuffix /include,$(LIBS_NAMES))) # si pas de /include ajouter manuellement

CPPFLAGS += $(addprefix -I, $(LIBS_INCLUDES))

SYS_LIBS = m X11 Xext
SYS_LIBS := $(addprefix -l, $(SYS_LIBS))

# ---------------------------------------------------------#
#                           OBJS                           #
# ---------------------------------------------------------#

BUILD_DIR := .build/

OBJS_DIR := $(addprefix $(BUILD_DIR), objs/)
OBJS := $(addprefix $(OBJS_DIR), $(SRCS:.c=.o))

# ---------------------------------------------------------#
#                           DEPS                           #
# ---------------------------------------------------------#

DEPS := $(OBJS:.o=.d)

CPPFLAGS += -MMD -MP

# ---------------------------------------------------------#
#                   CONFIG COMPILATION                     #
# ---------------------------------------------------------#

CFLAGS += -Wall -Wextra -Werror

CC = cc

# ---------------------------------------------------------#
#                           MOD                            #
# ---------------------------------------------------------#

ifeq($(MOD), debug)
	CFLAGS += -g3
endif

# ---------------------------------------------------------#
#                          RULES                           #
# ---------------------------------------------------------#

all: $(NAME)

$(NAME): $(LIBS_TARGET) $(OBJS)
	$(CC) $^ -o $@ $(SYS_LIBS)

$(OBJS_DIR)%.o: $(SRCS_DIR)%.c
	@mkdir -p $(dir $@)
	$(CC) $(CFLAGS) $(CPPFLAGS) -o $@ -c $<

$(LIBS_TARGET): force
	$(MAKE) -C $(dir $@)

$(force):

# ---------------------------------------------------------#
#                       CLEAN                              #
# ---------------------------------------------------------#

clean:
	rm -rf $(BUILD_DIR)
	$(foreach lib,$(LIBS_TARGET),$(MAKE) -C $(dir $(lib)) clean;)

	

fclean: clean
	rm -rf $(NAME)
	$(foreach lib,$(LIBS_TARGET),$(MAKE) -C $(dir $(lib)) fclean;)



re:
	$(MAKE) fclean
	$(MAKE) all

.PHONY : clean fclean all re force

-include $(DEPS)

```
