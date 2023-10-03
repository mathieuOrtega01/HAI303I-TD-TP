## __Exercice 25 (TP)__
> On souhaite réécrire monhead de l’exercice 20 afin que cette commande devienne un filtre !
> 1. Quelle est la syntaxe de la commande head ?
> 2. Ecrire un programme monheadf.c permettant de gérer les appels suivants :  
> $ monheadf -c 12 monhead.c monhead2.c  
> $ monheadf -l10 monhead.c monhead2.c  
> $ echo 'Hello World' | monheadf -c5  
> $ cat monheadf.c | monheadf  

head [OPTION]... [FILE]... L'option principale pour spécifier le nombre de lignes ou d'octets à afficher est -n. Par exemple :
- Afficher les 10 premières lignes d'un fichier : head -n 10 fichier.txt  
- Afficher les 5 premiers octets d'un fichier : head -c 5 fichier.txt

__Code__
```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>

// Fonction pour lire les premiers maxChar caractères depuis un fichier
char *readFirstNCharacters(FILE *file, int maxChar)
{
    char *buffer = (char *)malloc((maxChar + 1) * sizeof(char)); // +1 pour le caractère nul
    if (buffer == NULL)
    {
        perror("Allocation mémoire échouée");
        exit(EXIT_FAILURE);
    }

    int c;
    int charCount = 0;

    // Lire les premiers maxChar caractères
    while ((c = fgetc(file)) != EOF && charCount < maxChar)
    {
        buffer[charCount] = (char)c;
        charCount++;
    }

    buffer[charCount] = '\0'; // Assurez-vous de terminer la chaîne

    return buffer;
}

// Fonction pour lire les premiers maxChar caractères depuis une chaîne
char *readFirstNCharactersFromString(char *inputString, int maxChar)
{
    char *buffer = (char *)malloc((maxChar + 1) * sizeof(char)); // +1 pour le caractère nul
    if (buffer == NULL)
    {
        perror("Allocation mémoire échouée");
        exit(EXIT_FAILURE);
    }

    int charCount = 0;
    const char *ptr = inputString;

    // Lire les premiers maxChar caractères depuis la chaîne
    while (*ptr != '\0' && charCount < maxChar)
    {
        buffer[charCount] = *ptr;
        ptr++;
        charCount++;
    }

    buffer[charCount] = '\0'; // Assurez-vous de terminer la chaîne

    return buffer;
}

// Fonction pour lire les premières maxLines lignes depuis un fichier
char **readFirstNLines(FILE *file, int maxLines)
{
    char **lines = (char **)malloc((maxLines + 1) * sizeof(char *)); // +1 pour NULL à la fin
    if (lines == NULL)
    {
        perror("Allocation mémoire échouée");
        exit(EXIT_FAILURE);
    }

    int lineCount = 0;
    char buffer[256]; // Suppose une limite de 255 caractères par ligne, ajustez selon vos besoins

    // Lire les premières maxLines lignes
    while (fgets(buffer, sizeof(buffer), file) != NULL && lineCount < maxLines)
    {
        size_t len = strlen(buffer);

        // Supprimer le caractère de nouvelle ligne s'il est présent
        if (len > 0 && buffer[len - 1] == '\n')
        {
            buffer[len - 1] = '\0';
        }

        // Allouer de la mémoire pour stocker la ligne et la copier
        lines[lineCount] = strdup(buffer);

        if (lines[lineCount] == NULL)
        {
            perror("Erreur lors de l'allocation mémoire");
            exit(EXIT_FAILURE);
        }

        lineCount++;
    }

    lines[lineCount] = NULL; // Marquer la fin du tableau avec NULL

    return lines;
}

// Fonction pour lire les premières maxLines lignes depuis une chaîne
char **readFirstNLinesFromString(const char *inputString, int maxLines)
{
    char **lines = (char **)malloc((maxLines + 1) * sizeof(char *)); // +1 pour NULL à la fin
    if (lines == NULL)
    {
        perror("Allocation mémoire échouée");
        exit(EXIT_FAILURE);
    }

    int lineCount = 0;
    const char *ptr = inputString;

    // Lire les premières maxLines lignes depuis la chaîne
    while (*ptr != '\0' && lineCount < maxLines)
    {
        size_t len = 0;
        while (*ptr != '\0' && *ptr != '\n')
        {
            len++;
            ptr++;
        }

        // Allouer de la mémoire pour stocker la ligne et la copier
        lines[lineCount] = (char *)malloc((len + 1) * sizeof(char));

        if (lines[lineCount] == NULL)
        {
            perror("Erreur lors de l'allocation mémoire");
            exit(EXIT_FAILURE);
        }

        strncpy(lines[lineCount], ptr - len, len);
        lines[lineCount][len] = '\0'; // Assurez-vous de terminer la chaîne

        if (*ptr == '\n')
        {
            ptr++; // Sauter le caractère de nouvelle ligne
        }

        lineCount++;
    }

    lines[lineCount] = NULL; // Marquer la fin du tableau avec NULL

    return lines;
}

// Fonction pour libérer la mémoire allouée pour les lignes
void freeLines(char **lines)
{
    for (int i = 0; lines[i] != NULL; i++)
    {
        free(lines[i]); // Libérer la mémoire allouée pour chaque ligne
    }
    free(lines); // Libérer la mémoire allouée pour le tableau de lignes
}

int main(int argc, char *argv[])
{
    char *arg = argv[1];

    if (!isatty(fileno(stdin)))
    {
        if (strncmp(arg, "-c", 2) == 0)
        {
            int maxOption;
            arg += 2; // Avance le pointeur pour sauter "-c"
            if (*arg == '\0')
            {
                maxOption = atoi(argv[2]);
            }
            else
            {
                char *endptr;
                long num = strtol(arg, &endptr, 10);

                if (*endptr == '\0')
                {
                    maxOption = (int)num;
                }
                else
                {
                    perror("L'argument n'est pas de la forme attendue");
                    return 1;
                }
            }

            char ligne[256]; // taille maximale de la ligne
            while (fgets(ligne, sizeof(ligne), stdin) != NULL)
            {
                size_t len = strlen(ligne);
                if (len > 0 && ligne[len - 1] == '\n')
                {
                    ligne[len - 1] = '\0';
                }

                FILE *file = fopen(ligne, "r");

                char *output;
                if (file == NULL)
                {
                    printf("==> %s <==\n", ligne);
                    output = readFirstNCharactersFromString(ligne, maxOption);
                    printf("%s\n", output);
                }
                else
                {
                    printf("==> %s <==\n", ligne);
                    output = readFirstNCharacters(file, maxOption);
                    printf("%s\n", output);
                }
            }
        }
        else if (strncmp(arg, "-l", 2) == 0)
        {
            int maxOption;
            arg += 2; // Avance le pointeur pour sauter "-l"
            if (*arg == '\0')
            {
                maxOption = atoi(argv[2]);
            }
            else
            {
                char *endptr;
                long num = strtol(arg, &endptr, 10);

                if (*endptr == '\0')
                {
                    maxOption = (int)num;
                }
                else
                {
                    perror("L'argument n'est pas de la forme attendue");
                    return 1;
                }
            }

            char ligne[256]; // taille maximale de la ligne
            while (fgets(ligne, sizeof(ligne), stdin) != NULL)
            {
                size_t len = strlen(ligne);
                if (len > 0 && ligne[len - 1] == '\n')
                {
                    ligne[len - 1] = '\0';
                }

                FILE *file = fopen(ligne, "r");

                char **output;
                if (file == NULL)
                {
                    printf("==> %s <==\n", ligne);
                    output = readFirstNLinesFromString(ligne, maxOption);
                    for (int i = 0; i < maxOption; i++)
                    {
                        printf("%s\n", output[i]);
                    }
                }
                else
                {
                    printf("==> %s <==\n", ligne);
                    output = readFirstNLines(file, maxOption);
                    for (int i = 0; i < maxOption; i++)
                    {
                        printf("%s\n", output[i]);
                    }
                }
            }
        }
        else
        {
            // Default
        }
    }
    else
    {
        if (strncmp(arg, "-c", 2) == 0)
        {
            int maxOption;
            arg += 2;

            if (*arg == '\0')
            {
                int maxOption = atoi(argv[2]);
            }
            else
            {
                printf("-cX \n");
                char *endptr;
                long num = strtol(arg, &endptr, 10);

                if (*endptr == '\0')
                {
                    maxOption = (int)num;
                }
                else
                {
                    perror("L'argument n'est pas de la forme attendue");
                    return 1;
                }
            }

            for (int i = 3; i < argc; i++)
            {
                FILE *file = fopen(argv[i], "r");

                char *output;
                if (file == NULL)
                {
                    printf("==> %s <==\n", argv[i]);
                    output = readFirstNCharactersFromString(argv[i], maxOption);
                    printf("%s\n", output);
                }
                else
                {
                    printf("==> %s <==\n", argv[i]);
                    output = readFirstNCharacters(file, maxOption);
                    printf("%s\n", output);
                }
            }
        }
        else if (strncmp(arg, "-l", 2) == 0)
        {
            int maxOption;
            arg += 2;

            if (*arg == '\0')
            {
                int maxOption = atoi(argv[2]);
            }
            else
            {
                char *endptr;
                long num = strtol(arg, &endptr, 10);

                if (*endptr == '\0')
                {
                    maxOption = (int)num;
                }
                else
                {
                    perror("L'argument n'est pas de la forme attendue");
                    return 1;
                }
            }

            for (int i = 3; i < argc; i++)
            {
                FILE *file = fopen(argv[i], "r");

                char **output;
                if (file == NULL)
                {
                    printf("==> %s <==\n", argv[i]);
                    output = readFirstNLinesFromString(argv[i], maxOption);
                    for (int i = 0; i < maxOption; i++)
                    {
                        printf("%s\n", output[i]);
                    }
                }
                else
                {
                    printf("==> %s <==\n", argv[i]);
                    output = readFirstNLines(file, maxOption);
                    for (int i = 0; i < maxOption; i++)
                    {
                        printf("%s\n", output[i]);
                    }
                }
            }
        }
        else
        {
            // Default
        }
    }

    return 0;
}

```
