## __Exercice 1 (TD)__
> Quelle différence existe-t-il entre un interpréteur et un compilateur ? Citez deux exemples pour chacun d’entre eux.

La différence principale entre un interpréteur et un compilateur réside dans la façon dont ils traitent le code source d'un programme.

Un interpréteur exécute le code source ligne par ligne, en le traduisant en code machine ou bytecode à mesure qu'il est exécuté. Cela signifie que le code source est lu et interprété à chaque fois que le programme est exécuté. Les erreurs sont généralement détectées au fur et à mesure de l'exécution.

Deux exemples d'interpréteurs sont :
1. Python (utilise un interpréteur Python pour exécuter des scripts Python).
2. Ruby (utilise un interpréteur Ruby pour exécuter des scripts Ruby).

Un compilateur, en revanche, traduit tout le code source en code machine ou en code objet en une seule étape. Le code compilé est généralement plus rapide à exécuter car il n'a pas besoin d'être interprété à chaque fois. Les erreurs de compilation sont détectées avant l'exécution.

Deux exemples de compilateurs sont :
1. GCC (GNU Compiler Collection, un compilateur C/C++).
2. javac (le compilateur Java).

## __Exercice 2 (TD)__
> Quelles similitudes et quelles différences existe-t-il entre un fichier d’en-tête (toto.h) et une bibliothèque ? Quand les utilise-t-on dans le processus de compilation ? Citez deux exemples pour chacun d’entre eux.

Un fichier d'en-tête (par exemple, toto.h) et une bibliothèque sont tous deux utilisés pour organiser et réutiliser du code dans des programmes C/C++. Cependant, ils ont des objectifs et des utilisations légèrement différents.

Similitudes :
- Les deux sont utilisés pour regrouper des fonctions, des définitions de structures et des déclarations de variables afin de les rendre accessibles à d'autres parties du code.
- Ils peuvent être inclus dans un programme à l'aide de directives préprocesseur, telles que `#include`.

Différences :
- Un fichier d'en-tête (toto.h) contient principalement des déclarations de fonctions, de structures et de variables externes, mais il ne contient généralement pas de code exécutable. Il est inclus dans le code source pour permettre la compilation de celui-ci sans définir toutes les fonctions ou structures à cet endroit.
- Une bibliothèque est un ensemble de fonctions précompilées et prêtes à être utilisées. Elle peut contenir des fichiers objets ou des fichiers partagés (DLL ou SO) qui sont liés avec le code source lors de la compilation ou de l'édition des liens.

Exemples de fichiers d'en-tête :
1. `stdio.h` - Contient des déclarations de fonctions standard pour les opérations d'entrée/sortie en C.
2. `math.h` - Contient des déclarations de fonctions mathématiques en C.

Exemples de bibliothèques :
1. `libc.so` - La bibliothèque C standard contenant des fonctions de base comme `printf` et `malloc`.
2. `libsqlite3.a` - Une bibliothèque pour la gestion de bases de données SQLite.

## __Exercice 3 (TD)__
> Quelle différence entre l’inclusion d’un fichier d’entête standard tel que stdio.h, stdlib.h, ctype.h et un fichier d’entête personnel monprog.h ?

La principale différence entre l'inclusion d'un fichier d'en-tête standard tel que `stdio.h`, `stdlib.h`, `ctype.h`, et un fichier d'en-tête personnel tel que `monprog.h` réside dans leur contenu et leur objectif.
Comment les paramètres de la ligne de commande sont-ils passés à un programme C ? Comment l’environnement
du processus (variables d’environnement telles que PATH, HOME, ...) est-il atteignable par un programme C 
Les fichiers d'en-tête standard, tels que `stdio.h`, sont fournis avec le compilateur et contiennent des déclarations pour des fonctions, des constantes et des types de données standard nécessaires à la programmation en C. Ils sont destinés à être inclus dans de nombreux programmes différents et contiennent des déclarations pour des fonctions couramment utilisées telles que `printf`, `malloc`, `fopen`, etc.

En revanche, un fichier d'en-tête personnel comme `monprog.h` est créé par le développeur pour inclure des déclarations spécifiques à son programme. Il contient généralement des déclarations de fonctions, de structures et de variables spécifiques à un projet particulier. Ces fichiers sont utilisés pour organiser et partager du code entre différentes parties d'un programme ou entre différents fichiers source d'un projet.

## __Exercice 4 (TD)__
Comment les paramètres de la ligne de commande sont-ils passés à un programme C ? Comment l’environnement
du processus (variables d’environnement telles que PATH, HOME, ...) est-il atteignable par un programme C
Les paramètres de la ligne de commande sont passés à un programme C via les arguments de la fonction `main`. La fonction `main` a la signature suivante :

