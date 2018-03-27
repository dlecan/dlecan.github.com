---
layout: post
title: "Introduction au langage de programmation Rust - Partie 1"
date: 2017-03-09 11:34
comments: true
published: true
categories: [rust, development, tutorial]
---

Rust est un jeune langage qui a pour ambition de se substituer au C/C++ en proposant de nouveaux paradigmes de programmation, une librairie standard de haut niveau et un écosystème riche soutenu par une communauté très active.

Multiplateformes (systèmes d’exploitation ou architectures de processeurs) et pourvu de tous les concepts de programmation attendus pour un langage moderne (programmation orientée “objet”, programmation fonctionnelle, facilités à développer des programmes d’exécution concurrente), Rust a beaucoup d’atouts pour séduire.

Au travers de cette série d’articles, nous allons découvrir ensemble ce langage qui me paraît être le plus excitant depuis ces dernières années.

## Genèse

Avant de plonger dans le code, je vous propose de revenir sur les origines du langage. Elles remontent à 2010 : Graydon Hoare, ingénieur chez Mozilla, révèle ses travaux sur Rust. A l’époque, le “marché” des langages informatiques est un peu similaire à aujourd’hui, à savoir une domination de C/C++ et de Java, chacun cantonné à ses domaines de prédilection. En schématisant, Java règne sur l’informatique de “gestion” (systèmes d’information bancaires, assurance …) ainsi que sur Android, tandis que C/C++ est le choix par défaut de la programmation que je qualifierais de “système” ou “bas niveau” : système d’exploitation, pilotes, informatique embarquée, machines virtuelles (Java lui- même, ou NodeJS par exemple) …

Quelle caractéristique clivante peut bien isoler à ce point ces deux mondes de l’informatique ? Je pense qu’il s’agit principalement de la gestion de la mémoire, qui diverge totalement. Avec Java, elle est en grande partie masquée au développeur, c’est-à-dire que lorsqu’un développeur crée des objets en mémoire, il n’a pas vraiment à se soucier de son nettoyage. En effet, un processus qui s’exécute en arrière-plan, le ramasse-miette (garbage collector), se charge de détecter les objets qui ne seraient plus utilisés et de les supprimer de la mémoire. Le travail du développeur est donc grandement simplifié et celui-ci peut se concentrer sur le code métier le plus utile. C’est ce qui explique le succès de tous les langages à machine virtuelle et ramasse-miettes, comme Javascript ou .Net.

Si le fonctionnement du ramasse-miettes n’a pas ou peu de conséquences négatives -largement compensées par d’autres avantages- dans les applications de haut niveau comme celles dédiées au web, il est problématique, voire rédhibitoire pour d’autres. La latence, certes réduite, induite par l’initialisation ou le fonctionnement du ramasse-miettes, ne permet pas de construire des systèmes de plus bas niveau, comme les systèmes d’exploitation, les pilotes, les machines virtuelles elles-mêmes ou encore des programmes en ligne de commandes efficaces … Dans ces cas, la mémoire doit donc être gérée “à la main”.

En C/C++, la responsabilité du cycle de vie des structures ou objets en mémoire incombe au développeur et gare à la qualité du logiciel si cette tâche est mal effectuée : corruption de données, plantages, failles de sécurité … sont les conséquences principales d’une mauvaise gestion de la mémoire, phénomène encore aggravé dans un contexte de programme à exécution concurrente. Ne jetons pas la pierre aux développeurs : c’est une tâche ingrate, pénible et peu outillée. Alors dans ces conditions, comment produire des logiciels bas niveau de qualité ?

Depuis les premières ébauches, c’est précisément l’un des objectifs du langages Rust. Il ne repose ni sur un ramasse-miettes (ex: Java), ni sur une gestion mémoire manuelle du développeur (ex: C/C++). Rust propose une nouvelle façon de gérer la mémoire, qui se veut sûre et garantie à la compilation, ce qui permet d’en limiter l’impact sur les performances à l’exécution. Autant vous prévenir tout de suite : la courbe d’apprentissage de Rust est donc plus lente que dans d’autres langages mais largement compensée en qualité et fiabilité des programmes produits. Concrètement, vous allez transpirer au début mais serez fiers de la qualité de vos productions. Nous verrons par la suite comment elle se concrétise.

