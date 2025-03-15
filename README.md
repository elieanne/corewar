# COREWAR
# français
Introduction
En 1984, DG Jones a eu la brillante idée de créer un jeu de programmation inspiré de la façon dont certains virus parviennent à prendre le contrôle d'un système et de sa mémoire interne.

Dès le début, le jeu a connu un grand succès et une communauté florissante a ajouté de nouvelles fonctionnalités et organisé des tournois dans le monde entier.

Dans ce projet, nous recréerons les outils nécessaires pour exécuter une version plus élaborée du jeu.

Instructions générales
La machine virtuelle et l'assembleur peuvent être réalisés dans n'importe quel langage compilé.
Vous ne devez utiliser que la bibliothèque standard du langage que vous choisissez.
Si vous utilisez une bibliothèque externe, celle-ci doit être largement justifiée et expliquée (évitez-la à tout prix).
Vous ne devez pas utiliser de bibliothèques externes pour effectuer la partie analyse de l'assembleur pour quelque raison que ce soit.
Les programmes ne devraient jamais planter pour quelque raison que ce soit.
Les programmes ne devraient jamais fuir de mémoire pour quelque raison que ce soit.
Dynamique du jeu
À la base, le jeu consiste à avoir deux ou plusieurs programmes (écrits par les concurrents) qui seront exécutés par la machine virtuelle dans une arène (un espace mémoire sandbox).

Nous appellerons désormais ces programmes « joueurs », car ce sont eux qui joueront. Leurs créateurs n'auront aucune possibilité d'interagir avec eux ou avec l'arène pendant le match.

Le joueur disposera d'un ensemble limité d'instructions pour modifier l'état de l'arène. Il pourra les utiliser pour corrompre la mémoire de l'adversaire, se répliquer ou réparer les dégâts causés par les ennemis.

Le gagnant sera le dernier joueur à signaler à l'arène qu'il était en vie avant la fin de la partie.

Structure du jeu
La machine virtuelle est une reproduction simplifiée du fonctionnement d'un processeur. Elle exécute le code binaire (les joueurs) dans l'arène par cycles.

Au début du jeu, chaque joueur aura un processus, et ce processus sera placé au début de l'espace mémoire de chaque joueur (la position actuelle d'un processus est appelée compteur de programme, ou PC).

Chaque processus dispose également d'un espace mémoire privé (les registres) et d'une retenue. Une retenue est un indicateur spécial qui peut être modifié par le résultat de certaines instructions. Elle peut être utilisée de nombreuses manières astucieuses.

Les joueurs pourront créer de nouveaux processus en utilisant des instructions spécifiques, il convient toutefois de noter que chaque instruction nécessite un certain nombre de cycles pour être exécutée, et la création d'un nouveau processus est la plus coûteuse.

Fin de partie
Il existe une instruction spéciale, appelée livequi informe la VM qu'un joueur est toujours en bonne santé et se porte bien.

De temps à autre, la machine virtuelle vérifie quels processus ont exécuté une telle instruction et arrête ceux qui ne l'ont pas fait. Les processus ne sont maintenus en vie que s'ils exécutent au moins une liveinstruction entre les vérifications de la machine virtuelle.

Une fois qu'il n'y a plus de processus actifs, la VM arrête son exécution et le dernier joueur qui a notifié qu'elle était en vie est le gagnant du match.

La VM utilisera certaines règles pour réduire le temps écoulé depuis la dernière vérification à zéro, ce qui signifie que les jeux ne peuvent jamais être infinis et que tous les processus seront tués à un moment donné.

L'assembleur
Nous ne voulons pas écrire de code en binaire, mais nous ne voulons pas non plus que la machine virtuelle gère la complexité de l'analyse des instructions de haut niveau au moment de l'exécution.

Pour résoudre ce problème, nous allons créer un assembleur qui prendra un code assembleur en entrée et fournira un programme binaire en sortie.

Vous devez suivre ces spécifications :

Si aucun paramètre n'est passé, un message d'aide sera imprimé.
L'assembleur prend a file.scomme entrée.
Il renvoie un file.coren sortie.
file.sest un fichier texte ordinaire et ennuyeux.
file.corest un fichier binaire, afin de l'inspecter nous vous suggérons d'utiliser des outils de ligne de commande comme hexdump.
S'il y a une erreur dans le fichier source, l'assembleur doit quitter avec un code d'erreur, imprimer un message stderr(le plus précis sera le mieux) et ne créer aucun fichier file.cor.
Le binaire doit être écrit en big-endian .
La machine virtuelle
Le rôle de la Machine Virtuelle est d'exécuter les programmes binaires (joueurs) fournis sous forme .corde fichiers (pour un maximum de 4 joueurs).

La machine virtuelle doit gérer un -d [NB_CYCLES]indicateur (indiquant « dump »). Si cet indicateur est spécifié, la machine virtuelle arrête l'exécution NB_CYCLESet vide la mémoire de l'arène au format hexadécimal, en écrivant 32 octets par ligne.

Vous devez suivre ces spécifications :

Si aucun paramètre n'est passé, un message d'aide sera imprimé.
Si l'un des .corfichiers est corrompu, la machine virtuelle doit quitter avec un code d'erreur, imprimer un message stderret ne pas exécuter les programmes.
Au début de la bataille, la VM imprimera un message de bienvenue comme celui montré dans l'exemple.
À la fin de la bataille, la VM écrira le vainqueur (le cas échéant) comme celui montré dans l'exemple.
Les joueurs seront chargés dans l'arène en commençant par le premier octet et ils seront uniformément espacés les uns des autres.
Le dernier programme passé sera le premier exécuté durant le cycle.
Au cours d'un cycle, la VM chargera l'instruction sur le PC actuel et attendra N cycles avant de l'exécuter (N étant le coût de l'instruction).
Ce n'est que lors de l'exécution de l'instruction que la VM vérifiera les paramètres qu'elle peut avoir et l'exécutera uniquement si les paramètres sont corrects, sinon elle imprimera une erreur stderret continuera à s'exécuter.
Si une instruction a des paramètres incorrects, le PC sera avancé en fonction de la taille des paramètres.
Si l'instruction n'existe pas dans le jeu d'instructions, le PC sera avancé d'un octet.
Lorsqu'un nouveau processus est forké, il sera placé à la fin des processus et commencera son exécution au début du cycle suivant (ce qui signifie qu'il sera le premier à être exécuté lors du cycle suivant).
L'exécution entière est déterministe, donc avec les mêmes entrées, vous devez toujours avoir les mêmes sorties.
La machine virtuelle suppose que le binaire est en big-endian et le lit en conséquence.
Voici les cas où un fichier est considéré comme corrompu :

