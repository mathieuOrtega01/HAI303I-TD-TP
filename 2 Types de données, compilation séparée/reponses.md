## __Exercice 10 (TD/TP)__
> Soit une chaîne de caractères C contenant un nombre entier positif en représentation décimale. On veut écrire une fonction convertissant cette chaîne en un entier.
> 1. Ecrire un algorithme itératif de conversion.
> 2. Ecrire la fonction C correspondante.

__Algorithme itératif__
```
Fonction mon_atoi(string)
    number = 0
    power = 0
    
    Pour chaque caractère c dans la chaîne string (en commençant par la fin)
        newNumber = c - '0'
        Si newNumber est compris entre 0 et 9
            number = number + (newNumber * pow(10, power))
            power = power + 1
        Sinon
            Retourner number
        Fin Si
    Fin Pour
    
    Retourner number
Fin Fonction
```

__Code__
```c
#include <stdio.h>
#include <string.h>
#include <stdlib.h>

int mon_pow(int base, int exp)
    {
      if(exp < 0)
        return -1;

        int result = 1;
        while (exp)
        {
            if (exp & 1)
                result *= base;
            exp >>= 1;
            base *= base;
        }

        return result;
    }

int mon_atoi(char * string) 
{
    int number = 0;
    int power = 0;
    
    for (int s = strlen(string) - 1; s >= 0; s--)
    {
        int newNumber = string[s] - '0';
        if (newNumber > -1 && newNumber < 10) {
            number += newNumber * mon_pow(10, power);
            power++;
	    } else {
	        return number;
	    }
    }  
    
    return number;
}

int main(int argc, char *argv[]) 
{
    if (argc > 2) {
        printf("Vous devez saisir un argument\n");
    } else {
        printf("\"%s\" -> %d", argv[1], mon_atoi(argv[1]));
    }
}
```

## __Exercice 11 (TD/TP)__
> On veut réaliser la conversion inverse de l’exercice précédent. Soit un nombre entier positif que l’on veut convertir en une chaîne de caractères C contenant sa représentation décimale.
> 1. Ecrire un algorithme de conversion.
> 2. Ecrire la fonction C correspondante

__Algorithme__
```
Fonction int_to_char(number)
    Déclarer output de type chaîne de caractères
    
    count = count_digits(number) // Compter le nombre de chiffres dans le nombre
    Allouer de la mémoire pour la chaîne de caractères output de taille (count + 1)
    Placer le caractère nul '\0' à la fin de la chaîne output
    
    index = count - 1 // Initialiser l'index pour la conversion des chiffres
    
    Tant que number > 0
        Prendre le dernier chiffre de number en faisant number % 10
        Ajouter '0' à ce chiffre pour le convertir en caractère
        Stocker ce caractère dans output à l'indice index
        Diviser number par 10 pour supprimer le dernier chiffre
        Décrémenter l'index
    Fin Tant que
    
    Retourner output // Retourner la chaîne de caractères convertie
Fin Fonction
```

__Code__
```c
int mon_pow(int base, int exp)
    {
      if(exp < 0)
        return -1;

        int result = 1;
        while (exp)
        {
            if (exp & 1)
                result *= base;
            exp >>= 1;
            base *= base;
        }

        return result;
    }

int mon_atoi(char * string) 
{
    int number = 0;
    int power = 0;
    
    for (int s = strlen(string) - 1; s >= 0; s--)
    {
        int newNumber = string[s] - '0';
        if (newNumber > -1 && newNumber < 10) {
            number += newNumber * mon_pow(10, power);
            power++;
	    } else {
	        return number;
	    }
    }  
    
    return number;
}

int count_digits(int number)
{
    int count = 0;
        
    while (number > 0) {
        number /= 10;
        count++;
    }
    
    return count;
}

char * int_to_char(int number) 
{
    char *output;
    
    int count = count_digits(number);
	output = (char *) malloc( count+1 * sizeof(char));
    output[count] = '\0';
	int index = count-1;
	while (number > 0) {
        output[index] = (number % 10) + '0';
        number /= 10;
        index--;
    }
    
    return output;
}

int main(int argc, char *argv[]) 
{
    if (argc > 2) {
        printf("Vous devez saisir un argument\n");
    } else {
        int x = mon_atoi(argv[1]);
        
        printf("s=\"%s\" -> x=%d\n", argv[1], x);
        printf("On ajoute 10 à x et on affiche le résultat en string\n");
        
        x += 10;
        
        char * output = int_to_char(x);
        printf("Resultat: \"%s\"\n", output);
    }
}
```

## __Exercices 11__
> Ces algorithmes de conversion (entier vers chaîne, chaîne vers flottant, . . .) sont-ils fréquemment employés ?
Précisez à quelles occasions

Oui, les algorithmes de conversion entre différents types de données, tels que la conversion d'entier vers chaîne, de chaîne vers flottant, etc., sont très fréquemment employés en programmation. Ils sont utilisés dans de nombreuses applications et situations, notamment :

1. **Interaction avec les utilisateurs** : Lorsque vous demandez à l'utilisateur de saisir des données via une interface utilisateur (par exemple, un formulaire web), les données sont généralement saisies sous forme de chaînes de caractères. Vous devez souvent convertir ces données en types numériques pour les utiliser dans des calculs ou les stocker dans une base de données.

2. **Traitement de fichiers** : Lorsque vous lisez des données à partir de fichiers texte, les données sont généralement lues sous forme de chaînes de caractères. Vous devez effectuer des conversions pour les utiliser comme des nombres ou d'autres types de données.

3. **Communication réseau** : Lorsque vous communiquez des données sur un réseau, les données sont souvent transmises sous forme de chaînes de caractères. Les conversions sont nécessaires pour interpréter correctement ces données du côté du récepteur.

4. **Manipulation de données** : Dans le domaine de la manipulation de données, vous pouvez avoir besoin de convertir des données d'un type à un autre, par exemple, de convertir des valeurs numériques en chaînes de caractères pour générer des rapports ou des documents.

5. **Affichage de données** : Lorsque vous affichez des données à l'écran ou dans des rapports, vous devez souvent les convertir en une forme lisible par les humains. Par exemple, la conversion d'un nombre à virgule flottante en une chaîne de caractères avec un format spécifique.

6. **Calculs mathématiques** : Les calculs mathématiques peuvent nécessiter des conversions entre différents types de données, par exemple, en convertissant des entiers en flottants pour effectuer des opérations mathématiques précises.

7. **Manipulation de bases de données** : Lorsque vous interagissez avec des bases de données, les données peuvent être stockées dans des formats différents. Vous devez effectuer des conversions pour assurer la cohérence des données.

En résumé, les algorithmes de conversion sont essentiels dans la programmation pour permettre aux développeurs de travailler avec différentes représentations de données dans divers contextes d'application. Ils facilitent la manipulation, la communication et l'affichage de données dans un format adapté à l'objectif spécifique de chaque tâche.
