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

## __Exercice 26 (TP)__
> On souhaite écrire un filtre montail qui ne rejette que les n dernières lignes d’un fichier avec la syntaxe suivante : tail [-n number] [file]
> 1. Quelle structure de données nécessite ce programme ? Décrivez précisément cette structure de données !
> 2. Ecrire un algorithme
> 3. Ecrire le programme C correspondant.

Pour implémenter un filtre montail qui ne rejette que les n dernières lignes d'un fichier, vous pouvez utiliser une structure de file (file d'attente) pour stocker les lignes lues du fichier. Une file est une structure de données de type FIFO (First-In-First-Out), ce qui signifie que les éléments sont ajoutés à la fin et retirés du début. Chaque élément de la file peut être une chaîne de caractères représentant une ligne du fichier.

__Algorithme__
```
PROCEDURE initQueue(queue: LineQueue)
    queue.front <- -1
    queue.rear <- -1
END PROCEDURE

PROCEDURE enqueue(queue: LineQueue, line: STRING)
    IF (queue.rear + 1) MOD MAX_LINES = queue.front THEN
        // La file est pleine, défilez l'élément le plus ancien
        FREE(queue.lines[queue.front])
        queue.front <- (queue.front + 1) MOD MAX_LINES
    END IF

    // Ajoutez la nouvelle ligne à la file
    queue.rear <- (queue.rear + 1) MOD MAX_LINES
    queue.lines[queue.rear] <- strdup(line)
END PROCEDURE

PROCEDURE printQueue(queue: LineQueue)
    i <- queue.front
    WHILE i ≠ queue.rear DO
        OUTPUT(queue.lines[i])
        i <- (i + 1) MOD MAX_LINES
    END WHILE
    OUTPUT(queue.lines[i])
END PROCEDURE

PROCEDURE freeQueue(queue: LineQueue)
    i <- queue.front
    WHILE i ≠ queue.rear DO
        FREE(queue.lines[i])
        i <- (i + 1) MOD MAX_LINES
    END WHILE
    FREE(queue.lines[i])
END PROCEDURE

PROCEDURE main(argc: INTEGER, argv: ARRAY OF STRING)
    IF argc < 2 THEN
        OUTPUT("Usage: ", argv[0], " [-n number] [file]")
        EXIT(EXIT_FAILURE)
    END IF

    num <- 10 // Valeur par défaut
    filename <- NULL

    // Analyse des arguments
    FOR i FROM 1 TO argc - 1 DO
        IF strcmp(argv[i], "-n") = 0 THEN
            IF i + 1 < argc THEN
                num <- atoi(argv[i + 1])
                IF num <= 0 THEN
                    OUTPUT("Le nombre doit être un entier positif.")
                    EXIT(EXIT_FAILURE)
                END IF
                i <- i + 1 // Passer à l'argument suivant
            ELSE
                OUTPUT("Option -n requiert un argument.")
                EXIT(EXIT_FAILURE)
            END IF
        ELSE
            filename <- argv[i]
        END IF
    END FOR

    IF filename = NULL THEN
        OUTPUT("Aucun fichier spécifié.")
        EXIT(EXIT_FAILURE)
    END IF

    file <- fopen(filename, "r")
    IF file = NULL THEN
        PERROR("Erreur lors de l'ouverture du fichier")
        EXIT(EXIT_FAILURE)
    END IF

    DECLARE queue: LineQueue
    initQueue(queue)

    DECLARE buffer[256]: STRING // Suppose une limite de 255 caractères par ligne, ajustez selon vos besoins

    // Lire les lignes du fichier et les ajouter à la file
    WHILE fgets(buffer, sizeof(buffer), file) ≠ NULL DO
        enqueue(queue, buffer)
    END WHILE

    // Imprimer les n dernières lignes
    printQueue(queue)

    // Libérer la mémoire utilisée par la file
    freeQueue(queue)

    fclose(file)

    EXIT(EXIT_SUCCESS)
END PROCEDURE
```
__Code__
```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define MAX_LINES 1000

// Structure de file
typedef struct {
    char *lines[MAX_LINES];
    int front, rear;
} LineQueue;

// Initialiser la file
void initQueue(LineQueue *queue) {
    queue->front = queue->rear = -1;
}

// Ajouter une ligne à la file
void enqueue(LineQueue *queue, const char *line) {
    if ((queue->rear + 1) % MAX_LINES == queue->front) {
        // La file est pleine, défilez l'élément le plus ancien
        free(queue->lines[queue->front]);
        queue->front = (queue->front + 1) % MAX_LINES;
    }

    // Ajoutez la nouvelle ligne à la file
    queue->rear = (queue->rear + 1) % MAX_LINES;
    queue->lines[queue->rear] = strdup(line);
}

// Imprimer les lignes dans la file
void printQueue(LineQueue *queue) {
    int i = queue->front;
    while (i != queue->rear) {
        printf("%s\n", queue->lines[i]);
        i = (i + 1) % MAX_LINES;
    }
    printf("%s\n", queue->lines[i]);
}

// Libérer la mémoire utilisée par la file
void freeQueue(LineQueue *queue) {
    int i = queue->front;
    while (i != queue->rear) {
        free(queue->lines[i]);
        i = (i + 1) % MAX_LINES;
    }
    free(queue->lines[i]);
}

int main(int argc, char *argv[]) {
    if (argc < 2) {
        fprintf(stderr, "Usage: %s [-n number] [file]\n", argv[0]);
        return EXIT_FAILURE;
    }

    int num = 10; // Valeur par défaut
    const char *filename = NULL;

    // Analyse des arguments
    for (int i = 1; i < argc; i++) {
        if (strcmp(argv[i], "-n") == 0) {
            if (i + 1 < argc) {
                num = atoi(argv[i + 1]);
                if (num <= 0) {
                    fprintf(stderr, "Le nombre doit être un entier positif.\n");
                    return EXIT_FAILURE;
                }
                i++; // Passer à l'argument suivant
            } else {
                fprintf(stderr, "Option -n requiert un argument.\n");
                return EXIT_FAILURE;
            }
        } else {
            filename = argv[i];
        }
    }

    if (filename == NULL) {
        fprintf(stderr, "Aucun fichier spécifié.\n");
        return EXIT_FAILURE;
    }

    FILE *file = fopen(filename, "r");
    if (file == NULL) {
        perror("Erreur lors de l'ouverture du fichier");
        return EXIT_FAILURE;
    }

    LineQueue queue;
    initQueue(&queue);

    char buffer[256]; // Suppose une limite de 255 caractères par ligne, ajustez selon vos besoins

    // Lire les lignes du fichier et les ajouter à la file
    while (fgets(buffer, sizeof(buffer), file) != NULL) {
        enqueue(&queue, buffer);
    }

    // Imprimer les n dernières lignes
    printQueue(&queue);

    // Libérer la mémoire utilisée par la file
    freeQueue(&queue);

    fclose(file);

    return EXIT_SUCCESS;
}
```
## __Exercice 27 (TD)__
Etudiez le programme suivant : programme testaffichint.c
```c
#include <stdio.h> /* printf */
int main(int argc, char *argv[], char *env[]) {
int i=12345;
printf("%4.0d\n",i);
fwrite(&i,sizeof(int),1,stdout);
}
```
> L’affichage obtenu est le suivant :
> $testaffichint
> 12345
> 90 $
> 1. Quelle différence esiste-t-il entre ces deux affichages ?
> 2. Précisez la valeur de 90__. Quel est le “boutisme” (endianness) de cette machine ?
> 3. Quels avantages et inconvénients de ces types de codage dans les fichiers en matière de sauvegarde de
données ?