```c
int main(int argc, char *argv[])
```

- `argc` (argument count) est un entier qui représente le nombre total d'arguments passés, y compris le nom du programme lui-même.
- `argv` (argument vector) est un tableau de chaînes de caractères (tableau de pointeurs de caractères) où chaque élément représente un argument passé, y compris le nom du programme en `argv[0]`.

Par exemple, si vous exécutez un programme C avec la commande suivante :
```
./mon_programme arg1 arg2 arg3
```

Alors `argc` sera égal à 4 (car il y a quatre arguments) et `argv` contiendra :
- `argv[0]` : "./mon_programme"
- `argv[1]` : "arg1"
- `argv[2]` : "arg2"
- `argv[3]` : "arg3"

Pour accéder aux variables d'environnement, tels que PATH ou HOME, vous pouvez utiliser la fonction `getenv` de la bibliothèque `stdlib.h`. Par exemple :

```c
#include <stdio.h>
#include <stdlib.h>

int main() {
    char *path = getenv("PATH");
    if (path != NULL) {
        printf("PATH = %s\n", path);
    } else {
        printf("PATH not found in environment.\n");
    }
    return 0;
}
```

La fonction `getenv` permet de récupérer la valeur d'une variable d'environnement spécifique en fonction de son nom.

## __Exercice 5 (TD)__
> On souhaite écrire un programme affichant le nombre de paramètres passés à la ligne de commande, ces paramètres, ainsi que les variables d’environnement.
> 1. Ecrire l’algorithme de ce programme.
> 2. Ecrire le programme C correspondant

1. Algorithme du programme :
```
Début
    Inclure les fichiers d'en-tête (stdio.h, stdlib.h)
    
    Déclarer une variable argc de type entier
    Déclarer un tableau argv de pointeurs de caractères
    
    // Récupérer le nombre d'arguments passés à la ligne de commande
    argc ← Nombre d'arguments passés à la ligne de commande
    
    // Afficher le nombre total d'arguments
    Afficher "Nombre de paramètres passés : ", argc - 1
    
    // Afficher les paramètres passés à la ligne de commande
    Pour i allant de 1 à argc - 1 faire
        Afficher i, ": ", argv[i]
    Fin Pour
    
    // Afficher les variables d'environnement
    Afficher "Variables d'environnement :"
    Pour chaque variable d'environnement faire
        Afficher variable
    Fin Pour
    
Fin
```

2. Programme C correspondant :

```c
#include <stdio.h>
#include <stdlib.h>

int main(int argc, char *argv[]) {
    // Afficher le nombre de paramètres passés à la ligne de commande
    printf("Nombre de paramètres passés : %d\n", argc - 1);

    // Afficher les paramètres passés à la ligne de commande
    printf("Paramètres passés :\n");
    for (int i = 1; i < argc; i++) {
        printf("%d: %s\n", i, argv[i]);
    }

    // Afficher les variables d'environnement
    printf("Variables d'environnement :\n");
    extern char **environ;
    for (int i = 0; environ[i] != NULL; i++) {
        printf("%s\n", environ[i]);
    }

    return 0;
}
```

## __Exercice 6 (TD)__
> A l’aide du programme précédent, testez les possibilités du compilateur gcc en produisant :
> — le fichier prétraité ;
> — le fichier assembleur ;
> — le fichier objet ;
> Observez ces différents fichiers.

Pour observer les différents fichiers générés par le compilateur GCC à partir du programme précédent, vous pouvez utiliser les options de compilation suivantes :

- Pour obtenir le fichier prétraité (avec l'extension `.i`), utilisez l'option `-E` :

```bash
gcc -E nom_du_programme.c -o nom_du_programme.i
```

- Pour obtenir le fichier assembleur (avec l'extension `.s`), utilisez l'option `-S` :

```bash
gcc -S nom_du_programme.c -o nom_du_programme.s
```

- Pour obtenir le fichier objet (avec l'extension `.o`), utilisez l'option `-c` :

```bash
gcc -c nom_du_programme.c -o nom_du_programme.o
```

Vous pouvez observer le contenu de ces fichiers générés en ouvrant chaque fichier avec un éditeur de texte ou en utilisant des commandes telles que `cat` ou `less` dans un terminal.

- Le fichier prétraité contient le code source après le prétraitement, c'est-à-dire après l'inclusion des fichiers d'en-tête et le remplacement des macros.
- Le fichier assembleur contient le code en langage d'assemblage correspondant au code source C.
- Le fichier objet est une représentation binaire du code source C prêt à être lié avec d'autres fichiers objets pour créer un exécutable final.
