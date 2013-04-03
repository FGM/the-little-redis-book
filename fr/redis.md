# A propos de ce livre

## Licence

Le Petit Livre de Redis est distribué sous la licence Attribution-NonCommercial 
3.0 Unported. Vous ne devriez pas avoir eu à payer pour ce livre.

Vous êtes libre de copier, distribuer, modifier et afficher ce livre. Néanmoins, 
nous vous demandons de toujours attribuer ce livre en tant à Karl Seguin en tant 
qu'auteur et Frédéric G. Marand en qualité de traducteur et de ne pas l'utiliser 
à des fins commerciales. 

Le *texte intégral* de **la licence** est disponible sur:

<http://creativecommons.org/licenses/by-nc/3.0/legalcode>

## A propos de l'auteur

Karl Seguin est un développeur dont le champ d'expérience couvre de multiples
champs et technologies. C'est un contributeur de multiples projets Open Source,
un rédacteur technique et à l'occasion un conférencier. Il est l'auteur de 
multiples articles, ainsi que de quelques outils autour de Redis. Redis est le
moteur de classement et statistiques de son service gratuit pour les développeurs
de jeux amateurs: [mogade.com](http://mogade.com/).

Karl a aussi écrit 
[The Little MongoDB Book](http://openmymind.net/2011/3/28/The-Little-MongoDB-Book/), 
son populaire ouvrage gratuit à propos de MongoDB.

Son blog se trouve sur <http://openmymind.net> et il tweete en tant que
[@karlseguin](http://twitter.com/karlseguin)

## A propos du traducteur

Frédéric G. Marand est un développeur qui passe l'essentiel de son temps à la
maintenance du CMS Drupal et au conseil en performance et sécurité autour de cet
outil, dont il est un contributeur core depuis 2005. Dans une vie antérieure, sa 
spécialité était le protocole NFS, dont il a gardé une affinité particulière 
pour les mécanismes de RPC, comme XML-RPC.

Le monde Drupal le connaît comme `fgm`, son blog se trouve sur 
<http://blog.riff.org>, et il tweete en tant que 
[@osinet](http://twitter.com/osinet)

## Remerciements de l'auteur

Merci tout particulièrement à [Perry Neal](https://twitter.com/perryneal) pour
m'avoir prêté son regard, sa réflexion, et sa passion. Tu m'as fourni une aide
inappréciable. Merci.

## Version courante

La version la plus récente de ce livre est disponible sur:
<http://github.com/karlseguin/the-little-redis-book>

# Introduction

Ces dernières années, les techniques et outils utilisés pour le stockage et 
l'accès aux données ont évolué à une cadence incroyable. Même s'il est 
raisonnable d'affirmer que les bases de données relationnelles ne sont pas près 
de disparaître, il est tout aussi certain que l'écosystème entourant les données
ne restera pas figé.

Parmi tous les nouveaux outils et les nouvelles solutions, de mon point de vue,
Redis a été le plus excitant. Pourquoi ? En premier lieu parce qu'il est
incroyablement simple à apprendre. C'est en heures qu'il faut compter pour 
évaluer le temps nécessaire à se sentir à l'aise avec Redis. En second lieu, il
traite un ensemble de problèmes bien spécifique tout en demeurant dans le même
temps assez général. Qu'est-ce à dire ? Simplement que Redis n'essaie pas d'être
le solution à tous les problèmes liés à tous les types de données. Mieux vous
connaissez Redis, plus le choix de ce qu'il est approprié d'y trouver - ou pas -
devient évident. Et pour un développeur, un tel degré d'évidence est une 
expérience gratifiante. 

Bien qu'il soit possible de construire un système complet uniquement avec Redis,
je pense que la plupart des gens découvriront qu'il vient en complément de leur
solution de données plus générale - que ce soit une base de données 
relationnelle traditionnelle, une base de données orientée document, ou autre 
chose encore. Redis est le type de solution que l'on utilise pour implémenter 
une fonction spécifique. En ce sens, il est similaire à un moteur d'indexation. 
Personne n'envisagerait de construire une application entière sur Lucene; mais
lorsqu'un bon moteur de recherche est nécessaire, utiliser Lucene est bien plus
approprié qu'un système d'accès aux données générique - tant pour le développeur
que pour les utilisateurs. Bien entendu, là s'arrêtent les similarités entre
Redis et les moteurs d'indexation. 

Le but de ce livre est d'établir les fondations dont vous aurez besoin pour
maîtriser Redis. Nous nous focaliserons sur l'apprentissage des cinq structures
de données de Redis et examinerons diverses approches de modélisation des 
données. Enfin, nous aborderons quelques détails d'administration système et
techniques de débogage.

# Démarrer avec Redis

Chacun a sa façon d'apprendre: certains préfèrent mettre les mains dans le 
cambouis, d'autres préfèrent regarder des vidéos, d'autres encore préfèrent 
lire. Rien ne vous aidera à comprendre Redis autant que de réellement 
l'utiliser. Redis est facile à installer et comporte un interpréteur simple 
fournissant tout le nécessaire. Prenons quelques minutes pour le mettre en 
service sur notre machine. 

## Sur Windows

Redis lui-même n'est pas officiellement soutenu sur Windows, mais certaines
options sont disponibles. Il n'est pas question de les utiliser en production,
mais je n'ai personnellement jamais rencontré de limites en développement.

Un portage réalisé par Microsoft Open Technologies, Inc. est disponible sur
<https://github.com/MSOpenTech/redis>. A la date d'écriture de ce livre, cette 
solution n'est pas prête à être déployée en production.

Une autre solution, déjà disponible depuis quelques années, est disponible sur
<https://github.com/dmajkic/redis/downloads>. Vous pouvez télécharger la version
la plus récente (en haut de la liste). Décompressez le fichier zip et, selon
l'architecture de votre machine, ouvrez le répertoire `64bit` ou `32bit`.

## Sur \*nix et MacOSX

Pour les utilisateurs de systèmes \*nix et Mac, installer à partir des sources
est la meilleure option. Les instructions, ainsi que le numéro de la version la
plus récente, sont disponibles sur <http://redis.io/download>. A la date de
rédaction de ce livre, la version la plus récente est la 2.6.2; pour 
l'installer, nous exécuterions donc:

    wget http://redis.googlecode.com/files/redis-2.6.2.tar.gz
    tar xzf redis-2.6.2.tar.gz
    cd redis-2.6.2
    make

(A titre d'alternative, Redis est disponible dans plusieurs gestionnaires de
paquets. Par exemple, les utilisateurs de MacOSX disposant de Homebrew peuvent
se contenter de taper `brew install redis`.)

Si vous avez installé Redis depuis les sources, les binaires résultants ont été
placés dans le répertoire `src`. Rendez vous dans le répertoire `src` par la
commande `cd src`.

## Lancer Redis et s'y connecter

Si les étapes précédentes ont réussi, les binaires Redis sont dorénavant à
votre disposition. Redis en comporte plus d'un. Nous porterons notre 
attention sur le serveur Redis et l'interface ligne de commande de Redis (un
client de type DOS). Démarrons le serveur. Sous Windows, ce sera par un double 
clic sur `redis-server`. Sur \*nix/MacOSX en exécutant `./redis-server`.

Si vous prenez le temps de lire le message de démarrage, vous remarquerez un 
avertissement signalant que le fichier `redis.conf` est introuvable. En son
absence, Redis utilise les valeurs par défaut, ce qui est tout à fait adéquat
pour ce que nous allons en faire.

Lancez maintenant la console Redis en double-cliquant sur `redis-cli` (Windows) 
ou en exécutant `./redis-cli` (\*nix/MacOSX). Celle-ci va se connecter au
serveur en cours d'exécution sur le port par défaut (6379) de la machine locale.

Vous pouvez vous assurer que tout est en ordre en entrant la commande `info` à
la console. Vous devriez obtenir en retour un lot de paires clef-valeur 
fournissant bon nombre d'indicateurs sur l'état du serveur.

Si vous rencontrez des difficultés avec ce déploiement, je vous suggère de 
chercher de l'aide auprès du
[groupe officiel de soutien Redis](https://groups.google.com/forum/#!forum/redis-db).

# Pilotes Redis

Comme vous allez bientôt l'apprendre, la meilleure description de l'API de Redis
est un jeu de fonctions explicites, avec un caractère élémentaire et procédural.
Cela signifie que les choses sont très semblables entre la console en ligne de
commande et le pilote disponible dans votre langage favori. Vous ne devriez donc
pas rencontrer de difficultés à suivre ce livre dans votre langage de
prédilection plutôt qu'à la console. Si tel est votre choix, orientez-vous vers
la [page des clients](http://redis.io/clients) et téléchargez le pilote 
approprié.

# Chapitre 1 - Les bases

En quoi Redis est-il si particulier ? Quels types de problèmes résout-il ? A 
quoi faut-il faire attention en tant que développeur dans son utilisation ? 
Avant de pouvoir répondre à ces questions, nous devons comprendre ce qu'est 
Redis.

Redis est souvent décrit comme un stockage clef-valeur en mémoire persistant. Je
ne pense pas que ce soit une description exacte. Redis conserve bien toutes les
données en mémoire (plus de détails dans un moment), et écrit bien tout cela sur
disque pour la persistance, mais il est bien plus qu'un simple stockage 
clef-valeur. Il est important de dépasser cette erreur d'appréciation, sans quoi
votre perspective sur Redis et les problèmes qu'il résout sera trop étroite. 

Dans les faits, Redis expose cinq structures de données distinctes, dont une 
seule est typique des systèmes clef-valeur. C'est en comprenant ces cinq 
structures de données, comment elles fonctionnent, quelles méthodes elles
exposent, et ce qu'il est possible de modéliser avec qu'il est possible de
vraiment comprendre Redis. Pour commencer, penchons-nous sur ce que signifie
exposer des structures de données.

Si nous devions appliquer ce concept de structure de données au monde 
relationnel, nous pourrions dire que les bases de données exposent une seule et
unique structure de données: la table. Les tables sont à la fois complexes et
flexibles. Il n'y a pas grand chose qu'elles ne permettent pas de modéliser, 
enregistrer ou manipuler. Mais leur nature générique n'est pas sans 
inconvénients. En particulier, une représentation à base de tables n'est pas
toujours aussi simple ou aussi rapide que possible. Qu'en serait-il si, au lieu
d'avoir une structure à tout faire, nous utilisions des structures plus 
spécialisées ? Il se pourrait que certaines choses ne soient plus possibles, ou
du moins plus possibles efficacement, mais nous pourrions certainement gagner en
simplicité et en rapidité.

Utiliser des structures de données pour des problèmes spécifiques ? N'est-ce pas
précisément comment nous programmons ? Personne n'utilise un tableau associatif
pour tous les types de données, pas plus qu'une variable scalaire. Pour moi, 
c'est ce point qui définit l'approche de Redis. Si vous manipulez des scalaires,
des listes, des tableaux associatifs, ou des ensembles, pourquoi ne pas les
enregistrer comme des scalaires, des listes, des tableaux associatifs, ou des 
ensembles ? Pourquoi vérifier l'existence d'une valeur devrait-il être plus
complexe que d'appeler `exists(clef)` ou plus lent que O(1) (un accès en temps 
constant qui ne va pas ralentir quel que soit le nombre de données présentes) ?

# Les briques de base

## Bases de données

Le concept de base de données dans Redis est le même que celui qui vous est déjà 
familier. Une base de données contient un jeu de données. Le cas d'utilisation
typique d'une base de données consiste à regrouper toutes les données d'une même
application et à les séparer des données des autres applications.

Dans Redis, les bases de données sont simplement identifiées par un numéro, la
base par défaut portant le numéro `0`. Vous pouvez changer de base de données
active avec la commande `select`. Dans la console Redis, entrez `select 1`. 
Redis devrait vous répondre avec un message d'acquiescement et votre invite se
modifier en quelque chose comme `redis 127.0.0.1:6379[1]>`. Pour revenir à la
base par défaut, entrez simplement `select 0` à la console.

## Commandes, clefs et valeurs.

Bien que Redis soit plus qu'un stockage clef-valeur, tout au fond, chacune des 
cinq structures de données de Redis contient au moins une clef et une valeur. Il 
est impératif de comprendre les clefs et les valeurs avant de progresser vers 
les autres types d'informations.

Les clefs sont l'identifiant des éléments de données. Nous allons faire beaucoup
avec les clefs, mais pour l'instant il suffit de savoir qu'une clef peut 
ressembler à `users:leto`. Il serait raisonnable de s'attendre à ce qu'une telle
clef soit associée aux informations concernant un utilisateur dénommé `leto`. Le
symbole deux points n'a pas de signification particulière en ce qui concerne
Redis, mais l'utilisation d'un séparateur est une approche que beaucoup de gens
utilisent pour organiser leurs clefs.

Les valeurs représentent les données associées à la clef. Elles peuvent être 
tout et n'importe quoi. Parfois ce seront des chaînes, parfois des entiers, 
parfois des objets sérialisés (en JSON, XML, ou d'autres formats). Pour
l'essentiel, Redis traite les valeurs comme un tableau d'octets et ne se
préoccupe pas de ce qu'elles sont. Attention au fait que les divers pilotes
existants sont susceptibles de réaliser la sérialisation de façon différente 
les uns des autres, certains reportant la responsabilité de la sérialisation au
développeur; dans ce livre nous ne parlerons donc que des données chaînes, 
des entiers et des JSON.

Plongeons un peu les mains dans le cambouis. Entrez la commande suivante:

    set users:leto "{name: leto, planet: dune, likes: [spice]}"

Ceci est l'anatomie de base d'une commande Redis. Elle commence par la commande
proprement dite, dans le cas présent `set`. Viennent ensuite ses paramètres. La
commande `set` attend deux paramètres: la clef que nous définissons, et la 
valeur que nous donnons à cette clef. La plupart des commandes, mais pas toutes,
attendent un paramètre de clef (et lorsque c'est le cas, c'est souvent le 
premier paramètre). Pouvez-vous deviner comment retrouver cette valeur ?
Espérons que vous avez répondu (mais ne vous inquiétez pas si vous aviez un 
doute):

    get users:leto

Continuez en jouant avec d'autres combinaisons. Les clefs et valeurs sont des
concepts fondamentaux, et les commandes `get` et `set` sont le moyen le plus
simple qui soit de jouer avec. Créez d'autres utilisateurs, essayez d'autres
types de clefs, d'autres valeurs.

## Requêtes

En progressant, deux points se clarifient: du point de vue de Redis, les clefs
sont tout et les valeurs ne sont rien. Ou, autrement dit, Redis ne vous permet
pas de requêter sur les valeurs des objets. Une conséquence est que nous ne
pouvons pas trouver le ou les utilisateurs qui vivent sur la planète `dune`.

Pour beaucoup, cela est un souci. Nous avons toujours vécu dans un monde où
l'interrogation des données est si flexible et puissante que l'approche de Redis
semble primitive et manquant de pragmatisme. Ne laissez pas cela vous perturber
indûment. Souvenez-vous que Redis n'est pas une solution à tout faire. Il y aura
des choses qui n'y auront pas leur place (à cause des limitations de requêtage).
Prenez en considération le fait que dans certains cas vous trouverez d'autres
façons de modéliser vos données. 

Nous examinerons d'autres exemples concrets au fil de cet ouvrage, mais il est
important de bien comprendre cette réalité de base de Redis. Elle nous aide à
comprendre pourquoi les données peuvent être de toutes sortes - Redis n'a jamais
besoin de les lire ou les comprendre. Elle nous permet aussi de lancer nos
réflexions sur la modélisation des données dans ce nouveau monde.

## Mémoire et persistance

Nous avons mentionné précédemment que Redis est un stockage de données en 
mémoire et persistant. Pour ce qui est de la persistance, par défaut Redis prend
un cliché de la base de données sur disque en fonction du nombre de clefs 
modifiées. Vous pouvez le configurer pour que, lorsqu'un nombre X de clefs a 
changé, Redis enregistre la base de données toutes les Y secondes. Par défaut,
Redis enregistre la base de données toutes les 60 secondes si 1000 clefs ou plus
ont changé, et monte jusqu'à 15 minutes sur 9 clefs ou moins ont changé.

A titre d'alternative (ou en addition aux clichés), Redis peut fonctionne en 
mode ajout. Chaque fois qu'une clef change, un fichier ouvert en ajout seul est
modifié sur disque. Dans certains cas, il est acceptable de perdre 60 secondes
de données en contrepartie d'une bonne performance, en cas de panne matérielle
ou logicielle. Dans d'autres cas, cela n'est pas acceptable. Redis vous permet
de choisir. Au chapitre 6 nous aborderons une troisième voie, qui consiste à
déporter la persistance à un serveur esclave.

Pour ce qui est de la mémoire, Redis conserve toutes vos données en mémoire.
L'implication évidente de ceci est le coût de l'utilisation de Redis: la mémoire
vive est toujours la part la plus coûteuse du matériel des serveurs.

J'ai le sentiment que certains développeurs ont perdu le contact avec le peu 
d'espace que les données peuvent nécessiter. Les &oelig;uvres complètes de 
William Shakespeare occupent environ 5.5&nbsp;Mo de stockage. Pour ce qui est de 
la capacité de monter en charge, d'autres solutions tendent à être limitées par 
les E/S ou le processeur. Laquelle de ces deux limitations (mémoire ou E/S) vous
forcera à étaler votre application sur un plus grand nombre de machines dépend
fondamentalement du type de données manipulées et de la façon dont vous les
stockez et les requêtes. A moins que vous n'enregistriez de lourds fichiers
multimédia dans Redis, l'aspect &laquo;en mémoire&raquo; est probablement un
faux problème. Pour les applications pour lesquelles cela est un problème, il 
est probable que vous ne ferez que passer de l'état &laquo;limité par les 
E/S&raquo; à l'état &laquo;limité par la mémoire&raquo;. 

Redis a ajouté le soutien de la mémoire virtuelle. Néanmoins cette capacité a 
été considérée comme un échec (par les propres développeurs de Redis) et son
utilisation est dorénavant déconseillée. 

(Accessoirement, remarque que le fichier de 5.5 Mo des &oelig;uvres complètes de
Shakespeare peut être compressé à environ 2 Mo. Redis ne pratique pas la 
compression automatique mais, puisqu'il traite les valeurs comme des octets, il
n'y a rien pour vous empêcher de compresser/décompresser vos données 
vous-mêmes.)

## Vision globale

Nous avons abordé de multiples sujets de haut niveau. La dernière chose que je
tiens à faire avant de plonger dans Redis est de relier certains de ces sujets
entre eux, et en particulier les limitations de requêtage, les structures de
données, et la façon dont Redis enregistre les données en mémoire.

La combinaison de ces facettes délivre quelque chose de merveilleux: la vitesse.
Certains vont dire &laquo;C'est bien normal que Redis soit rapide, tout est en
mémoire&raquo;. Mais ce n'est qu'une part de l'équation. La vraie raison pour
laquelle Redis se distingue par rapport à d'autres solutions est l'existence de
ses structures de données spécialisées.

Rapide jusqu'à quel point ? Cela dépend de bien des choses - quelles commandes
vous utilisez, quels types de données, et ainsi de suite. Mais le niveau de
performance de Redis tend à s'évaluer en dizaines de milliers, voire centaines
de milliers d'opérations **par seconde**. Vous pouvez utiliser `redis-benchmark` 
(qui est disponible dans le même répertoire que `redis-server` et `redis-cli`)
pour le mesurer vous même.

J'ai une fois modifié du code qui utilisait un modèle de données traditionnel
pour utiliser Redis. Un test de montée en charge que j'avais écrit prenait 5
minutes à s'exécuter en utilisant le modèle relationnel. Il prenait environ 
150ms sur Redis. Il n'est pas toujours possible d'obtenir des gains aussi 
massifs, mais cela peut au moins vous donner une idée de l'ordre de grandeur des
accélérations dont nous parlons.

Comprendre cet aspect de Redis est important parce qu'il impacte votre façon
d'interagir avec lui. Les développeurs venus d'un environnement SQL s'efforcent
souvent de minimiser le nombre d'aller/retour qu'ils font vers la base de 
données. C'est une bonne pratique pour tous les systèmes, Redis compris. 
Néanmoins, puisque nous traitons des structures de données plus simples, nous
allons parfois devoir interroger le serveur Redis à de multiples reprises pour
atteindre nos objectifs. Ce type de motif d'accès aux données peut sembler
anti-naturel au début, mais dans la réalité, son coût tend à être insignifiant
en comparaison avec les gains bruts de performance qu'il apporte. 

## Dans ce chapitre

Bien que nous ayons à peine commencé à jouer avec Redis, nous avons déjà couvert
un vaste éventail de sujets. Ne vous inquiétez pas si quelque chose n'est pas
totalement limpide - comme le requêtage. Dans le chapitre suivant, nous allons
plonger dans le vif du sujet et vos interrogations devraient se résoudre 
d'elles-mêmes.

Ce qu'il faut retenir de ce chapitre:

* Le clefs sont des chaînes de caractères qui identifient des données (valeurs)
* Les valeurs sont des tableaux d'octets arbitraires que Redis n'interprète pas
* Redis expose (et est mis en &oelig;uvre comme) cinq structures de données 
spécialisées
* Ces trois points combinés font que Redis est rapide et simple d'utilisation,
mais pas adapté à tous les scénarios d'utilisation possibles.

# Chapitre 2 - Les structures de données

Il est temps d'examiner les cinq structures de données de Redis. Nous allons
détailler pour chacun en quoi elle consiste, quelles méthodes sont disponibles
et à quel type de fonctionnalité ou données elle est bien adaptée.

Jusqu'ici, les seules constructions Redis que nous avons découvertes sont les
commandes, les clefs et les valeurs. Rien de concret n'a été présenté à propos
des structures de données. Lorsque nous avons utilisé la commande `set`, comment
Redis a-t-il su quelle structure de données utiliser ? En fait, chaque commande
est spécifique à une structure de données. Ainsi, lorsque vous utilisez `set`,
c'est pour enregistrer la valeur dans une structure de données chaîne. Lorsque 
vous utilisez `hset`, c'est pour l'enregistrer dans un tableau associatif. 
Compte tenu de la faible taille du vocabulaire de Redis, cela demeure tout à 
fait gérable.

**[Le site web de Redis](http://redis.io/commands) contient une documentation de
référence de grande qualité: il ne servirait à rien de la reproduire ici. Nous
ne couvrirons donc que les commandes les plus importantes pour comprendre le
dessein d'une structure de données en particulier.**

Il n'y a rien de plus important que de prendre plaisir à essayer les choses. 
Vous pouvez toujours effacer toutes les données de votre base de données avec la
commande `flushdb`, alors ne soyez pas timides et lâchez vous!

## Les chaînes 

Les chaînes sont la structure de données la plus basique disponible dans Redis. 
Lorsque vous pensez à une paire clef-valeur, c'est à une chaîne que vous pensez. 
Ne vous fiez pas à leur nom : comme toujours, la valeur peut être quelconque. Je 
préfère les appeler &laquo;scalaires&raquo;, mais ce n'est que mon avis 
personnel.

Nous avons déjà rencontré un cas d'utilisation courant pour les chaînes : 
l'enregistrement d'instances d'objets identifiés par une clef. C'est là quelque 
chose dont vous ferez un usage massif :

    set users:leto "{name: leto, planet: dune, likes: [spice]}"

En complément, Redis vous permet de réaliser quelques opérations courantes. 
Ainsi, `strlen <key>` renvoie la longueur de la valeur associée à une clef ; 
`getrange <key> <start> <end>` renvoie une sous-chaîne d'une valeur ; 
`append <key> <value>` ajoute la valeur à la valeur préexistante (ou la crée si 
elle n'existe pas encore). Allez-y, essayez-les. Voici ce que j'obtiens:

    > strlen users:leto
    (integer) 42

    > getrange users:leto 27 40
    "likes: [spice]"

    > append users:leto " OVER 9000!!"
    (integer) 54

A ce stade, vous vous dites probablement que tout cela est bel et bon, mais n'a 
aucun sens. Vous ne pouvez pas extraire une sous-chaîne d'un JSON ou lui ajouter 
une valeur. Vous êtes dans le vrai, la leçon à en retirer est que certaines des 
commandes, en particulier liées à la structure de données chaîne, n'ont de sens 
que pour certains types de données.

Nous avons appris précédemment que Redis ne s'occupe pas de vos valeurs. La 
plupart du temps, c'est exact. Néanmoins, un petit nombre de commandes liées aux 
chaînes sont spécifiques à certains types de structures de valeurs. A titre 
d’exemple vague, je verrais bien les commandes `append` et `getrange` être 
utiles à certaines sérialisations personnalisées pour être efficaces en 
occupation d'espace. Comme exemple plus concret, considérez les commandes 
`incr`, `incrby`, `decr` et `decrby`. Elles ont pour effet d'incrémenter ou 
décrémenter la valeur d'une chaîne.:

    > incr stats:page:about
    (integer) 1
    > incr stats:page:about
    (integer) 2

    > incrby ratings:video:12333 5
    (integer) 5
    > incrby ratings:video:12333 3
    (integer) 8

Comme vous pouvez l'imaginer, les chaînes Redis sont idéales pour 
l'enregistrement de statistiques de fréquentation. Essayez d'incrémenter 
`users:leto` (dont la valeur n'est pas un entier) et observez ce qui se passe 
(vous devriez obtenir un message d'erreur).

Les commandes `setbit` et `getbit` sont le support d'un cas d'utilisation 
remarquable. Il y a un 
[article merveilleux](http://blog.getspool.com/2011/11/29/fast-easy-realtime-metrics-using-redis-bitmaps/) 
sur la façon dont Spool utilise ces deux commandes pour répondre de façon 
efficace à la question &laquo;combien de visiteurs uniques avons-nous eu 
aujourd'hui?&raquo;. Pour 128 millions d'utilisateurs, un portable renvoie la 
réponse en moins de 50ms et nécessite moins de 16Mo de mémoire.

Comprendre la façon dont les masques de bits fonctionnent, ou comment Spool les 
utilise, n'est pas l'important. Ce qui l'est, c'est de comprendre que les 
chaînes Redis sont plus puissantes qu'elles n'en ont l'air au départ. Néanmoins, 
les cas d'utilisation les plus courants sont ceux que nous avons listés 
précédemment : enregistrer des objets (complexes ou non) et des compteurs. 
Enfin, puisque l'accès à une valeur à partir de sa clef est si rapide, les 
chaînes sont souvent utilisées comme antémémoire de données.

## Les tableaux associatifs

Les tableaux associatifs sont un bon exemple des raisons pour lesquelles 
désigner Redis comme un stockage clef-valeur n'est pas pleinement exact. 
Voyez-vous, par bien des aspects, les tableaux associatifs sont comme les 
chaînes. La différence de taille entre les deux est le fait qu'ils fournissent 
un niveau d'indirection supplémentaire: un champ. De ce fait, les équivalents de
`set` et `get` pour les tableaux associatifs sont:

	hset users:goku powerlevel 9000
	hget users:goku powerlevel

Nous pouvons aussi affecter plusieurs champs à la fois, relire plusieurs champs 
à la fois, récupérer tous les champs et toutes les valeurs, lister tous les
champs, ou détruire un champ spécifique:

	hmset users:goku race saiyan age 737
	hmget users:goku race powerlevel
	hgetall users:goku
	hkeys users:goku
	hdel users:goku age

Comme vous pouvez le voir, les tableaux associatifs nous apportent un peu plus 
de contrôle que les simples chaînes. Plutôt que de stocker l'utilisateur comme
une valeur sérialisée unique, nous pourrions utiliser un tableau associatif pour
obtenir une représentation plus précise. L'avantage en serait l'aptitude à 
retirer et mettre à jour/supprimer des parties spécifiques des données, sans 
avoir à récupérer ou enregistrer la donnée entière.

C'est en considérant les tableaux associatifs sous l'angle d'un objet bien 
défini, comme un utilisateur, que l'on peut comprendre comment ils fonctionnent.
Et il est vrai que, pour des raisons de performances, il pourrait être utile de 
disposer d'une granularité de contrôle plus fine. Néanmoins, au prochain 
chapitre, nous verrons comment les tableaux associatifs peuvent être utilisés 
pour organiser vos données et rendre le requêtage plus pratique. A mon sens, 
c'est là le point fort des tableaux associatifs.

## Les listes

Les listes vous permettent d'enregistrer et manipuler un tableau de valeurs
associé à une clef. Vous pouvez ajouter des valeurs à la liste, récupérer la
première ou la dernière valeur, et manipuler les valeurs à un indice donné. Vous
Les listes restent ordonnées et disposent d'opérations efficaces basées sur les
indices. Nous pourrions créer une liste `newusers` qui conserve la trace des
derniers utilisateurs inscrits sur le site:

	lpush newusers goku
	ltrim newusers 0 49

Nous commençons par pousser un nouvel utilisateur à l'avant de la liste, puis 
nous le retaillons pour qu'elle ne contienne que les 50 derniers utilisateurs
inscrits. C'est là un motif courant. `ltrim` est une opération en O(N), où N 
est le nombre de valeurs à supprimer. Dans ce cas précis, où nous retaillons 
toujours après une seule insertion, sa performance sera donc constante en O(1)
(puisque N sera toujours égal à 1).

C'est aussi la première fois que nous voyons la valeur associée à une clef
référencer la valeur associée à une autre. Si nous voulions récupérer les 
détails des 10 derniers inscrits, nous exécuterions la combinaison suivante:

	keys = redis.lrange('newusers', 0, 10)
	redis.mget(*keys.map {|u| "users:#{u}"})

Le fragment de code Ruby ci-dessus illustre le type d'aller-retours évoqués plus
haut.

Bien entendu, les listes ne sont pas seulement bonnes à conserver des références
vers d'autres clefs. Les valeurs peuvent être de toute nature. Vous pourriez
utiliser les listes pour enregistrer des journaux d'accès, ou le parcours d'un 
utilisateur sur un site. Si vous êtes créateur de jeux, vous pourriez utiliser 
une liste pour garder la trace des actions en attente pour l'utilisateur.

## Les ensembles

Les ensembles servent à enregistrer des valeurs uniques et fournissent un 
certain nombre d'opérations ensemblistes, comme l'union. Les ensembles ne sont
pas ordonnés, mais ils fournisseurs des opérations efficaces sur les valeurs.
Un exemple classique d'utilisation d'un ensemble est une liste d'amis:

	sadd friends:leto ghanima paul chani jessica
	sadd friends:duncan paul jessica alia

Quel que soit le nombre d'amis d'un utilisateur, nous pouvons dire efficacement
(O(1)) si `userX` est un ami de `userY` ou non:

	sismember friends:leto jessica
	sismember friends:leto vladimir

De plus, nous pouvons voir si deux utilisateurs ou plus partagent les mêmes 
amis:

	sinter friends:leto friends:duncan

...et même enregistrer le résultat sous une nouvelle clef:

	sinterstore friends:leto_duncan friends:leto friends:duncan

Les ensembles sont pratiques pour l'étiquetage (tagging) ou le suivi des
propriétés d'une valeur pour lesquelles les doublons n'auraient pas de sens (ou
sur lesquelles nous voulons appliquer des opérations ensemblistes comme les
intersections et les unions).

## Ensembles ordonnés

La dernière et la plus puissante des structures de données de Redis est
l'ensemble ordonné. Si l'on veut comparer les tableaux associatifs à des chaînes
pourvues de champs, les ensembles ordonnés sont comme des ensembles pourvus de 
scores. Le score fournit des mécanismes de tri et ordonnancement. Pour obtenir
une liste d'amis, nous pourrions procéder comme ceci:

	zadd friends:duncan 70 ghanima 95 paul 95 chani 75 jessica 1 vladimir

Et pour trouver combien `duncan` a d'amis avec un score de 90 ou plus ?

	zcount friends:duncan 90 100

Ou bien connaître le rang de classement de `chani` ?

	zrevrank friends:duncan chani

Nous utilisons `zrevrank` au lieu de `zrank` car le tri par défaut de Redis est
par score croissant, alors que dans cet exemple nous trions par score
décroissant. Le cas d'utilisation le plus évident des ensembles ordonnés est un
système de tableau de classement de jeu. Dans la réalité, toutefois, toute 
donnée susceptible d'être triée par un nombre entier et que vous souhaitez
manipuler sur la base de cet entier, est une bonne candidate pour une 
représentation en ensemble ordonné.

## Dans ce chapitre

Nous avons survolé de loin les cinq structures de données de Redis. L'un des
aspects plaisants de Redis est que l'on peut souvent réaliser plus qu'on ne
l'imagine au premier abord. Il y a certainement des façons d'utiliser les 
chaînes et les ensembles ordonnés auxquelles personne n'a encore pensé. Pour 
autant, dès lors que vous comprenez le cas d'utilisation normal, vous pouvez
découvrir que Redis est idéal pour des problèmes de toutes sortes. De même, ce
n'est pas parce que Redis expose cinq structures de données et de nombreuses
méthodes que vous avez besoin de toutes les utiliser. Il n'est pas rare de
réaliser une fonctionnalité en n'utilisant que quelques commandes.

# Chapter 3 - Leveraging Data Structures

In the previous chapter we talked about the five data structures and gave some examples of what problems they might solve. Now it's time to look at a few more advanced, yet common, topics and design patterns.

## Big O Notation

Throughout this book we've made references to the Big O notation in the form of O(n) or O(1). Big O notation is used to explain how something behaves given a certain number of elements. In Redis, it's used to tell us how fast a command is based on the number of items we are dealing with.

Redis documentation tells us the Big O notation for each of its commands. It also tells us what the factors are that influence the performance. Let's look at some examples.

The fastest anything can be is O(1) which is a constant. Whether we are dealing with 5 items or 5 million, you'll get the same performance. The `sismember` command, which tells us if a value belongs to a set, is O(1). `sismember` is a powerful command, and its performance characteristics are a big reason for that. A number of Redis commands are O(1).

Logarithmic, or O(log(N)), is the next fastest possibility because it needs to scan through smaller and smaller partitions. Using this type of divide and conquer approach, a very large number of items quickly gets broken down in a few iterations. `zadd` is a O(log(N)) command, where N is the number of elements already in the set.

Next we have linear commands, or O(N). Looking for a non-indexed column in a table is an O(N) operation. So is using the `ltrim` command. However, in the case of `ltrim`, N isn't the number of elements in the list, but rather the elements being removed. Using `ltrim` to remove 1 item from a list of millions will be faster than using `ltrim` to remove 10 items from a list of thousands. (Though they'll probably both be so fast that you wouldn't be able to time it.)

`zremrangebyscore` which removes elements from a sorted set with a score between a minimum and a maximum value has a complexity of O(log(N)+M). This makes it a mix. By reading the documentation we see that N is the number of total elements in the set and M is the number of elements to be removed. In other words, the number of elements that'll get removed is probably going to be more significant, in terms of performance, than the total number of elements in the set.

The `sort` command, which we'll discuss in greater detail in the next chapter has a complexity of O(N+M*log(M)). From its performance characteristic, you can probably tell that this is one of Redis' most complex commands.

There are a number of other complexities, the two remaining common ones are O(N^2) and O(C^N). The larger N is, the worse these perform relative to a smaller N. None of Redis' commands have this type of complexity.

It's worth pointing out that the Big O notation deals with the worst case. When we say that something takes O(N), we might actually find it right away or it might be the last possible element.


## Pseudo Multi Key Queries

A common situation you'll run into is wanting to query the same value by different keys. For example, you might want to get a user by email (for when they first log in) and also by id (after they've logged in). One horrible solution is to duplicate your user object into two string values:

	set users:leto@dune.gov "{id: 9001, email: 'leto@dune.gov', ...}"
	set users:9001 "{id: 9001, email: 'leto@dune.gov', ...}"

This is bad because it's a nightmare to manage and it takes twice the amount of memory.

It would be nice if Redis let you link one key to another, but it doesn't (and it probably never will). A major driver in Redis' development is to keep the code and API clean and simple. The internal implementation of linking keys (there's a lot we can do with keys that we haven't talked about yet) isn't worth it when you consider that Redis already provides a solution: hashes.

Using a hash, we can remove the need for duplication:

	set users:9001 "{id: 9001, email: leto@dune.gov, ...}"
	hset users:lookup:email leto@dune.gov 9001

What we are doing is using the field as a pseudo secondary index and referencing the single user object. To get a user by id, we issue a normal `get`:

	get users:9001

To get a user by email, we issue an `hget` followed by a `get` (in Ruby):

	id = redis.hget('users:lookup:email', 'leto@dune.gov')
	user = redis.get("users:#{id}")

This is something that you'll likely end up doing often. To me, this is where hashes really shine, but it isn't an obvious use-case until you see it.

## References and Indexes

We've seen a couple examples of having one value reference another. We saw it when we looked at our list example, and we saw it in the section above when using hashes to make querying a little easier. What this comes down to is essentially having to manually manage your indexes and references between values. Being honest, I think we can say that's a bit of a downer, especially when you consider having to manage/update/delete these references manually. There is no magic solution to solving this problem in Redis.

We already saw how sets are often used to implement this type of manual index:

	sadd friends:leto ghanima paul chani jessica

Each member of this set is a reference to a Redis string value containing details on the actual user. What if `chani` changes her name, or deletes her account? Maybe it would make sense to also track the inverse relationships:

	sadd friends_of:chani leto paul

Maintenance cost aside, if you are anything like me, you might cringe at the processing and memory cost of having these extra indexed values. In the next section we'll talk about ways to reduce the performance cost of having to do extra round trips (we briefly talked about it in the first chapter).

If you actually think about it though, relational databases have the same overhead. Indexes take memory, must be scanned or ideally seeked and then the corresponding records must be looked up. The overhead is neatly abstracted away (and they  do a lot of optimizations in terms of the processing to make it very efficient).

Again, having to manually deal with references in Redis is unfortunate. But any initial concerns you have about the performance or memory implications of this should be tested. I think you'll find it a non-issue.

## Round Trips and Pipelining

We already mentioned that making frequent trips to the server is a common pattern in Redis. Since it is something you'll do often, it's worth taking a closer look at what features we can leverage to get the most out of it.

First, many commands either accept one or more set of parameters or have a sister-command which takes multiple parameters. We saw `mget` earlier, which takes multiple keys and returns the values:

	keys = redis.lrange('newusers', 0, 10)
	redis.mget(*keys.map {|u| "users:#{u}"})

Or the `sadd` command which adds 1 or more members to a set:

	sadd friends:vladimir piter
	sadd friends:paul jessica leto "leto II" chani

Redis also supports pipelining. Normally when a client sends a request to Redis it waits for the reply before sending the next request. With pipelining you can send a number of requests without waiting for their responses. This reduces the networking overhead and can result in significant performance gains.

It's worth noting that Redis will use memory to queue up the commands, so it's a good idea to batch them. How large a batch you use will depend on what commands you are using, and more specifically, how large the parameters are. But, if you are issuing commands against ~50 character keys, you can probably batch them in thousands or tens of thousands.

Exactly how you execute commands within a pipeline will vary from driver to driver. In Ruby you pass a block to the `pipelined` method:

	redis.pipelined do
	  9001.times do
		redis.incr('powerlevel')
	  end
	end

As you can probably guess, pipelining can really speed up a batch import!

## Transactions

Every Redis command is atomic, including the ones that do multiple things. Additionally, Redis has support for transactions when using multiple commands.

You might not know it, but Redis is actually single-threaded, which is how every command is guaranteed to be atomic. While one command is executing, no other command will run. (We'll briefly talk about scaling in a later chapter.) This is particularly useful when you consider that some commands do multiple things. For example:

`incr` is essentially a `get` followed by a `set`

`getset` sets a new value and returns the original

`setnx` first checks if the key exists, and only sets the value if it does not

Although these commands are useful, you'll inevitably need to run multiple commands as an atomic group. You do so by first issuing the `multi` command, followed by all the commands you want to execute as part of the transaction, and finally executing `exec` to actually execute the commands or `discard` to throw away, and not execute the commands. What guarantee does Redis make about transactions?

* The commands will be executed in order

* The commands will be executed as a single atomic operation (without another client's command being executed halfway through)

* That either all or none of the commands in the transaction will be executed

You can, and should, test this in the command line interface. Also note that there's no reason why you can't combine pipelining and transactions.

	multi
	hincrby groups:1percent balance -9000000000
	hincrby groups:99percent balance 9000000000
	exec

Finally, Redis lets you specify a key (or keys) to watch and conditionally apply a transaction if the key(s) changed. This is used when you need to get values and execute code based on those values, all in a transaction. With the code above, we wouldn't be able to implement our own `incr` command since they are all executed together once `exec` is called. From code, we can't do:

	redis.multi()
	current = redis.get('powerlevel')
	redis.set('powerlevel', current + 1)
	redis.exec()

That isn't how Redis transactions work. But, if we add a `watch` to `powerlevel`, we can do:

	redis.watch('powerlevel')
	current = redis.get('powerlevel')
	redis.multi()
	redis.set('powerlevel', current + 1)
	redis.exec()

If another client changes the value of `powerlevel` after we've called `watch` on it, our transaction will fail. If no client changes the value, the set will work. We can execute this code in a loop until it works.

## Keys Anti-Pattern

In the next chapter we'll talk about commands that aren't specifically related to data structures. Some of these are administrative or debugging tools. But there's one I'd like to talk about in particular: the `keys` command. This command takes a pattern and finds all the matching keys. This command seems like it's well suited for a number of tasks, but it should never be used in production code. Why? Because it does a linear scan through all the keys looking for matches. Or, put simply, it's slow.

How do people try and use it? Say you are building a hosted bug tracking service. Each account will have an `id` and you might decide to store each bug into a string value with a key that looks like `bug:account_id:bug_id`. If you ever need to find all of an account's bugs (to display them, or maybe delete them if they delete their account), you might be tempted (as I was!) to use the `keys` command:

	keys bug:1233:*

The better solution is to use a hash. Much like we can use hashes to provide a way to expose secondary indexes, so too can we use them to organize our data:

	hset bugs:1233 1 "{id:1, account: 1233, subject: '...'}"
	hset bugs:1233 2 "{id:2, account: 1233, subject: '...'}"

To get all the bug ids for an account we simply call `hkeys bugs:1233`. To delete a specific bug we can do `hdel bugs:1233 2` and to delete an account we can delete the key via `del bugs:1233`.


## In This Chapter

This chapter, combined with the previous one, has hopefully given you some insight on how to use Redis to power real features. There are a number of other patterns you can use to build all types of things, but the real key is to understand the fundamental data structures and to get a sense for how they can be used to achieve things beyond your initial perspective.

# Chapter 4 - Beyond The Data Structures

While the five data structures form the foundation of Redis, there are other commands which aren't data structure specific. We've already seen a handful of these: `info`, `select`, `flushdb`, `multi`, `exec`, `discard`, `watch` and `keys`. This chapter will look at some of the other important ones.

## Expiration

Redis allows you to mark a key for expiration. You can give it an absolute time in the form of a Unix timestamp (seconds since January 1, 1970) or a time to live in seconds. This is a key-based command, so it doesn't matter what type of data structure the key represents.

	expire pages:about 30
	expireat pages:about 1356933600

The first command will delete the key (and associated value) after 30 seconds. The second will do the same at 12:00 a.m. December 31st, 2012.

This makes Redis an ideal caching engine. You can find out how long an item has to live until via the `ttl` command and you can remove the expiration on a key via the `persist` command:

	ttl pages:about
	persist pages:about

Finally, there's a special string command, `setex` which lets you set a string and specify a time to live in a single atomic command (this is more for convenience than anything else):

	setex pages:about 30 '<h1>about us</h1>....'

## Publication and Subscriptions

Redis lists have an `blpop` and `brpop` command which returns and removes the first (or last) element from the list or blocks until one is available. These can be used to power a simple queue.

Beyond this, Redis has first-class support for publishing messages and subscribing to channels. You can try this out yourself by opening a second `redis-cli` window. In the first window subscribe to a channel (we'll call it `warnings`):

	subscribe warnings

The reply is the information of your subscription. Now, in the other window, publish a message to the `warnings` channel:

	publish warnings "it's over 9000!"

If you go back to your first window you should have received the message to the `warnings` channel.

You can subscribe to multiple channels (`subscribe channel1 channel2 ...`), subscribe to a pattern of channels (`psubscribe warnings:*`) and use the `unsubscribe` and `punsubscribe` commands to stop listening to one or more channels, or a channel pattern.

Finally, notice that the `publish` command returned the value 1. This indicates the number of clients that received the message.


## Monitor and Slow Log

The `monitor` command lets you see what Redis is up to. It's a great debugging tool that gives you insight into how your application is interacting with Redis. In one of your two redis-cli windows (if one is still subscribed, you can either use the `unsubscribe` command or close the window down and re-open a new one) enter the `monitor` command. In the other, execute any other type of command (like `get` or `set`). You should see those commands, along with their parameters, in the first window.

You should be wary of running monitor in production, it really is a debugging and development tool. Aside from that, there isn't much more to say about it. It's just a really useful tool.

Along with `monitor`, Redis has a `slowlog` which acts as a great profiling tool. It logs any command which takes longer than a specified number of **micro**seconds. In the next section we'll briefly cover how to configure Redis, for now you can configure Redis to log all commands by entering:

	config set slowlog-log-slower-than 0

Next, issue a few commands. Finally you can retrieve all of the logs, or the most recent logs via:

	slowlog get
	slowlog get 10

You can also get the number of items in the slow log by entering `slowlog len`

For each command you entered you should see four parameters:

* An auto-incrementing id

* A Unix timestamp for when the command happened

* The time, in microseconds, it took to run the command

* The command and its parameters

The slow log is maintained in memory, so running it in production, even with a low threshold, shouldn't be a problem. By default it will track the last 1024 logs.

## Sort

One of Redis' most powerful commands is `sort`. It lets you sort the values within a list, set or sorted set (sorted sets are ordered by score, not the members within the set). In its simplest form, it allows us to do:

	rpush users:leto:guesses 5 9 10 2 4 10 19 2
	sort users:leto:guesses

Which will return the values sorted from lowest to highest. Here's a more advanced example:

	sadd friends:ghanima leto paul chani jessica alia duncan
	sort friends:ghanima limit 0 3 desc alpha

The above command shows us how to page the sorted records (via `limit`), how to return the results in descending order (via `desc`) and how to sort lexicographically instead of numerically (via `alpha`).

The real power of `sort` is its ability to sort based on a referenced object. Earlier we showed how lists, sets and sorted sets are often used to reference other Redis objects. The `sort` command can dereference those relations and sort by the underlying value. For example, say we have a bug tracker which lets users watch issues. We might use a set to track the issues being watched:

	sadd watch:leto 12339 1382 338 9338

It might make perfect sense to sort these by id (which the default sort will do), but we'd also like to have these sorted by severity. To do so, we tell Redis what pattern to sort by. First, let's add some more data so we can actually see a meaningful result:

	set severity:12339 3
	set severity:1382 2
	set severity:338 5
	set severity:9338 4

To sort the bugs by severity, from highest to lowest, you'd do:

	sort watch:leto by severity:* desc

Redis will substitute the `*` in our pattern (identified via `by`) with the values in our list/set/sorted set. This will create the key name that Redis will query for the actual values to sort by.

Although you can have millions of keys within Redis, I think the above can get a little messy. Thankfully `sort` can also work on hashes and their fields. Instead of having a bunch of top-level keys you can leverage hashes:

	hset bug:12339 severity 3
	hset bug:12339 priority 1
	hset bug:12339 details "{id: 12339, ....}"

	hset bug:1382 severity 2
	hset bug:1382 priority 2
	hset bug:1382 details "{id: 1382, ....}"

	hset bug:338 severity 5
	hset bug:338 priority 3
	hset bug:338 details "{id: 338, ....}"

	hset bug:9338 severity 4
	hset bug:9338 priority 2
	hset bug:9338 details "{id: 9338, ....}"

Not only is everything better organized, and we can sort by `severity` or `priority`, but we can also tell `sort` what field to retrieve:

	sort watch:leto by bug:*->priority get bug:*->details

The same value substitution occurs, but Redis also recognizes the `->` sequence and uses it to look into the specified field of our hash. We've also included the `get` parameter, which also does the substitution and field lookup, to retrieve bug details.

Over large sets, `sort` can be slow. The good news is that the output of a `sort` can be stored:

	sort watch:leto by bug:*->priority get bug:*->details store watch_by_priority:leto

Combining the `store` capabilities of `sort` with the expiration commands we've already seen makes for a nice combo.

## In This Chapter

This chapter focused on non-data structure-specific commands. Like everything else, their use is situational. It isn't uncommon to build an app or feature that won't make use of expiration, publication/subscription and/or sorting. But it's good to know that they are there. Also, we only touched on some of the commands. There are more, and once you've digested the material in this book it's worth going through the [full list](http://redis.io/commands).

# Chapter 5 - Lua Scripting

Redis 2.6 includes a built-in Lua interpreter which developers can leverage to write more advanced queries to be executed within Redis. It wouldn't be wrong of you to think of this capability much like you might view stored procedures available in most relational databases.

The most difficult aspect of mastering this feature is learning Lua. Thankfully, Lua is similar to most general purpose languages, is well documented, has an active community and is useful to know beyond Redis scripting. This chapter won't cover Lua in any detail; but the few examples we look at should hopefully serve as a simple introduction.

## Why?

Before looking at how to use Lua scripting, you might be wondering why you'd want to use it. Many developers dislike traditional stored procedures, is this any different? The short answer is no. Improperly used, Redis' Lua scripting can result in harder to test code, business logic tightly coupled with data access or even duplicated logic.

Properly used however, it's a feature that can simplify code and improve performance. Both of these benefits are largely achieved by grouping multiple commands, along with some simple logic, into a custom-build cohesive function. Code is made simpler because each invocation of a Lua script is run without interruption and thus provides a clean way to create your own atomic commands (essentially eliminating the need to use the cumbersome `watch` command). It can improve performance by removing the need to return intermediary results - the final output can be calculated within the script.

The examples in the following sections will better illustrate these points.

## Eval

The `eval` command takes a Lua script (as a string), the keys we'll be operating against, and an optional set of arbitrary arguments. Let's look at a simple example (executed from Ruby, since running multi-line Redis commands from its command-line tool isn't fun):

    script = <<-eos
      local friend_names = redis.call('smembers', KEYS[1])
      local friends = {}
      for i = 1, #friend_names do
        local friend_key = 'user:' .. friend_names[i]
        local gender = redis.call('hget', friend_key, 'gender')
        if gender == ARGV[1] then
          table.insert(friends, redis.call('hget', friend_key, 'details'))
        end
      end
      return friends
    eos
    Redis.new.eval(script, ['friends:leto'], ['m'])

The above code gets the details for all of Leto's male friends. Notice that to call Redis commands within our script we use the `redis.call("command", ARG1, ARG2, ...)` method.

If you are new to Lua, you should go over each line carefully. It might be useful to know that `{}` creates an empty `table` (which can act as either an array or a dictionary), `#TABLE` gets the number of elements in the TABLE, and `..` is used to concatenate strings.

`eval` actually take 4 parameters. The second parameter should actually be the number of keys; however the Ruby driver automatically creates this for us. Why is this needed? Consider how the above looks like when executed from the CLI:
  
    eval "....." "friends:leto" "m"
    vs
    eval "....." 1 "friends:leto" "m"

In the first (incorrect) case, how does Redis know which of the parameters are keys and which are simply arbitrary arguments? In the second case, there is no ambiguity.

This brings up a second question: why must keys be explicitly listed? Every command in Redis knows, at execution time, which keys are going to needed. This will allow future tools, like Redis Cluster, to  distribute requests amongst multiple Redis servers. You might have spotted that our above example actually reads from keys dynamically (without having them passed to `eval`). An `hget` is issued on all of Leto's male friends. That's because the need to list keys ahead of time is more of a suggestion than a hard rule. The above code will run fine in a single-instance setup, or even with replication, but won't in the yet-released Redis Cluster.

## Script Management

Even though scripts executed via `eval` are cached by Redis, sending the body every time you want to execute something isn't ideal. Instead, you can register the script with Redis and execute it's key. To do this you use the `script load` command, which returns the SHA1 digest of the script:

    redis = Redis.new
    script_key = redis.script(:load, "THE_SCRIPT")

Once we've loaded the script, we can use `evalsha` to execute it:

    redis.evalsha(script_key, ['friends:leto'], ['m'])

`script kill`, `script flush` and `script exists` are the other commands that you can use to manage Lua scripts. They are used to kill a running script, removing all scripts from the internal cache and seeing if a script already exists within the cache.

## Libraries

Redis' Lua implementation ships with a handful of useful libraries. While `table.lib`, `string.lib` and `math.lib` are quite useful, for me, `cjson.lib` is worth singling out. First, if you find yourself having to pass multiple arguments to a script, it might be cleaner to pass it as JSON:

    redis.evalsha ".....", [KEY1], [JSON.fast_generate({gender: 'm', ghola: true})]

Which you could then deserialize within the Lua script as:

    local arguments = cjson.decode(ARGV[1])

Of course, the JSON library can also be used to parse values stored in Redis itself. Our above example could potentially be rewritten as such:

      local friend_names = redis.call('smembers', KEYS[1])
      local friends = {}
      for i = 1, #friend_names do
        local friend_raw = redis.call('get', 'user:' .. friend_names[i])
        local friend_parsed = cjson.decode(friend_raw)
        if friend_parsed.gender == ARGV[1] then
          table.insert(friends, friend_raw)
        end
      end
      return friends

Instead of getting the gender from specific hash field, we could get it from the stored friend data itself. (This is a much slower solution, and I personally prefer the original, but it does show what's possible).

## Atomic

Since Redis is single-threaded, you don't have to worry about your Lua script being interrupted by another Redis command. One of the most obvious benefits of this is that keys with a TTL won't expire half-way through execution. If a key is present at the start of the script, it'll be present at any point thereafter - unless you delete it.

## Administration

The next chapter will talk about Redis administration and configuration in more detail. For now, simply know that the `lua-time-limit` defines how long a Lua script is allowed to execute before being terminated. The default is generous 5 seconds. Consider lowering it.

## In This Chapter

This chapter introduced Redis' Lua scripting capabilities. Like anything, this feature can be abused. However, used prudently in order to implement your own custom and focused commands, it won't only simplify your code, but will likely improve performance. Lua scripting is like almost every other Redis feature/command: you make limited, if any, use of it at first only to find yourself using it more and more every day. 

# Chapter 6 - Administration

Our last chapter is dedicated to some of the administrative aspects of running Redis. In no way is this a comprehensive guide on Redis administration. At best we'll answer some of the more basic questions new users to Redis are most likely to have.

## Configuration

When you first launched the Redis server, it warned you that the `redis.conf` file could not be found. This file can be used to configure various aspects of Redis. A well-documented `redis.conf` file is available for each release of Redis. The sample file contains the default configuration options, so it's useful to both understand what the settings do and what their defaults are. You can find it at <https://github.com/antirez/redis/raw/2.4.6/redis.conf>.

**This is the config file for Redis 2.4.6. You should replace "2.4.6" in the above URL with your version. You can find your version by running the `info` command and looking at the first value.**

Since the file is well-documented, we won't be going over the settings.

In addition to configuring Redis via the `redis.conf` file, the `config set` command can be used to set individual values. In fact, we already used it when setting the `slowlog-log-slower-than` setting to 0.

There's also the `config get` command which displays the value of a setting. This command supports pattern matching. So if we want to display everything related to logging, we can do:

	config get *log*

## Authentication

Redis can be configured to require a password. This is done via the `requirepass` setting (set through either the `redis.conf` file or the `config set` command). When `requirepass` is set to a value (which is the password to use), clients will need to issue an `auth password` command.

Once a client is authenticated, they can issue any command against any database. This includes the `flushall` command which erases every key from every database. Through the configuration, you can rename commands to achieve some security through obfuscation:

	rename-command CONFIG 5ec4db169f9d4dddacbfb0c26ea7e5ef
	rename-command FLUSHALL 1041285018a942a4922cbf76623b741e

Or you can disable a command by setting the new name to an empty string.

## Size Limitations

As you start using Redis, you might wonder "how many keys can I have?" You might also wonder how many fields can a hash have (especially when you use it to organize your data), or how many elements can lists and sets have? Per instance, the practical limits for all of these is in the hundreds of millions.


## Replication

Redis supports replication, which means that as you write to one Redis instance (the master), one or more other instances (the slaves) are kept up-to-date by the master. To configure a slave you use either the `slaveof` configuration setting or the `slaveof` command (instances running without this configuration are or can be masters).

Replication helps protect your data by copying to different servers. Replication can also be used to improve performance since reads can be sent to slaves. They might respond with slightly out of date data, but for most apps that's a worthwhile tradeoff.

Unfortunately, Redis replication doesn't yet provide automated failover. If the master dies, a slave needs to be manually promoted. Traditional high-availability tools that use heartbeat monitoring and scripts to automate the switch are currently a necessary headache if you want to achieve some sort of high availability with Redis.

## Backups

Backing up Redis is simply a matter of copying Redis' snapshot to whatever location you want (S3, FTP, ...). By default Redis saves its snapshot to a file named `dump.rdb`. At any point in time, you can simply `scp`, `ftp` or `cp` (or anything else) this file.

It isn't uncommon to disable both snapshotting and the append-only file (aof) on the master and let a slave take care of this. This helps reduce the load on the master and lets you set more aggressive saving parameters on the slave without hurting overall system responsiveness.

## Scaling and Redis Cluster

Replication is the first tool a growing site can leverage. Some commands are more expensive than others (`sort` for example) and offloading their execution to a slave can keep the overall system responsive to incoming queries.

Beyond this, truly scaling Redis comes down to distributing your keys across multiple Redis instances (which could be running on the same box, remember, Redis is single-threaded). For the time being, this is something you'll need to take care of (although a number of Redis drivers do provide consistent-hashing algorithms). Thinking about your data in terms of horizontal distribution isn't something we can cover in this book. It's also something you probably won't have to worry about for a while, but it's something you'll need to be aware of regardless of what solution you use.

The good news is that work is under way on Redis Cluster. Not only will this offer horizontal scaling, including rebalancing, but it'll also provide automated failover for high availability.

High availability and scaling is something that can be achieved today, as long as you are willing to put the time and effort into it. Moving forward, Redis Cluster should make things much easier.

## In This Chapter

Given the number of projects and sites using Redis already, there can be no doubt that Redis is production-ready, and has been for a while. However, some of the tooling, especially around security and availability is still young. Redis Cluster, which we'll hopefully see soon, should help address some of the current management challenges.

# Conclusion

In a lot of ways, Redis represents a simplification in the way we deal with data. It peels away much of the complexity and abstraction available in other systems. In many cases this makes Redis the wrong choice. In others it can feel like Redis was custom-built for your data.

Ultimately it comes back to something I said at the very start: Redis is easy to learn. There are many new technologies and it can be hard to figure out what's worth investing time into learning. When you consider the real benefits Redis has to offer with its simplicity, I sincerely believe that it's one of the best investments, in terms of learning, that you and your team can make.