1. Différence entre les deux affichages :
   - L'affichage "12345" est produit par la fonction `printf("%4.0d\n", i);`, où `%4.0d` spécifie que l'entier `i` doit être affiché avec un champ de largeur minimale de 4 caractères, sans chiffres après la virgule.
   - L'affichage "90 $" est produit par `fwrite(&i, sizeof(int), 1, stdout);`, où le programme écrit la représentation binaire de l'entier `i` dans le fichier de sortie (`stdout` ici).

2. Valeur de 90 et Endianness :
   - La valeur "90" est la représentation ASCII du caractère `Z` en décimal.
   - La différence entre l'affichage "12345" et "90 $" suggère une différence d'ordre des octets endianness.
   - La valeur 12345 en mémoire est stockée comme une séquence d'octets, et selon le boutisme de la machine, ces octets peuvent être ordonnés de différentes manières.
   - La valeur "90 $" suggère que sur cette machine, l'octet le moins significatif est stocké en premier little-endian. Cela signifie que les octets sont stockés de droite à gauche, le moins significatif d'abord.

3. Avantages et Inconvénients des Types de Codage dans les Fichiers :
   - Avantages :
     - Little-Endian : Il est souvent plus simple à mettre en œuvre et est le format natif de nombreuses architectures. Les processeurs Intel x86, par exemple, utilisent little-endian
     - Big-Endian : Il facilite la lecture humaine des données binaires et est parfois utilisé pour la communication entre différentes architectures.

   - Inconvénients :
     - Little-Endian : Peut poser des problèmes lors de l'échange de données entre systèmes avec des boutismes différents.
     - Big-Endian : L'écriture et la lecture nécessitent souvent des opérations de manipulation des octets.

   - Choix : Le choix entre little endian et big endian dépend souvent des conventions et des architectures utilisées dans un contexte donné. Les protocoles de communication et les formats de fichiers spécifient généralement l'ordre des octets pour assurer l'interopérabilité entre différentes plates-formes.
