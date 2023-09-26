## __Exercice 10 (TD)__
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
