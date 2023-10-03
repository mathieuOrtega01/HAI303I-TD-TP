## __Exercice 17 (TD)__
> Quelles différences existent entre l’utilisation d’appels systèmes man 2 (open, read, close) ou bien des fonctions
de bibliothèque man 3 (fopen, fprintf, ...) ?

Les appels systèmes (man 2) et les fonctions de bibliothèque (man 3) sont deux méthodes différentes pour effectuer des opérations d'entrée/sortie (E/S) dans un programme en C (ou dans d'autres langages de programmation). Voici quelques-unes des principales différences entre les deux :

1. Niveau d'abstraction :
   - Les appels systèmes (man 2) sont des fonctions directes du système d'exploitation qui fournissent un accès bas niveau aux opérations E/S. Ils sont généralement plus proches du matériel et de la manière dont le système d'exploitation gère les ressources.
   - Les fonctions de bibliothèque (man 3) sont des fonctions de plus haut niveau qui encapsulent souvent les appels systèmes sous-jacents. Elles offrent une interface plus conviviale et abstraite pour effectuer des opérations E/S.

2. Portabilité :
   - Les fonctions de bibliothèque (man 3) sont généralement plus portables entre les systèmes d'exploitation. Cela signifie que vous pouvez écrire un code utilisant fopen, fprintf, etc., et il fonctionnera de manière similaire sur différentes plates-formes.
   - Les appels systèmes (man 2) peuvent varier d'un système d'exploitation à l'autre, ce qui rend le code moins portable. Vous devrez souvent utiliser des appels systèmes spécifiques à la plate-forme si vous utilisez cette approche.

3. Facilité d'utilisation :
   - Les fonctions de bibliothèque (man 3) sont généralement plus simples à utiliser et à comprendre pour les développeurs. Elles masquent souvent les détails complexes liés à la gestion des fichiers et des E/S.
   - Les appels systèmes (man 2) nécessitent une compréhension plus approfondie du système d'exploitation et des opérations E/S. Ils peuvent être plus puissants, mais aussi plus complexes.

4. Performance :
   - En général, les appels systèmes (man 2) ont tendance à être plus rapides que les fonctions de bibliothèque (man 3) car ils évitent les surcharges liées à l'abstraction supplémentaire des fonctions de bibliothèque. Cependant, cette différence de performance peut être négligeable pour de nombreuses applications.

5. Flexibilité :
   - Les appels systèmes (man 2) offrent une plus grande flexibilité pour des cas d'utilisation spécifiques qui nécessitent un contrôle précis sur les opérations E/S.
   - Les fonctions de bibliothèque (man 3) sont souvent plus faciles à utiliser pour des tâches courantes, comme la lecture et l'écriture de fichiers, sans avoir à se soucier des détails de bas niveau.

En résumé, le choix entre les appels systèmes (man 2) et les fonctions de bibliothèque (man 3) dépendra de vos besoins spécifiques en matière de développement. Si vous recherchez la simplicité, la portabilité et une abstraction plus élevée, les fonctions de bibliothèque sont généralement préférables. Si vous avez besoin de performances maximales ou de contrôle précis sur les opérations E/S, les appels systèmes peuvent être la meilleure option.

## __Exercice 18 (TD/TP)__
> On souhaite compter les caractères d’un fichier passé en argument à la ligne de commande en utilisant des
appels systèmes (wc -c).
> 1. Ecrire un algorithme
> 2. Ecrire le programme C correspondant.

__Algorithme__
```
Fonction main(argc, argv)
    Si argc > 2 Alors
        Afficher "Vous devez saisir un argument"
    Sinon
        nom_fichier <- argv[1]
        Déclarer une chaîne de caractères commande
        Formatter commande avec "wc " + nom_fichier
        resultat <- Appeler la fonction système avec la commande
        Si resultat est égal à -1 Alors
            Afficher "Erreur lors de l'exécution de la commande wc"
            Quitter avec un code d'échec
        Fin Si
    Fin Si
Fin Fonction
``` 

__Code__
```c
#include <stdio.h>
#include <stdlib.h>

int main(int argc, char *argv[]) {
	if (argc > 2) {
        	printf("Vous devez saisir un argument\n");
    	} else {
    		const char *nom_fichier = argv[1];

    		// Utilisation de la fonction system pour appeler wc
    		char commande[100]; // Chaîne pour stocker la commande
    		snprintf(commande, sizeof(commande), "wc %s", nom_fichier);

    		int resultat = system(commande);

    		if (resultat == -1) {
        		perror("Erreur lors de l'exécution de la commande wc");
        		exit(EXIT_FAILURE);
   		 }
    	}
}
```

## __Exercice 19 (TD/TP)__
> On souhaite refaire l’exercice précédent en utilisant des fonctions de bibliothèque. Que faut-il faire ? Faites-le

```c
#include <stdio.h>
#include <stdlib.h>

int main(int argc, char *argv[]) {
	if (argc > 2) {
        	printf("Vous devez saisir un argument\n");
    	} else {
    		char *nom_fichier = argv[1];

    		FILE *f;
    		f=fopen(nom_fichier, "r");
    		
    		char c;
    		int lineCount = 0;
    		int wordCount = 0;
    		int characterCount = 0;
    		
    		while((c=fgetc(f))!=EOF){
    			if (c == '\n') {
    				lineCount++;
    			} else if (c == ' ') {
    				wordCount++;
    			}
    			
        		characterCount++;
    		}
    		
    		fclose(f);
    		
    		printf("%d %d %d %s\n", lineCount, wordCount, characterCount, argv[1]);
    	}
}
```

