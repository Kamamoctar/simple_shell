# Projet Simple Shell 0x16.c - Sodash -

Il s'agit d'un simple interpréteur de commandes UNIX basé sur bash et Sh.

## Aperçu

**Sodashy** est un interpréteur de langage de commande compatible sh qui exécute des commandes lues à partir de l'entrée standard ou d'un fichier.

### Invocation

Utilisation : **Sodash**
Sodash est démarré avec l'entrée standard connectée au terminal. Pour commencer, compilez tous les .c situés dans ce dépôt en utilisant cette commande :

```C
gcc -Wall -Werror -Wextra -pedantic *.c -o sodash
./sodas
```

**Sodash** peut être appelé de manière interactive et non interactive. Si **sodash** est invoqué avec une entrée standard non connectée à un terminal, il lit et exécute les commandes reçues dans l'ordre.

Exemple:

```C
$ echo "echo 'principal'" | ./sodas
'principal'
$
```

Lorsque **sodash** est invoqué avec une entrée standard connectée à un terminal (déterminé par isatty(3), le mode interactif est ouvert. **sodash** utilisera l'invite suivante `^-^`.

Exemple:

```C
$./soda
^-^
```

Si un argument de ligne de commande est invoqué, **sodash** prendra ce premier argument comme un fichier à partir duquel lire les commandes.

Exemple:

```C
$ chat texte
echo 'principal'
$ ./texte soda
'principal'
$
```

### Environnement

Lors de l'invocation, **sodash** reçoit et copie l'environnement du processus parent dans lequel il a été exécuté. Cet environnement est un tableau de chaînes *nom-valeur* décrivant des variables au format *NOM=VALEUR*. Voici quelques variables environnementales clés :

#### MAISON

Le répertoire personnel de l'utilisateur actuel et l'argument de répertoire par défaut pour la commande intégrée **cd**.

``` CC
$ echo "echo $HOME" | ./sodas
/home/vagabond
```

#### PWD

Le répertoire de travail actuel tel que défini par la commande **cd**.

```C
$ echo "echo $PWD" | ./sodas
/home/vagrant/main/simple_shell
```

#### OLDPWD

Le répertoire de travail précédent tel que défini par la commande **cd**.

```C
$ echo "echo $OLDPWD" | ./sodas
/home/vagrant/main/bog-062022-test_suite
```

#### CHEMIN

Une liste de répertoires séparés par deux-points dans lesquels le shell recherche des commandes. Un nom de répertoire nul dans le chemin (représenté par l'un des deux deux-points adjacents, un deux-points initial ou un deux-points final) indique le répertoire actuel.

```C
$ echo "echo $CHEMIN" | ./sodas
/home/vagrant/.cargo/bin:/home/vagrant/.local/bin:/home/vagrant/.rbenv/plugins/ruby-build/bin:/home/vagrant/.rbenv/shims:/home/vagrant /.rbenv/bin:/home/vagrant/.nvm/versions/node/v10.15.3/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin :/bin:/usr/games:/usr/local/games:/snap/bin:/home/vagrant/.cargo/bin:/home/vagrant/workflow:/home/vagrant/.local/bin
```

### Exécution de la commande

Après avoir reçu une commande, **sodash** la segmente en mots en utilisant `" "` comme délimiteur. Le premier mot est considéré comme la commande et tous les mots restants sont considérés comme des arguments de cette commande. **sodash** procède ensuite aux actions suivantes :

1. Si le premier caractère de la commande n'est ni une barre oblique (`\`) ni un point (`.`), le shell le recherche dans la liste des commandes intégrées du shell. S'il existe une fonction intégrée de ce nom, la fonction intégrée est invoquée.
2. Si le premier caractère de la commande n'est ni une barre oblique (`\`), ni un point (`.`), ni un élément intégré, **sodash** recherche chaque élément de la variable d'environnement **PATH** pour un répertoire contenant un fichier exécutable de ce nom.
3. Si le premier caractère de la commande est une barre oblique (`\`) ou un point (`.`) ou si l'une des recherches ci-dessus a réussi, le shell exécute le programme nommé avec tous les arguments restants dans un environnement d'exécution séparé.

### Statut de sortie

**sodash** renvoie l'état de sortie de la dernière commande exécutée, zéro indiquant le succès et différent de zéro indiquant l'échec.
Si une commande n'est pas trouvée, l'état de retour est 127 ; si une commande est trouvée mais n'est pas exécutable, l'état de retour est 126.
Toutes les commandes intégrées renvoient zéro en cas de succès et un ou deux en cas d'utilisation incorrecte (indiqué par un message d'erreur correspondant).

### Signaux

Lors de l'exécution en mode interactif, **sodash** ignore l'entrée clavier ctrl+c. Alternativement, une entrée de End-Of-File ctrl+d quittera le programme.

L'utilisateur tape ctrl+d dans la commande foutrh.

```C
.$/soda
^-^ ^C
^-^ ^C
^-^ ^C
^-^
```

### Remplacement des variables

**sodash** interprète le caractère `$` pour le remplacement de variable.

#### $ENV_VARIABLE

`ENV_VARIABLE` est remplacé par sa valeur.

Exemple:

```C
$ echo "echo $PWD" | ./sodas
/home/vagrant/main/simple_shell
```

#### $ ?

`?` est remplacé par la valeur de retour du dernier programme exécuté.

Exemple:

```C
$ echo "echo $?" | ./sodas
0
```

#### $$

Le deuxième `$` est remplacé par l'ID de processus actuel.

Exemple:

```C
$ echo "echo $$" | ./sodas
3855
```

### Commentaires

**sodash** ignore tous les mots et caractères précédés d'un caractère `#` sur une ligne.

Exemple:

```C
$ echo "echo 'main' #ceci sera ignoré !" | ./sodas
'principal'
```

### Les opérateurs

**sodash** interprète spécialement les caractères d'opérateur suivants :

#### ; - Séparateur de commandes

Les commandes séparées par un `;` sont exécutées séquentiellement.

Exemple:

```C
$ echo "echo 'bonjour' ; echo

#### exit

* Usage: `exit [STATUS]`
* Exits the shell.
* The `STATUS` argument is the integer used to exit the shell.
* If no argument is given, the command is interpreted as `exit 0`.

Example:

```C
./sodash
exit
```

#### env

* Usage: `env`
* Prints the current environment.

Example:

```C
$ ./sodash
$ env
NVM_DIR=/home/vagrant/.nvm
...
```

#### setenv

* Usage: `setenv [VARIABLE] [VALUE]`
* Initializes a new environment variable, or modifies an existing one.
* Upon failure, prints a message to `stderr`.

Example:

```C
$ ./sodash
$ setenv NAME Main
$ echo $NAME
Main
```

#### unsetenv

* Usage: `unsetenv [VARIABLE]`
* Removes an environmental variable.
* Upon failure, prints a message to `stderr`.

Example:

``` C
$ ./sodash
$ setenv NAME Main
$ unsetenv NAME
$ echo $NAME

$
```

## Authors

* NAMADOU Moctar Kamakaté ((https://github.com/Kamamoctar))

## More information

**Sodash** is a simple shell unix command interpreter that is part of the alx low level programming module at ALXSchool and is intended to emulate the basics **sh** shell. All the information given in this README is based on the **sodash** and **bash** man (1) pages.
