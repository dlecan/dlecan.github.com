---
layout: post
title: "Introduction au langage de programmation Rust - Partie 2"
date: 2017-03-14 14:21
comments: true
published: true
categories: [rust, development, tutorial]
---

> Article initialement publié sur [https://www.technologies-ebusiness.com/langages/introduction-a-rust-partie-2](https://www.technologies-ebusiness.com/langages/introduction-a-rust-partie-2)

Nous poursuivons notre découverte du langage Rust après [une première partie](/introduction-rust-part-1/) dans laquelle vous avez pratiqué quelques concepts de base de Rust : éléments de syntaxe, déclaration de variables immuables, fonctions et matching. Si la pratique de Rust directement dans le navigateur était adaptée pour débuter, je vous propose désormais de développer directement sur votre poste. Nous pourrons alors continuer à explorer les possibilités de Rust.
Installer Rust

Le site officiel [https://www.rust-lang.org](https://www.rust-lang.org) propose des binaires ou des installeurs pour Linux, Mac ou Windows, qui ne se mettent pas à jour automatiquement. Sachant qu’une nouvelle version du langage Rust et des outils est publiée toutes les six semaines et que l’on est régulièrement amené à jongler entre les différents channels de Rust (stable, beta, nightly), je vous déconseille cette façon d’installer Rust.

Préférez plutôt l’usage de Rustup, le nouveau programme officiel d’installation de Rust (la page officielle de téléchargement commence aussi à y faire référence). Vous trouvez la procédure d’installation sur le site [https://www.rustup.rs](https://www.rustup.rs), à savoir une simple commande à taper dans une console : `curl https://sh.rustup.rs -sSf | sh`. Vous installez Rustup qui ensuite installe pour vous le compilateur Rust rustc, l’outil de gestion de dépendances et de build cargo, ainsi que le débuggueur ou le formateur de code de la version stable courante de Rust. Vous pourrez très simplement mettre à jour cette version stable avec rustup update quand vous en aurez besoin (toutes les six semaines !) ou bien installer la beta par exemple (rustup install beta).

Précis et efficace ... quand vous avez curl et un interpréteur sh à disposition, ce qui n’est pas le cas par défaut sous Windows. Si vous développez avec Rust sous Windows, vous aurez, tôt ou tard, besoin aussi de Git pour versionner vos fichiers sources. Je vous recommande donc d’installer d’abord l’outillage de Git qui vient avec une ligne de commande assez complète et qui ressemble à ce que vous pourriez obtenir sur un Linux : [https://git-for-windows.github.io](https://git-for-windows.github.io). Téléchargez l’installeur 32 ou 64 bits selon votre machine, puis lancez la commande d’installation de Rustup dans le shell proposé par Git for Windows.

A la fin de l’installation, en ligne de commande (sous Windows celle de Git for Windows, n’oubliez pas car je ne le répéterai plus :-), tapez :

```
$ rustc --version
```

Si vous obtenez quelque chose comme `rustc 1.10.0 (cfcb716cf 2016-07-03)`, le compilateur Rust est opérationnel sur votre poste !

## Editeur de texte malin ou environnement de développement intégré (IDE) ?

Autant vous le dire d’emblée, les "assistants" de développement Rust sont loin d’être du niveau de ce que l’on peut trouver dans d’autres langages comme Java ou .Net. Il y a encore beaucoup de travail à faire mais on arrive tout même à se créer un environnement acceptable. Personnellement, je travaille avec Sublime Text, complété par quelques plugins qui me permettent d’avoir la coloration syntaxique, le formatage et le linting, une validation moins précise de la syntaxe. Je vous invite à consulter areweideyet.com pour choisir l’environnement le plus adapté à votre contexte : Vim, Emacs, Atom, Visual Studio, Eclipse ... ou simplement, si vous souhaitez aller vite, un éditeur de texte type Notepad++, Geany ou Sublime Text seront suffisants.

## Calculer division : reboot

Vous souvenez-vous de votre premier programme Rust écrit dans la première partie de ce dossier ? Nous allons le revisiter avec les nouveaux outils dont nous nous sommes dotés, et en particulier cargo. C’est un mix de Maven pour la structure standard des projets Rust, de npm pour la gestion de dépendances ou l’installation d’un programme et de commandes permettant de gérer un projet Rust.

Créons-en un de type "programme Rust" (grâce au paramètre `-bin`) :

```
$ cargo new --bin division
```

Avec votre éditeur de texte, copiez-collez le contenu de notre dernier programme dans le fichier src/main.rs du répertoire division créé par Cargo :

```rust
fn calculer_division(x: i32, y: i32) -> i32  {
    match y {
        0 => panic!("Division par 0"),
        1 => x,
        _ => x / y
    }
}


fn main() {

    let resultat = calculer_division(-4, 2);

    println!("Résultat : {}", resultat);

}
```

Code complet sur ce Gist : [https://git.io/vKcsS](https://git.io/vKcsS). Sauvegardez, puis lancez en ligne de commande :

```
$ cargo run

  Compiling division v0.1.0 (file://.../division)

    Running `target/debug/division`

Résultat : -2
```


Cargo compile et lance à la suite le programme sans argument. Si vous souhaitez simplement compiler, lancez cargo build et si vous souhaitez lancer le programme vous-même, sachez qu’il se trouve dans le répertoire target/debug :

```
$ ./target/debug/division

Résultat : -2
```

## Une API sûre

Nous allons variabiliser le numérateur de notre division et le passer en paramètre de la ligne de commande. Explorons l’API de Rust pour ce besoin : lire les arguments en paramètre du programme s’effectue grâce à une fonction du module std::env déclarée comme ceci (Cf. [https://doc.rust-lang.org/std/env/fn.args.html](https://doc.rust-lang.org/std/env/fn.args.html)) :

``` rust
pub fn args() -> Args
```

La fonction `std::env::args()` nous renvoie donc une instance de la *struct* `Args` (elle-même dans le module `std::env`), une structure qui va contenir des champs et des méthodes permettant de manipuler les arguments du programme, et ce de manière sûre. Qu’est-ce que cela signifie ? Sûr implique par exemple le bannissement de la "nullité", source de fréquentes erreurs d’exécution (le fameux *NullPointerException* en Java par exemple). En Rust, toutes les API sont conçues pour renvoyer quelque chose -un résultat ou une erreur- et même "rien" est quelque chose en Rust.

## `Option` - `Some` - `None`

Regardons la déclaration de la fonction nth de Args qui va nous permettre de récupérer le nième argument de notre programme. Elle est déclarée comme ceci (la signature est légèrement adaptée pour une compréhension plus aisée) :

```rust
fn nth(&mut self, n: usize) -> Option<String>
```

Mettons de coté le &mut self, nous y reviendrons par la suite. nth est une fonction qui renvoie un Option de type String. Option est une énumération Rust à deux variantes possibles :

```rust
pub enum Option<T> {
    None,
    Some(T),
}
```

Si vous substituez le type générique `T` par `String`, vous comprenez alors que la méthode `nth` peut renvoyer soit `None`, qui signifie qu’il y a pas de valeur à cette position, soit `Some(String)`, qui signifie qu’il existe une valeur à la position demandée et quelle peut être extraite de la valeur de l’énumération. Ce qui est génial, c’est que ces variantes peuvent être "matchées" (Cf. partie 1 de ce dossier) :

```rust
use std::env;

...

let numerateur = match env::args().nth(1) {
    Some(argument) => argument,
    None => panic!("Argument obligatoire manquant : le numérateur")
};
```

Ici, on effectue aussi du pattern matching pour extraire la valeur contenue dans le `Some`. `Some(argument)` permet de déclarer la variable argument, affectée à la valeur contenu dans le `Some`, que l’on peut alors renvoyer (avec un `return` implicite). On arrête le programme prématurément avec la macro `panic!` et un message explicite en cas de `None`.

Enfin, cerise sur le gâteau, comme tout est expression en Rust, on peut affecter directement le retour de notre match à une variable : `let numerateur = match env::args() ...`.

Notez l’utilisation du module env importé par une ligne en début de programme, `use std::env;`, à ajouter pour chaque module utilisé dans le programme.

Avec les `Option` et l’API de Rust, nous avons pu extraire notre paramètre de ligne de commande et l’avons rendu obligatoire. Ce n’est cependant pas suffisant : en effet, la fonction `nth` renvoie un `Option<String>` dans notre cas, ce qui veut dire que la variable numerateur ci-dessus est de type `String`, alors que nous attendons un `i32`. Il faut donc convertir notre valeur.

## `Result` - `Ok` - `Err`

Rust propose une API de conversion de `String` :

```rust
fn parse<F>(&self) -> Result<F, F::Err> where F: FromStr
```

Prenons quelques instants pour comprendre la signature de cette fonction :

- `fn parse` : "parse" est le nom de la fonction :-)
- `F` : il s’agit d’un type générique, que vous pouvez substituer mentalement par le type que vous voulez obtenir après le parsing de votre `String`
  La définition en est donnée en fin de ligne, `where F: FromStr`, ce qui signifie que `F` doit respecter le contrat décrit dans le *trait* `std::str::FromStr` (c’est une sorte d’interface)
- `&self` : il s’agit de la syntaxe spécifique à Rust qui indique que cette fonction n’est pas statique et qu’elle s’applique sur des instances de l’objet courant (`String` ici)
- `Result<F, F::Err>` : le type de retour, encapsulant `F` et un type d’erreur associée à `F`

`Result` est la façon élégante en Rust de gérer les éventuels retours en erreur d’un traitement. Il est décrit comme ceci dans l’API de Rust :

```rust
pub enum Result<T, E> {
  Ok(T),
  Err(E),
}
```

Un peu comme `Option` vue précédemment, `Result` est une énumération à deux variantes, sur lesquelles on pourra *matcher* :

- `Ok` est utilisé pour encapsuler le résultat d’un traitement qui s’est bien déroulé
- `Err` permet de propager les erreurs de traitement

Il n’y a donc pas d’exception en Rust et l’usage des codes retours pour indiquer un résultat de traitement est considéré comme une mauvaise pratique, car non sûre.

Le type `i32` implémentant bien le *trait* `std::str::FromStr`, on pourrait écrire la fonction dédiée au parsing d’une `String` en `i32` :

```rust
fn parse(&self) -> Result<i32, ParseIntError>
```

L’association de `ParseIntError` à `i32` est décrite dans sa documentation (cherchez `impl FromStr for i32` et juste en dessous le type `Err = ParseIntError`).

Gardez en tête que c’est une simple vue de l’esprit, car elle est redondante avec la définition de la fonction décrite avec un type générique, mais elle permet de fixer les idées quand on n’est pas encore à l’aise avec la syntaxe de Rust.

Nous pouvons donc convertir notre `String` numerateur en `i32` après un *matching* :

```rust
let numerateur = match numerateur.parse::<i32>() {
    Ok(numerateur) => numerateur,
    Err(error) => panic!("Impossible de convertir notre argument. Raison: {}", error)
};
```

Il faut un peu aider le compilateur car il ne peut pas deviner quelle conversion on souhaite appliquer. Pour cela, on utilise la syntaxe *turbofish*: `::<>`, qui permet de spécifier le type de destination.

Si le `parse` s’est bien déroulé, le matching sur `Ok` permet d’extraire le numérateur sous forme de `i32` désormais ; sinon avec le matching sur `Err`, on arrête une nouvelle fois le programme avec un message d’erreur adéquat.

Enfin, substituez le premier paramètre de l’appel de la fonction calculer_division par la variable numerateur (code complet : [https://git.io/v6ypr](https://git.io/v6ypr)), compilez et exécutez en une fois :

```
$ cargo run 4

  Compiling division v0.1.0 (file://.../division)

    Running `target/debug/division 4`

Résultat : 2
```

Bravo, en quelques lignes de code robustes, vous avez gérés la présence et l’absence d’argument lors de l’exécution de notre programme et ce, de manière plutôt élégante.

Ainsi se termine cette 2è partie de notre dossier consacré à Rust. [Dans la prochaine partie](/introduction-rust-part-3/), le niveau de difficulté montera d’un cran : il sera en effet temps de se confronter au *borrow checker* !