## __Exercice 20 (TD/TP)__
> On souhaite écrire un programme monhead qui ne rejette que les n premières lignes d’un fichier. Par exemple :
head -3 monfic.txt
Cette commande permet d’afficher les 3 premières lignes du fichier monfic.txt.
> 1. Qu’est-ce qu’une ligne ? Faut-il utiliser appel système ou fonction de bibliothèque ?
> 2. Ecrire un algorithme
> 3. Ecrire le programme C correspondant.
> 4. l’option -c43 de head permet de lire les 43 premiers caractères. Implémenter cette option

le char '\n' délimite la ligne et sert de point d'arrêt pour une ligne

__Algorithme__
```
Fonction main(argc, argv)
    Si argc n'est pas égal à 3 Alors
        Afficher un message d'erreur expliquant l'usage correct du programme
        Retourner EXIT_FAILURE
    Fin Si

    argument1 <- argv[1]
    nom_fichier <- argv[2]

    f <- Ouvrir le fichier nom_fichier en mode lecture
    Si f est égal à NULL Alors
        Afficher un message d'erreur indiquant l'échec de l'ouverture du fichier
        Retourner EXIT_FAILURE
    Fin Si

    lineCount <- 0
    wordCount <- 0
    characterCount <- 0

    Si le premier caractère de argument1 est '-' Alors
        Si le deuxième caractère de argument1 est 'c' Alors
            maxCharacter <- 0
            index <- 2
            Tant que argument1[index] n'est pas '\0' Faire
                Si argument1[index] est un chiffre Alors
                    Mettre à jour maxCharacter en convertissant argument1[index] en entier
                Sinon
                    Afficher un message d'erreur indiquant un argument non valide
                    Fermer le fichier f
                    Retourner EXIT_FAILURE
                Fin Si
                Incrémenter index de 1
            Fin Tant Que

            Tant que (lire le caractère c à partir du fichier f n'est pas EOF) ET (characterCount n'est pas égal à maxCharacter) Faire
                Si c est un saut de ligne Alors
                    Incrémenter lineCount de 1
                Sinon Si c est un espace Alors
                    Incrémenter wordCount de 1
                Fin Si
                Incrémenter characterCount de 1
                Afficher le caractère c
            Fin Tant Que

            Afficher un saut de ligne
        Sinon
            maxLine <- Convertir argument1 en entier et inverser son signe
            Tant que (lire le caractère c à partir du fichier f n'est pas EOF) ET (lineCount n'est pas égal à maxLine) Faire
                Si c est un saut de ligne Alors
                    Incrémenter lineCount de 1
                Sinon Si c est un espace Alors
                    Incrémenter wordCount de 1
                Fin Si
                Incrémenter characterCount de 1
                Afficher le caractère c
            Fin Tant Que
        Fin Si
    Sinon
        Afficher un message d'erreur indiquant un argument non valide
        Fermer le fichier f
        Retourner EXIT_FAILURE
    Fin Si

    Fermer le fichier f
    Retourner EXIT_SUCCESS
Fin Fonction
```
__Code__
```c
#include <stdio.h>
#include <stdlib.h>

int main(int argc, char *argv[]) {
    if (argc != 3) {
        fprintf(stderr, "Usage: %s <-x | -cx> <nom_fichier>\n", argv[0]);
	fprintf(stderr, "   -x  : Lire le nombre de lignes du fichier (où x est un nombre)\n");
	fprintf(stderr, "   -cx : Lire le nombre de caractères du fichier (où x est un nombre)\n");
        return EXIT_FAILURE;
    }

    char *argument1 = argv[1];
    char *nom_fichier = argv[2];

    FILE *f = fopen(nom_fichier, "r");
    if (f == NULL) {
        perror("Erreur lors de l'ouverture du fichier");
        return EXIT_FAILURE;
    }

    char c;
    int lineCount = 0;
    int wordCount = 0;
    int characterCount = 0;

    if (argument1[0] == '-') {
        if (argument1[1] == 'c') {
            int maxCharacter = 0;
            int index = 2;

            while (argument1[index] != '\0') {
                if (argument1[index] >= '0' && argument1[index] <= '9') {
                    maxCharacter = maxCharacter * 10 + (argument1[index] - '0');
                } else {
                    fprintf(stderr, "Argument non valide : %s\n", argument1);
                    fclose(f);
                    return EXIT_FAILURE;
                }

                index++;
            }

            while ((c = fgetc(f)) != EOF && characterCount != maxCharacter) {
                if (c == '\n') {
                    lineCount++;
                } else if (c == ' ') {
                    wordCount++;
                }

                characterCount++;
                printf("%c", c);
            }

            printf("\n");
        } else {
            int maxLine = atoi(argument1) * -1;

            while ((c = fgetc(f)) != EOF && lineCount != maxLine) {
                if (c == '\n') {
                    lineCount++;
                } else if (c == ' ') {
                    wordCount++;
                }

                characterCount++;
                printf("%c", c);
            }
        }
    } else {
        fprintf(stderr, "Argument non valide : %s\n", argument1);
        fclose(f);
        return EXIT_FAILURE;
    }

    fclose(f);
    return EXIT_SUCCESS;
}
```