La syntaxe du langage, ainsi que la librairie standard, ont été débattues, testées, amendées par la -déjà très active- communauté, pendant des années, la première version stable 1.0 de Rust ayant été publiée en avril 2015. Ce délai a permis aussi de mettre en place une gouvernance ouverte et transparente qui régit l’évolution du langage, à laquelle chacun peut participer (board central, RFC ouvertes …). Trois channels de Rust sont édités en parallèle : nightly, beta et stable. De nouvelles versions de Rust stable et beta sont publiées toutes les 6 semaines, apportant à chaque fois leurs lots d’évolutions au langage.

Avant de démarrer, soyez attentifs lors de vos recherches sur Internet à la version de Rust concernée par les articles de blogs ou les solutions apportées sur Stack Overflow. On y trouve en effet beaucoup de contenus obsolètes, car applicables à des versions de Rust antérieures à la version 1.0.

Enfin, un mot sur la communauté de développeurs Rust. Elle est accueillante et bienveillante, vous trouverez de nombreux développeurs prêts à vous aider, à vous faire progresser ainsi qu’à critiquer votre code de manière constructive. Une bonne surprise et une des forces de ce langage.

## Bonjour lecteurs !

Commençons à écrire quelques lignes de code, sans installation préalable de Rust sur votre poste. Rendez-vous sur https://play.rust-lang.org/ pour ouvrir l’interpréteur web Rust, qui fonctionne très bien sur smartphone ou tablette. Idéal pour tester simplement Rust sans vous prendre la tête ! Ne modifiez pas le paramétrage par défaut de la page (boutons du haut) et concentrez-vous sur la zone de texte centrale : c’est là qu’il faut taper le code Rust.

Lors de votre première connexion, le formulaire est déjà préinitialisé avec un morceau de code que je vous propose de substituer par un contenu plus adapté à notre contexte :

```rust
fn main() {

    println!("{}", "Bonjour Programmez!");
}
```

Si vous avez des difficultés à saisir ce code, vous pouvez ouvrir directement cette URL : https://is.gd/JzBaCy. Par la suite, je présenterai systématiquement un lien vers le code présenté, vous permettant éventuellement de le copier/coller.

Cliquez ensuite sur le gros bouton rouge “Run” en haut pour exécuter ce programme. Vous devez alors obtenir sous la zone de texte de code l’affichage de “Bonjour Programmez!”. Bravo, vous venez d’écrire votre premier programme Rust !

## Analysons ensemble ces quelques lignes

La syntaxe s’inspire fortement du langage C (que l’on retrouve en Java, Javascript, .Net …) et le formatage du code est standardisé par le langage : indentation, espaces, positions des accolades … Notez le bouton “Format” dans l’interpréteur Web qui formate le code de la zone de texte comme on peut s’y attendre 🙂

- `fn` permet de déclarer une fonction, nommée ici main et sans argument. Par convention, c’est le point d’entrée unique d’un programme écrit en Rust
- `println!` permet d’écrire dans la sortie standard, du texte ou des structures plus complexes. Notez le `!` qui signifie que `println!` est une macro. C’est une routine de génération de code **à la compilation** et un pattern de développement très utilisé par les développeurs Rust pour masquer une complexité