Code de signature incorrect.
La taille déclarée du programme ne correspond pas à la taille réelle.
La taille du programme est supérieure à la taille maximale autorisée.
La taille totale du fichier est inférieure à la taille minimale.
Messages de bienvenue et de fin de partie
Au début de chaque partie, la VM doit imprimer :

For this match the players will be:
Player 1 ([X] bytes): [NAME] ([DESCRIPTION])
Player 2 ([X] bytes): [NAME] ([DESCRIPTION])
...
À la fin du jeu, la machine virtuelle devrait imprimer cycle [X]: The winner is player [X]: [NAME]!.

Si personne n'a exécuté une liveinstruction valide, le message de fin devrait être cycle [X]: Nobody wins!.

Espace mémoire circulaire de l'arène
La mémoire où les joueurs vont se battre est circulaire, cela signifie que si nous voulons avancer à partir de la dernière adresse en mémoire (par exemple 4095) nous arriverons à l'adresse 0.

Cela implique que le déplacement vers l'arrière d'une position à partir de 0nous amènera à l'adresse 4095.

Avoir une mémoire circulaire garantit aux joueurs qu'ils ne déborderont jamais l'espace mémoire dans lequel ils jouent.

Arrêter l'exécution du processus
Chaque CYCLE_TO_DIEmachine virtuelle vérifiera chaque processus et tuera tous les processus qui n'ont pas exécuté avec succès une liveinstruction.

Pour éviter des jeux infinis, CYCLES_TO_DIEsera décrémenté de CYCLE_DELTAsous certaines conditions :

Si au cours de la dernière boucle de vie, il y en a au moins qui NBR_LIVEont été exécutés avec succès par les joueurs.
S'il y a eu MAX_CHECKSdes boucles de vie depuis qu'il a été décrémenté la dernière fois.
Notez que certains joueurs intelligents peuvent tromper un autre joueur pour qu'il commence à faire livedes déclarations à leur place, ce qui signifie pratiquement qu'un joueur peut toujours faire livedes déclarations après que tous ses processus ont été tués.

Le langage assembleur
Dans le fichier d'assemblage, il doit y avoir un .name "[Name of the player]"champ et un .description "[Description of the player]"avant chaque instruction.

Chaque instruction doit tenir sur une seule ligne et peut être précédée d'une déclaration d'étiquette. Le nom de l'instruction est séparé de l'étiquette facultative et des paramètres par un ou plusieurs espaces.

Les paramètres sont séparés les uns des autres par ,.

Les instructions seront de tailles différentes en fonction du nombre et du type de paramètres passés.

Types de paramètres
Paramètre de registre
Chaque processus possède ses propres registres (de 1 à REG_NUMBER). Chacun de ces registres peut contenir 32 bits, mais le numéro de registre lui-même est écrit sur seulement 8 bits (ce qui suffit pour passer de 1 à 16).

Ils sont formatés comme r1, r2, ..., r16.

La VM initialisera les registres pour 0chaque joueur sauf pour r1lequel aura - PLAYER_ID, donc pour le premier joueur r1 == -1.

Paramètre direct
Un paramètre direct est une valeur qui se représente elle-même. Il est formaté en utilisant %« précédent ». %10Il représentera donc le nombre dix.

Paramètre indirect
Un paramètre indirect vous donnera la position relative de l'adresse où rechercher la valeur, considérez-le comme un ancêtre des pointeurs bruts.

Dans ce cas, 10il sera demandé à la machine virtuelle de regarder dix octets en avant dans l'instruction actuelle et de prendre la valeur à partir de là.

N'ayez pas peur d'utiliser l'Asm et la VM fournis pour jouer avec ces différents paramètres afin d'être sûr d'avoir bien compris leur comportement.

Jeu d'instructions
Nom	Nb Paramètres	Opcode	Cycles Nb	A un Pcode	A Idx	Paramètres
en direct	1	1	10	FAUX	FAUX	Direct
ld	2	2	5	vrai	FAUX	[Indirect, Direct]
Registre
St	2	3	5	vrai	FAUX	Registre
[Registre, Indirect]
ajouter	3	4	10	vrai	FAUX	S'inscrire
S'inscrire
S'inscrire
sous	3	5	10	vrai	FAUX	S'inscrire
S'inscrire
S'inscrire
et	3	6	6	vrai	FAUX	[Registre, Indirect, Direct]
[Registre, Indirect, Direct]
Registre
ou	3	7	6	vrai	FAUX	[Registre, Indirect, Direct]
[Registre, Indirect, Direct]
Registre
xor	3	8	6	vrai	FAUX	[Registre, Indirect, Direct]
[Registre, Indirect, Direct]
Registre
zjmp	1	9	20	FAUX	vrai	Direct
ldi	3	10	25	vrai	vrai	[Registre, Indirect, Direct]
[Registre, Direct]
Registre
IST	3	11	25	vrai	vrai	Registre
[Registre, Indirect, Direct]
[Registre, Direct]
fourchette	1	12	800	FAUX	vrai	Direct
lld	2	13	10	vrai	FAUX	[Indirect, Direct]
Registre
lldi	3	14	50	vrai	vrai	[Registre, Indirect, Direct]
[Registre, Direct]
fourche	1	15	1000	FAUX	vrai	Direct
non	1	16	2	vrai	FAUX	Registre
live: indique à la machine virtuelle que le joueur dont l'identifiant correspond à l'opposé du premier paramètre est vivant. Si le premier paramètre est positif, -2cela signifie que le joueur 2 est vivant.
ld: charge le premier paramètre dans le registre, passé en second. (Si le premier paramètre est indirect, la VM s'y appliquera % IDX_MOD.)
st: écrit la valeur du registre passée comme premier paramètre dans le second paramètre. (Si le second paramètre est indirect, la VM s'y appliquera % IDX_MOD.)
add: additionne les deux premiers arguments et écrit le résultat dans le troisième.
sub: soustrait les deux premiers arguments et écrit le résultat dans le troisième.
and, or, xor: applique l'opération binaire correspondante aux deux premiers paramètres et l'enregistre dans le troisième. (Si le premier et/ou le deuxième paramètre sont indirects, la machine virtuelle % IDX_MODles appliquera.)
zjmp: déplace le PC en ajoutant la valeur passée comme premier paramètre. (La VM s'appliquera toujours % IDX_MODau premier paramètre)
ldi: écrit la valeur contenue à l'adresse obtenue en additionnant les deux premiers arguments dans le registre du troisième argument. (La machine virtuelle s'appliquera % IDX_MODà l'adresse obtenue)
sti: écrit la valeur du registre passé en premier argument dans l'adresse obtenue en additionnant les deux derniers arguments. (La machine virtuelle s'appliquera % IDX_MODà l'adresse obtenue)
fork: crée un nouveau processus qui sera une copie exacte (profonde) du processus actuel, à l'exception du PC qui se trouvera à l'adresse spécifiée dans l'argument. (La machine virtuelle s'appliquera % IDX_MODau premier argument)
lld, lldi, lfork: ces instructions sont les versions longues de ld, ldiet fork, ce qui signifie que les adresses ne seront pas tronquées de IDX_MOD. (Comme ces instructions sont « longues », l'opération modulo ne leur sera pas appliquée)
nop: cette opération ne fait rien, elle peut toujours prendre un argument et possède un pcode, alors soyez prudent sur la façon dont vous l'implémentez.
Toutes les adresses sont relatives au PC actuel du processus.

Certaines instructions doivent tronquer les adresses qui leur sont attribuées en appliquant le modulo IDX_MOD. Cette fonctionnalité empêche les joueurs d'atteindre des espaces mémoire trop éloignés du PC actuel, ce qui signifie que les processus ne pourront pas s'attaquer directement, mais devront se déplacer par étapes plus petites, ce qui contribue à des parties plus équilibrées.

Étiquettes
Les étiquettes sont également appelées pseudo-instructions, car elles ne sont pas traduites en code binaire. Elles sont largement utilisées pour aider les programmeurs à créer du code assembleur sans avoir à mémoriser et compter les positions mémoire relatives.

Ainsi, lorsque vous regardez dans les binaires, les étiquettes seront remplacées afin de correspondre à leur position relative en octets à partir de l'endroit où elles ont été utilisées.

À titre d'exemple, le ameba.slecteur zjmp %:helloest exactement le même que zjmp %-5( -5étant ff fben notation hexadécimale).

C'est parce qu'à partir de là, zjmpvous devrez revenir 5 octets en arrière afin de faire correspondre la hello:déclaration d'étiquette.

Notez que les étiquettes peuvent être déclarées au début d'une instruction ou sur leur propre ligne.

Champ Pcode
Certaines instructions peuvent accepter différents types de paramètres, et ces paramètres peuvent nécessiter des tailles différentes. Pour ces instructions, l'assembleur utilise un octet après l'opcode pour indiquer à la machine virtuelle le type de paramètres attendu.

Tous les deux bits de cet octet informeront la VM du type d'un paramètre.

01sera utilisé pour un paramètre de registre.
10sera utilisé pour un paramètre direct.
11sera utilisé pour un paramètre indirect.
Un exemple d'octet de pcode pourrait être 01111000:

Ici, le premier paramètre devrait être un registre, le deuxième devrait être une valeur indirecte et le troisième une valeur directe.

Ces nombres sont en base binaire, par exemple 10en binaire c'est 2en décimal.

A un champ IDX
Certaines instructions additionnent des valeurs directes et/ou les utilisent comme adresses pour accéder à l'espace mémoire de l'arène. C'est pourquoi nous savons qu'un entier de 32 bits n'est pas nécessaire et, pour économiser de l'espace, nous enregistrons les valeurs directes sur seulement deux octets dans ce cas.

Vous avez Had Idxune colonne dans la table Instruction Set, lorsqu'elle est vraie, vous devez enregistrer et lire les paramètres directs sur deux octets, lorsqu'elle est fausse, ils seront enregistrés sur quatre octets comme d'habitude.

Le drapeau Carry
Dans tous les processeurs, vous trouverez un carry qui est un indicateur qui notifie certains états de cas limites pour autoriser des opérations spéciales, assurer la sécurité et servir de nombreuses autres fonctionnalités.

Le portage des processus dans la VM est en ce sens une version simplifiée de ce concept.

Les commandes suivantes modifieront le déroulement du processus qui les exécute : ld, add, sub, and, or, xor.

Ces commandes définiront le report à truesi la valeur écrite dans le registre est 0, et le définiront à falsesinon.

Une seule commande lit le report et c'est zjmp, elle fera le saut si le report est vrai et ne fera rien sinon.

La signature du fichier
La signature, aussi appelée « magique », d'un fichier est une valeur particulière inscrite au début d'un binaire. Cette signature permet au système d'exploitation de déterminer le type de fichier et de l'exécuter. Pour les .corfichiers, la signature est fournie dans le fichier de configuration.

Votre joueur
Bien que l'objectif principal pour l'instant soit de créer un Asm et une VM, vous devez fournir un joueur de base qui devrait être capable de se battre et de gagner contre le ameba.s, ce joueur sera testé dans la VM fournie lors de l'évaluation.

Ressources
Fichier de configuration
La spécification nécessite beaucoup de constantes, il serait fastidieux de les définir dans le sujet à chaque fois qu'elles sont mentionnées.

C'est pourquoi nous fournissons un fichier de configuration contenant toutes les constantes requises. Certaines sont spécifiques à l'ASM, d'autres à la VM, la grande majorité étant utile aux deux.

Bien que le fichier soit indépendant de la langue, il serait très facile de le traduire dans une langue spécifique. Nous vous suggérons de le faire afin de centraliser la configuration. Cela facilitera le débogage et l'extension de vos programmes si nécessaire.

Un joueur de base
Pour mieux comprendre le fonctionnement de l'Asm et de la VM, plongeons dans le code assembleur avec un lecteur très basique :

.name "ameba"
.description "not doing much"

        sti r1,%:hello,%1
        and r1,%0,r1

hello:  live %1
        zjmp %:hello
Ce joueur est l’un des êtres vivants les plus basiques que vous puissiez imaginer, il dit simplement qu’il est vivant.

Voici comment il procède pour y parvenir :

L' stiinstruction copiera la valeur dans le premier registre (qui est l'opposé de son identifiant, donc -1si ce joueur est le premier).
Il est intéressant de noter que ce numéro sera copié à l'adresse en mémoire correspondant à l'étiquette hello+ 1 octet.
En d’autres termes, cette instruction modifie l’argument de l’ liveinstruction.
L' andinstruction copiera le résultat de l'opérateur logique AND dans le premier registre.
Ce résultat sera nul, car tout nombre ET 0 est nul. Cela a pour effet secondaire de définir le report de ce processus sur vrai.
L' liveinstruction informe la machine virtuelle que le joueur est actif. Notez que la valeur spécifique de l'argument n'a aucune importance dans ce contexte, car l' stiinstruction la remplace avant son liveexécution.
Cela zjmpramène le PC au début de l'instruction en direct (créant une boucle).
Notez zjmpque le saut n'est effectué que si la retenue est différente de zéro. C'est pourquoi nous avons exécuté l' andinstruction précédente.
Voyons maintenant à quoi ressemble ce binaire de lecteur avec hexdump -C ameba.cor:

00000000  00 ea 83 f3 61 6d 65 62  61 00 00 00 00 00 00 00  |....ameba.......|
00000010  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00  |................|
*
00000080  00 00 00 00 00 00 00 00  00 00 00 17 6e 6f 74 20  |............not |
00000090  64 6f 69 6e 67 20 6d 75  63 68 00 00 00 00 00 00  |doing much......|
000000a0  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00  |................|
*
00000890  0b 68 01 00 0f 00 01 06  64 01 00 00 00 00 01 01  |.h......d.......|
000008a0  00 00 00 01 09 ff fb                              |.......|
000008a7
Tous les nombres sont représentés en hexadécimal.
Au début de chaque ligne se trouve la position du premier octet de la ligne en hexadécimal.
Cela signifie que pour la deuxième ligne, 10hex est en fait 16 en notation décimale.
Le panneau de droite affiche un .si l'octet n'est pas un caractère ASCII valide.
Les lignes répétées sont agrégées sous *, c'est pourquoi nous passons de 10à 80.
Chacun xxest un octet (un octet est de 8 bits, ffen hexadécimal c'est 1111 1111en binaire, 255en décimal).
Lisons-le :

Les 4 premiers octets 00 ea 83 f3constituent la signature du programme (également appelée la magie), il s'agit d'un entier de 32 bits, représenté par quatre tranches de 8 bits.
Ensuite, les 128 bits suivants sont destinés au nom du programme, les cinq premiers 61 6d 65 62 61sont le nom réel, les autres sont des zéros car nous n'en avons pas réellement besoin.
Ensuite, nous avons 4 octets 00 00 00 17(donc 23) qui seront un entier de 32 bits avec la taille en octets du programme à exécuter dans l'arène de la VM (donc uniquement la taille des instructions, sans le nom, la description, la signature etc).
Ensuite, nous avons la description qui fonctionne exactement de la même manière que le nom, mais elle ajoutera un remplissage de quatre octets à la fin.
Les instructions réelles commencent à partir de là :

L'instruction stiest composée de ces octets : 0b 68 01 00 0f 00 01.
0best l'opcode, 68est le pcode, 01est le premier paramètre, 00 0fest le deuxième paramètre et 00 01est le troisième paramètre.
Notez que les deux derniers paramètres sont écrits sur deux octets chacun car stiils sont Has Idxdéfinis sur vrai.
L'instruction suivante est and, et c'est 06 64 01 00 00 00 00 01.
06est l'opcode, 64est le pcode, le premier paramètre est 01, le deuxième est 00 00 00 00et le troisième est 01.
Voici l' liveinstruction : 01 00 00 00 01.
01est l'opcode, 00 00 00 01est le premier paramètre, notez que l'espace mémoire du paramètre sera remplacé par l' stiinstruction.
La dernière instruction est zjmp: 09 ff fb.
01est l'opcode tandis que ff fbest le premier paramètre (sur deux octets à cause de Has Idxencore une fois).
Environnement de test
Vous disposerez d'une aire de jeux comprenant :

Une VM et un assembleur binaires à utiliser comme références.
Un Dockerfile pour exécuter les binaires dans un conteneur.
Instructions sur la façon d'exécuter les références.
Quelques joueurs à utiliser comme tests.
La VM fournie dispose d'un indicateur supplémentaire -vque vous pouvez utiliser pour imprimer l'état de la VM à chaque cycle, cela devrait vous aider grandement pendant le développement et le débogage.

Quelques conseils pour la route
Bien que le fonctionnement de la VM et de l'assembleur puisse paraître assez étrange et obscur, c'est un scénario parfait pour reproduire un CPU et comprendre ce que fait réellement un ordinateur en son cœur.

Dans ce projet, vous créez en effet une machine Turing complète largement inspirée de l'architecture Von Neumann (qui est toujours la façon dont fonctionnent les processeurs d'aujourd'hui).

Prime
Ces bonus ne seront appliqués que si l'assembleur et la machine virtuelle sont parfaits. Vous pouvez utiliser des bibliothèques externes (par exemple des bibliothèques graphiques) pour les obtenir.

Créez un désassembleur qui prendra du binaire en entrée et renverra un .sfichier en sortie.
Créez un visualiseur pour montrer ce qui se passe dans la VM en temps réel.
Introduire la prise en charge des opérations arithmétiques dans le langage assembleur.
Introduire un système de macro simple dans le langage assembleur.
Ressources
Machine de Turing
Architecture Von Neumann

# anglais
Introduction
Back in 1984 D. G. Jones had the brilliant idea to create a programming game inspired by how some viruses manage to take over a system and their internal memory.

From the very start the game was a great success and a flourishing community added new features and organized tournaments worldwide.

In this project we will recreate the necessary tools to run a more elaborate version of the game.

General instructions
The Virtual Machine and the Assembler can be done in any compiled language.
You should only use the standard library of the language you choose.
In case you use any external library it must be largely justified and explained (avoid it at any cost).
You must not use external libraries to do the parsing part of the Assembler for any reason.
The programs should never crash for any reason.
The programs should never leak memory for any reason.
Game dynamics
At its very core the game consists of having two or more programs (written by the contestants) that will be executed by the Virtual Machine in an arena (a sandbox memory space).

We will call those programs the "players" from now on since they will be the ones playing. Their creators won't have any possibility to interact with them or the arena during the match.

The player will have a limited set of instructions it could use to change the state of the arena. It can use those instructions to try and corrupt the opponent's memory space, replicate itself or repair damages made by the enemies.

The winner will be the last player notifying the arena it was alive before the game ends.

Structure of the game
The Virtual Machine is a simplified reproduction of how a CPU works. It will execute the binary code (the players) in the arena in cycles.

At the start of the game each player will have one process, and this process will be placed at the start of each player memory space (the current position of a process is called program counter, or PC).

Each process will also have a private memory space (the registers) and a carry. A carry is a special flag that may be changed by the result of some instructions, it can be used in many clever ways.

The players will be able to create new processes using specific instructions, it is worth noting though that every instruction takes a certain amount of cycles to be executed, and creating a new process is the most expensive one.

End game
There is a special instruction, called live that notifies the VM one player is still healthy and doing well.

Every once in a while the VM will check which processes executed such instruction and kill all the processes that have not. The processes are kept alive only if they execute at least one live instruction between the VM checks.

Once there is no more active processes the VM stops execution and the last player that notified it was alive is the winner of the match.

The VM will use some rules to decrease the elapsed time since the last check down to zero, this means the games can never be infinite and all processes will be killed at one point.

The Assembler
We don't want to write code in binary, but we don't want the VM to deal with the complexity of parsing high level instructions at execution time.

To solve this issue we will create an Assembler which will take an assembly code as input and deliver a binary program as output.

You should follow those specifications:

If no parameters are passed it will print a help message.
The assembler takes a file.s as input.
It returns a file.cor as output.
file.s is a regular and boring text file.
file.cor is a binary file, in order to inspect it we suggest you to use command-line tools like hexdump.
If there is any error in the source file the Assembler should exit with an error code, print a message on stderr (the more specific the better) and do not create any file.cor.
The binary must be written in big-endian.
The Virtual Machine
The Virtual Machine role is to execute the binary programs (players) provided as .cor files (for a maximum of 4 players).

The VM must handle a -d [NB_CYCLES] flag (stating for "dump"). If the flag is specified the VM will stop execution at NB_CYCLES and dump the memory in the arena in hexadecimal, writing 32 bytes per row.

You should follow those specifications:

If no parameters are passed it will print a help message.
If one of the .cor file is corrupted the VM should exit with an error code, print a message on stderr and do not execute the programs.
At the start of the battle the VM will print a welcome message as the one showed in the example.
At the end of the battle the VM will write the winner (if any) as the one showed in the example.
The players will be loaded into the arena starting by the first byte and they will be evenly spaced from each other.
The last program passed will be the first one executed during the cycle.
During a cycle the VM will load the instruction at the current PC and wait N cycles before to execute it (N being the cost of the instruction).
Only when executing the instruction the VM will check for the parameters it may have and it will execute it only if the parameters are correct, otherwise it will print an error on stderr and continue to execute.
If an instruction has incorrect parameters the PC will be moved forward according to the size of the parameters.
If the instruction doesn't exist in the instruction set the PC will be moved forward of 1 byte.
When a new process is forked it will be placed at the end of the processes and start its execution at the start of the next cycle (which means it will be the first one being executed on the next cycle).
The entire execution is deterministic, so with the same inputs you must always have the same outputs.
The VM assumes the binary is in big-endian and read it accordingly.
Those are the cases where a file is considered corrupted:

Wrong signature code.
Declared size of the program not matching the actual size.
The size of the program is bigger that the maximum allowed size.
The total file size is smaller than the minimum size.
Greeting and end game messages
At the start of each game the VM should print:

For this match the players will be:
Player 1 ([X] bytes): [NAME] ([DESCRIPTION])
Player 2 ([X] bytes): [NAME] ([DESCRIPTION])
...
At the end of the game the vm should print cycle [X]: The winner is player [X]: [NAME]!.

If nobody executed a valid live statement the end message should be cycle [X]: Nobody wins!.

Circular memory space of the arena
The memory where the players will fight is circular, this means if we want to move forward from the last address in memory (for example 4095) we will arrive at address 0.

It implies that moving backward of one position from 0 will bring us to the address 4095.

Having circular memory guarantees players they will never overflow the memory space they are playing in.

Stop process execution
Every CYCLE_TO_DIE the VM will check every process and kill all the processes that did not successfully execute any live instruction.

To avoid infinite games, CYCLES_TO_DIE will be decremented by CYCLE_DELTA under certain conditions:

If during the last life loop there were at least NBR_LIVE successfully executed by the players.
If it has been MAX_CHECKS life loops since it was decremented last time.
Notice that some smart players may trick another player to start making live statements for them, this virtually means a player can still make live statements after all its processes were killed.

The Assembly language
In the assembly file there must be a .name "[Name of the player]" field and a .description "[Description of the player]" before any instruction.

Every instruction has to be on a single line and may be preceded by a label declaration. The instruction name is separated from the optional label and the parameters by one or more spaces.

The parameters are separated from each other by ,.

The instructions will be of different sizes depending on the number and type of parameters passed.

Parameters types
Register parameter
Each process has its own registers (from 1 to REG_NUMBER). Each of those registers can hold 32 bits, but the register number itself is written in only 8 bits (which are enough to go from 1 to 16).

They are formatted as r1, r2, ..., r16.

The VM will initialize the registers to 0 for each player except for r1 which will have - PLAYER_ID, so for the first player r1 == -1.

Direct parameter
A direct parameter is a value that represents itself, it is formatted by using % before the value. So %10 will represent the number ten.

Indirect parameter
An indirect parameter will give you the relative position of the address where to look for the value, think of it as an ancestor of raw pointers.

In this case 10 will say to the VM to look ten bytes forward the current instruction and take the value from there.

Don't be scared to use the provided Asm and VM to play with those different parameters in order to be sure you understood properly their behavior.

Instruction Set
Name	Nb Params	Opcode	Nb Cycles	Has Pcode	Has Idx	Params
live	1	1	10	false	false	Direct
ld	2	2	5	true	false	[Indirect, Direct]
Register
st	2	3	5	true	false	Register
[Register, Indirect]
add	3	4	10	true	false	Register
Register
Register
sub	3	5	10	true	false	Register
Register
Register
and	3	6	6	true	false	[Register, Indirect, Direct]
[Register, Indirect, Direct]
Register
or	3	7	6	true	false	[Register, Indirect, Direct]
[Register, Indirect, Direct]
Register
xor	3	8	6	true	false	[Register, Indirect, Direct]
[Register, Indirect, Direct]
Register
zjmp	1	9	20	false	true	Direct
ldi	3	10	25	true	true	[Register, Indirect, Direct]
[Register, Direct]
Register
sti	3	11	25	true	true	Register
[Register, Indirect, Direct]
[Register, Direct]
fork	1	12	800	false	true	Direct
lld	2	13	10	true	false	[Indirect, Direct]
Register
lldi	3	14	50	true	true	[Register, Indirect, Direct]
[Register, Direct]
lfork	1	15	1000	false	true	Direct
nop	1	16	2	true	false	Register
live: says to the VM the player with the id matching the opposite of the first parameter is alive. If the first parameter is -2 it means player 2 is alive.
ld: loads the first parameter into the registry passed as second parameter. (If the first parameter is Indirect, the VM will apply % IDX_MOD to it)
st: writes the value in the registry passed as the first parameter into the second parameter. (If the second parameter is Indirect, the VM will apply % IDX_MOD to it)
add: sums the first two arguments and writes the result into the third one.
sub: subtract the first two arguments and writes the result into the third one.
and, or, xor: apply the respective bitwise operation to the first two parameters and saves it in the third one. (If the first and/or second parameters are Indirect, the VM will apply % IDX_MOD to them)
zjmp: moves the PC adding the value passed as the first parameter. (The VM will always apply % IDX_MOD to the first parameter)
ldi: writes the value contained at the address obtained by summing the first two arguments into the register in the third argument. (The VM will apply % IDX_MOD to the obtained address)
sti: writes the value of the register passed as the first argument into to address obtained by summing the last two arguments. (The VM will apply % IDX_MOD to the obtained address)
fork: creates a new process which will be an exact (deep) copy of the current one except for the PC which will be at the address specified in the argument. (The VM will apply % IDX_MOD to the first argument)
lld, lldi, lfork: those instructions are the long versions of ld, ldi and fork, which means the addresses won't be truncated by IDX_MOD. (Since those instructions are "long" the modulo operation won't be applied on them)
nop: this operation does nothing, it can still take one argument and has a pcode so be careful on how you implement it.
All addresses are relative to the current PC of the process.

Some instructions have to truncate the addresses they are given by applying modulo IDX_MOD. This feature prevents players from reaching spaces in memory that are too far from the current PC, which means processes won't be able to attack each other straight away but will need to move by doing smaller steps, this help having more balanced games.

Labels
Labels are also called pseudo instructions, in the sense they won't be translated into binary code. They are widely used to help programmers creating assembly code without needing to remember and count relative memory positions.

So when looking in the binaries the labels will be replaced in order to match their relative position in bytes from the place they were used.

As an example in the ameba.s player zjmp %:hello is exactly the same of zjmp %-5 (-5 being ff fb in hexadecimal notation).

This is because from the zjmp you will need to go back 5 bytes in order to match the label hello: declaration.

Notice that labels can be declared at the start of an instruction or on their own line.

Pcode field
Some instruction may accept different kind of parameters, and those parameters may require different sizes. For those instructions the assembler will use a byte after the opcode to inform the VM which kind of parameters to expect.

Every two bits of this byte will inform the VM about the type of a parameter.

01 will be used for a register parameter.
10 will be used for a direct parameter.
11 will be used for an indirect parameter.
An example of a pcode byte could be 01111000:

Here the first parameter is expected to be a register, the second should be an indirect value and the third a direct value.

Those numbers are in binary base, for example 10 in binary is 2 in decimal.

Has IDX field
Some instruction will sum up direct values and/or use them as addresses to lookup into the arena memory space. For this reason we know that a full 32 bits integer won't be necessary and to save space we do save direct values on only two bytes in those cases.

You have Had Idx column in the Instruction Set table, when it is true you must save and read direct parameters on two bytes, when false they will be saved on four bytes as usual.

The Carry flag
In all processors you will find a carry which is a flag that notify some edge case states to allow special operations, ensure security and serve many others functionalities.

The Carry of the processes in the VM is in that sense a simplified version of this concept.

The following commands will modify the carry of the process executing them: ld, add, sub, and, or, xor.

Those commands will set the carry to true if the value written in the register is 0, and set it to false otherwise.

Only one command reads the carry and it's zjmp, it will do the jump if the carry is true and will do nothing otherwise.

The file signature
The signature, also called magic, of a file is a particular value written at the start of a binary. This signature has the role to help the OS understand which kind of file it is and how to execute it. For the .cor files the signature is provided in the config file.

Your player
While the main focus for now is to create an Asm and a VM, you should provide a basic player that should be able to fight and win against the ameba.s, this player will be tested in the provided VM during evaluation.

Resources
Config file
The specification requires a lot of constants, it would be tedious to define them in the subject every time they are mentioned.

For this reason we provide a config file which includes all the required constants. Some of them are specific to the Asm, some others to the VM, the vast majority will be helpful to both.

While the file is language agnostic, it would be very easy to translate it to a specific language. We suggest you to do so in order to centralize the configuration, it will help to debug and extend easily your programs if needed.

A basic player
To help understand better how the Asm and the VM works, let's deep dive into the assembly code with a very basic player:

.name "ameba"
.description "not doing much"

        sti r1,%:hello,%1
        and r1,%0,r1

hello:  live %1
        zjmp %:hello
This player is one of the most basic living things you can imagine, it just says it is alive.

Here is how it proceeds in order to do so:

The sti instruction will copy the value in the first register (which is the opposite of its id, so -1 if this player is the first one).
It is interesting that this number will be copied at the address in memory corresponding to the label hello + 1 byte.
Said in other words this instruction is changing the argument of the live statement.
The and instruction will copy the result of the AND logic operator into the first register.
This result will be zero since any number AND 0 is zero. This has the side effect of setting the carry of this process to true.
The live statement notify the VM the player is alive. Notice the specific value of the argument is irrelevant in this context since the sti instruction override it before the live is executed.
The zjmp brings the PC back to the start of the live instruction (creating a loop).
Notice zjmp only operates the jump if the carry is not equal to zero. This is why we executed the and instruction before.
Now let's see what this player binary looks like with hexdump -C ameba.cor:

00000000  00 ea 83 f3 61 6d 65 62  61 00 00 00 00 00 00 00  |....ameba.......|
00000010  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00  |................|
*
00000080  00 00 00 00 00 00 00 00  00 00 00 17 6e 6f 74 20  |............not |
00000090  64 6f 69 6e 67 20 6d 75  63 68 00 00 00 00 00 00  |doing much......|
000000a0  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00  |................|
*
00000890  0b 68 01 00 0f 00 01 06  64 01 00 00 00 00 01 01  |.h......d.......|
000008a0  00 00 00 01 09 ff fb                              |.......|
000008a7
All numbers are represented in hexadecimal.
At the start of each line there is the position of the first byte of the line in hexadecimal.
This means that for the second line 10 hex is actually 16 in decimal notation.
The right panel shows a . if the byte is not a valid ASCII character.
Repeated lines are aggregated under *, this is why we pass from 10 to 80.
Each xx is a byte (a byte is 8 bits, ff in hexadecimal is 1111 1111 in binary, 255 in decimal).
Let's read it through:

The first 4 bytes 00 ea 83 f3 are the program signature (also called the magic), this is a 32 bits integer, represented as four 8 bits slices.
Then the next 128 bits are for the name of the program, the first five 61 6d 65 62 61 are the actual name, the others are zeros because we don't actually need it.
Then we have 4 bytes 00 00 00 17 (so 23) which will be a 32 bits integer with the size in bytes of the program to be executed in the arena of the VM (so only the size of the instructions, without the name, description, signature and so on).
Then we have the description which works exactly the same as the name but it will add four bytes padding at the end of it.
The actual instructions start from there:

The instruction sti is made by those bytes: 0b 68 01 00 0f 00 01.
0b is the opcode, 68 is the pcode, 01 is the first parameter, 00 0f is the second parameter and 00 01 is the third parameter.
Notice the last two parameters are written on two bytes each because sti has Has Idx set to true.
Next instruction is and, and it is 06 64 01 00 00 00 00 01.
06 is the opcode, 64 is the pcode, the first parameter is 01, the second one is 00 00 00 00 and the third one is 01.
Here comes the live instruction: 01 00 00 00 01.
01 is the opcode, 00 00 00 01 is the first parameter, notice the parameter memory space will be overridden by the sti instruction.
The last instruction is zjmp: 09 ff fb.
01 is the opcode while ff fb is the first parameter (on two bytes because of Has Idx once again).
Testing environment
You will be provided with a playground containing:

A VM and an Assembler binaries to use as references.
A Dockerfile to run the binaries in a container.
Instructions on how to execute the references.
Some players to use as tests.
The provided VM has an extra flag -v which you can use to print the state of the VM at every cycle, this should greatly help you during development and debugging.

Some advices for the road
While the functioning of the VM and the Assembler may seem quite strange and obscure, it is a perfect scenario to reproduce a CPU and understand what a computer really does at its core.

In this project you are indeed creating a Turing Complete machine largely inspired by Von Neumann architecture (which is still how today's processors works).

Bonus
Those bonuses will be evaluated only if the Assembler and the VM are perfect. You can use external libraries (for example graphic libraries) in order to achieve them.

Create a disassembler that will take binary as input and return a .s file as output.
Create a visualizer to show what is happening in the VM in real time.
Introduce arithmetic operations support in the Assembly language.
Introduce a simple macro system in the Assembly language.
Resources
Turing Machine
Von Neumann Architecture



<img src="https://cdn.rawgit.com/emilwallner/Corewar/master/images/corewarlogo.svg" width="100%">

---

**Core War was inspired by a malicious virus written in the 80’s.** To deal with the self-replicating virus, a white hat hacker invented Reaper. It was a virus designed to spread and eliminate the malware. He fought fire with fire.

This inspired A. K. Dewdney to coin the idea for Core War.

The idea was simple. You compete by designing viruses to overtake a computer. You win by protecting your own program and overwriting your opponent's programs. This is all happening on a virtual computer. Think, a simple computer within your computer. 

This is what is looks like in action:

![Alt text](http://g.recordit.co/pyyAggYcWm.gif "Optional title")

Let’s focus on the high-level game dynamics: 

- **The game board**, the memory of our virtual computer. It’s represented in a 64 X 64 grid of bytes.
- **The players**, small programs represented in different colors. The white parts have blank memory.
- **Cursors**, the moving parts with inverted color. They read from the game board. 

The cursors have a couple of features. They can jump on the game board, store and write values, and clone themselves. The cursors belong to no-one, they just read from the game board.

When the game starts, all players have one cursor at the beginning of their program. The game advances as the cursors read the commands stored in their programs. If they end up on a faulty command or a blank memory, it moves to the next byte. 

Below, we see how the pink player starts by cloning their cursors. It then starts attacking the blue player.

![Alt text](http://g.recordit.co/Y9r9E78FVY.gif "Optional title")

Let’s get into a little bit more depth. 

Every byte you see, the pairs of numbers or letters, are represented in hexadecimal. Each hexadecimal has the value of a digit, from 0 - 255. 

There are 16 operations in the game. The operations decide if the cursor should jump to a different address, clone itself and so on. The first 16 hexadecimal are [**coding bytes**](https://docs.google.com/spreadsheets/d/1IuQv2yfx7ewS3d6H2KyDu7N9U67u3mhFBy8W1XUhxuM/edit?usp=sharing), they all store a different operation. The remaining digits, 17 - 255, are regular numbers. 

The coding byte decides which operation to execute and how many bytes to read. In the image above, the pink player goes to the hexadecimal **0c**. It's 12 in decimal, telling the cursor to clone itself. The two bytes after decide where the cloned cursor starts. 

![Alt text](http://g.recordit.co/XQdhVmqvrV.gif "Optional title")

There are three main components to determine who wins: 

- **Game rounds**, every game round is measured in cycles. It determines how much each cursor can read from the game board. 
- **Lives**, if a cursor reads a player life from the game board, they are given a life for that round. These are visualized in the colorful progress bars. 
- **Cycle to die**, for every game round, the number of bytes a cursor can read from the game board is reduced.

You win if your program is the last one to receive a life. 

There are more nuances to the game that I don’t cover. The best way to learn about them is to run it with the below code.  

<br>
<br>

## Executing Core War

Open your terminal and make it full-screen, copy paste the below, and hit enter.

```shell
git clone https://academy.digifemmes.com/git/bahouara/corewar.git ; 
cd Core-War ; 
make ; 
./corewar -visual ./Players/Compiled/bigzork.cor ./Players/Compiled/fluttershy.cor ./Players/Compiled/turtle.cor ./Players/Compiled/helltrain.cor
```
Once the cycle to die reaches 0, it will announce a winner. Then you can press any key to leave the program.

The usage for the Core War game: 

```
Usage:
 	Example: ./corewar -visual -n -1 filename.cor
 	GUI: ./corewar -visual [Players]
 	Default Player Numbers: 1, 2, 3, 4...
 	Change Player Number: -n -1 filename.cor
 	Dump: ./corewar -dump 300 [Players](prints the memory after 300 cycles)
 	Game on! 👾 🤖 🔫
```
If you want to create and then compile players, use the asm: 

```
./asm [filename.s]
```

<br>
<br>


## Technical Implementation Overview

I did Core War with three friends: @mhza, @Natansab, and @tlenglin. 

The Core War consists of players that are written in assembly, a compiler to turn them into binary, and the virtual computer to run the programs on. 

I focussed on the main architecture of the virtual machine (VM), the game dynamics, implementing three operations, handling the cursors and implementing the graphical user interface (GUI).  

The program is written using read, open, write, malloc, free and exit from **stdlib.h**. We used the **ncurses** library to create the GUI.  

This is an example of a simple player, a normal player has around 150 lines of assembly code:


```assembly
    .name "zork"
    .comment "I'M ALIIIIVE"
    
    l2:          sti r1,%:live,%1
                 and r1,%0,r1
    
    live:        live %1
                 zjmp %:live
```

This complies to binary which is later represented in hexadecimal. The above program looks like this when it’s compiled: 

```binary
    0000000 00 ea 83 f3 7a 6f 72 6b 00 00 00 00 00 00 00 00
    0000010 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
    *
    0000080 00 00 00 00 00 00 00 00 00 00 00 17 49 27 4d 20
    0000090 41 4c 49 49 49 49 56 45 00 00 00 00 00 00 00 00
    00000a0 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
    *
    0000890 0b 68 01 00 0f 00 01 06 64 01 00 00 00 00 01 01
    00008a0 00 00 00 01 09 ff fb
```

The first part includes an identification code and the name, followed by the comment, and ending with the program which will be stored in the memory of the virtual machine. 

<br>
<br>

## My Technical Implementation

As I mentioned earlier, I focussed on the main architecture of the virtual machine, the game dynamics, implementing three hexadecimal operations, handling the cursors and the GUI.  

This is the main I created for the VM:

```C
    int                        main(int ac, char **av)
    {
            t_env e;
    
            if (ac == 1)
                    ft_error_usage();
            init_e(&e, av);
            ft_parse_flags(&e, ac, av);
            ft_files_to_string(&e);
            init_players(&e);
            ft_parsing(&e);
            ft_build_arena(&e);
            ft_init_cursor(&e);
            ft_move_cursors(&e);
            ft_declare_winner(&e);
            ft_exit(&e, 0);
            return (0);
    }
```
It should give you a rough understanding of the main logic. We parse the flags entered in the terminal, turn the files into strings, initiate the players; parse the player data, set up the memory for the VM, initiate the cursors, run the game, declare the winners, and free the data.   

Below is the main struct and it gives you a rough idea what’s going on. 

```C
    typedef struct                s_env
    {
            t_player        player[MAX_PLAYERS + 1];
            t_cursor        *head;
            t_arena         a[MEM_SIZE];
            int             cursors;
            int             dump;
            int             dump_value;
            int             cycle;
            int             tot_cycle;
            int             lives;
            int             check;
            int             bonus;
            int             winner;
            int             last_alive;
            int             cycles_to_die;
            char            arena[MEM_SIZE];
            int             player_amount;
            char            **files;
            t_op            p_tab[17];
    }                             t_env;
```
The game board, the memory of the virtual machine, is stored in a static struct array, **t_arena**. It could have been an int array, but this enabled more flexibility to manage the GUI.  

The cursors are stored in a doubly linked list, it’s circular, and has an extra node to keep track of the beginning of the list. Every time a cursor reads the coding byte to clone itself, it adds a new cursor at the end of the list. The core structure performed well up to 10M cursors. 

I choose a function pointer to handle the 16 operations mentioned in the first section. This allowed us to easily work on different functions and access them when necessary. 

```C
    static void (*g_func_ptr[17])(t_env *e, t_cursor *cursor) =
    {
            ft_live, ft_live, ft_ld, ft_st, ft_add, ft_sub, ft_and, ft_or,
            ft_xor, ft_zjmp, ft_ldi, ft_sti, ft_fork, ft_lld, ft_lldi,
            ft_lfork, ft_aff
    };
```