Je vous recommande l’utilisation de `println!` à deux arguments et plus, syntaxe familière à ceux qui connaissent les fonctions `printf/fprintf` du C ou `str.format` de Python : le 1er paramètre représente la mise en forme, les suivants les valeurs à substituer aux `{}` présents dans le contenu du 1er argument (plus de détails sur https://doc.rust-lang.org/std/fmt/)

- `;` obligatoire en fin de ligne la plupart du temps en Rust

Modifions maintenant notre programme en déclarant une variable (https://is.gd/SIDHI3) :

```rust
fn main() {
    let une_chaine = "Bonjour Programmez!";
    println!("{}", une_chaine);
}
```

Les variables se déclarent avec le mot-clé `let`, sont immuables (que l’on ne peut pas réaffecter) et sont fortement typées. Pourtant vous remarquerez que nous n’avons pas déclaré le type de cette variable. En effet, Rust met en œuvre de l’inférence de type, à savoir qu’il tente de détecter le type des variables mais pas des paramètres. Un allègement significatif de la syntaxe qui facilite l’écriture et la relecture. Il faudra cependant parfois l’aider à deviner correctement le type. Remarquez enfin la syntaxe de type *snake case*, avec des `_` pour séparer les concepts dans les noms des variables (par opposition à la syntaxe *camel case*: `uneVariable`). Pratique, car le compilateur râlera si vous ne respectez pas cette convention.

Lancez avec “Run” pour compiler et exécuter ce code.

Poursuivons la découverte des concepts de Rust en introduisant une nouvelle fonction qui permet d’effectuer la division d’un nombre par un autre :

```rust
fn calculer_division(x: i32, y: i32) -> i32  {
    println!("Numérateur: {}", x);
    println!("Dénominateur: {}", y);
    x / y
}
```

L’appel de la fonction que nous venons de déclarer s’effectue exactement comme l’on s’y attend (programme complet : https://is.gd/0U61pa) :

```rust
...

fn main() {
    let resultat = calculer_division(-4, 2);
    println!("Résultat : {}", resultat);
}
```

Par choix, il n’y a pas d’inférence de type en Rust lors de la déclaration des fonctions : les types des paramètres d’entrée et du retour doivent être explicitement spécifiés. Le type des arguments est indiqué après chaque nom de variable suivi de : et le type de retour est spécifié après la flèche `->`. Rust propose une liste de types primitifs très complète, puisque vous avez par exemple la possibilité de choisir des entiers, des flottants signés ou non signés, des entiers ou des flottants de “longueur” variable.

Exemples :

- `u8` : est un nombre de longueur 8 octets uniquement positif, donc un nombre compris entre 0 et 255 (2⁸-1)

- `i16` : est un nombre de longueur 16 octets, positif ou négatif, donc un nombre compris entre -32 768 (-2^16/2) et 32 767 (2^16/2-1)

Je vous conseille d’explorer la liste complète des types primitifs pour vous faire une idée des autres types disponibles (https://doc.rust-lang.org/book /primitive-types.html).

Les plus attentifs d’entre vous aurons remarqué que cette fonction ne “retourne” explicitement rien comparé à d’autres langages et que la dernière ligne ne se termine pas par un point-virgule, alors que l’on a précédemment vu qu’il était obligatoire. En Rust, il y a un return implicite sur la dernière expression exécutée d’une fonction. Attention, ce n’est pas nécessairement la dernière ligne de code de la fonction. Dans l’exemple ci-dessous, il y a 2 façons de sortir de la fonction et aucune d’entre elles ne correspond à la dernière ligne de code de la fonction.

```rust
fn calculer_division(x: i32, y: i32) -> i32  {
    if y != 0 {
        x / y
    } else {
        panic!("Division par 0")
    }
}

fn main() {
...
}
```

Exemple complet : https://is.gd/9LKj2d.

A propos du point-virgule, dans notre cas ici qui semble manquant, vous ne devez pas en mettre en fin de ligne. Essayez, vous aurez une erreur de compilation ! Pourquoi ? Rust est un langage basé sur les expressions et non sur des déclarations. Cela signifie que tout renvoie quelque chose : assigner une variable renvoie quelque chose, if renvoie quelque chose … L’expression `x / y` renvoie le résultat de la division, compatible avec le type `i32`, là où l’expression `x / y;` un résultat dont le type est `()`, incompatible avec `i32`.

Enfin, modifions une dernière fois notre programme pour induire une syntaxe plus habituelle pour les Rustacéens (traduction de *Rustaceans“, le nom officiel des développeurs Rust) : le *matching*.

```rust
fn calculer_division(x: i32, y: i32) -> i32  {
    match y {
        0 => panic!("Division par 0"),
        1 => x,
        _ => x / y
    }
}

fn main() {
...
}
```

Exemple complet : https://is.gd/X7889d.

La syntaxe parle d’elle-même, c’est simple à comprendre. Le `match` permet de gérer plus de cas que ne peut le faire un simple if tout en rendant obligatoire le traitement du cas par défaut. Qui n’a jamais oublié un else ou un case default en Java, par exemple ? En Rust, c’est impossible car le compilateur s’assure que tous les cas possibles de matching sont bien déclarés et gérés par le développeur. `_` signifie au compilateur “tous les autres cas de matching” (dans notre cas, “tout sauf 0 et 1”). Sachez aussi que le matching offre aussi beaucoup plus de possibilités que ne montre ce simple exemple.

Ici se termine la 1ère partie de cette introduction à Rust. Je vous invite à poursuivre la lecture de ce dossier avec [la seconde partie](/introduction-rust-part-2/), dans laquelle vous installerez Rust et ses outils sur votre poste de développement et découvrirez d’autres concepts passionnants du langage, avec quelques notions de programmation fonctionnelle.
