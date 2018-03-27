
---
layout: post
title: "Introduction au langage de programmation Rust - Partie 3"
date: 2017-03-17 21:32
comments: true
published: true
categories: [rust, development, tutorial]
---

> Initialement publié sur [https://www.technologies-ebusiness.com/langages/introduction-a-rust-partie-3](https://www.technologies-ebusiness.com/langages/introduction-a-rust-partie-3)

Après avoir vu comment installer le compilateur Rust et Cargo, puis écrit quelques lignes de code dans la (seconde partie de cette introduction](/introduction-rust-part-2/), nous allons continuer dans cette troisième partie à faire évoluer notre programme en découvrant de nouvelles caractéristiques de Rust.

Avant de nous lancer, rappelez-vous, une nouvelle version de Rust est publiée toutes les six semaines ; il est donc très probable que la version que vous avez installée est déjà obsolète.

Mettez à jour Rustup lui-même, puis ensuite Rust :

```
$ rustup self update // mise à jour de Rustup
 ...
$ rustup update // mise à jour de tous les canaux de Rust installés
```

Pensez à effectuer régulièrement ces opérations : une version à jour de Rust vous donne accès à de nouvelles fonctionnalités et une meilleure compatibilité avec les librairies de la communauté.

## `Struct` et `trait`

Notre programme des deux premières parties était structuré de manière procédurale. Nous allons lui donner une allure plus lisible et plus encapsulée, pour ne pas dire plus "objet". Rust n’a pas pour autant des "classes" comme dans d’autres langages, mais des `struct` et des `trait`.

Comme leur nom l’indique, les struct sont des structures qui permettent de stocker un ensemble de données complexes ; on dira qu’une struct définie un nouveau type. Notez qu’il n’y a pas de constructeur ou d’accesseurs à proprement parler comme dans d’autre langage, Rust reste très simple ici.

Faisons évoluer notre programme en introduisant une *struct* :

```rust
struct Division {
  numerateur: i32,
  denominateur: i32,
}
```

La structure peut-être ensuite instanciée et ses propriétés manipulées :

```rust
let une_division = Division {numerateur: 10, denominateur: 12};

println!("Une division ({} - {})", une_division.numerateur, une_division.denominateur);
```

Continuons notre logique d’encapsulation en ajoutant des méthodes à notre structure. Ici, la syntaxe de Rust sort vraiment de l’ordinaire. En effet, les méthodes ne sont pas ajoutées directement dans le corps de la *struct*, mais déclarées où vous le souhaitez dans votre programme avec le mot-clé impl.

```rust
impl Division {
    fn calculer(&self) -> i32 {
        match self.denominateur {
            0 => panic!("Division par 0"),
            1 => self.numerateur,
            _ => self.numerateur / self.denominateur
        }
    }
}
```

Le code de la méthode calculer est repris de la méthode `calculer_division` vu dans la précédente partie de cet article.

Il est tentant de penser qu’on peut faire n’importe quoi, comme en Javascript, à savoir rajouter des méthodes à n’importe quelle *struct*, écrite par vous ou fournie par le langage. Par exemple, en Javascript, on peut rajouter des méthodes au prototype `String` et les utiliser comme des méthodes du langage (cette capacité est notamment utilisée par tous les polyfills).

En Rust, il est impossible d’ajouter des méthodes à n’importe quelle *struct* ; les `impl` doivent être déclarés dans le même module (`crates` en Rust) que la *struct*.

Mettez à jour votre programme Rust :

- déclarer le nouveau type `Division` à l’aide d’une *struct*
- remplacer la déclaration de la méthode statique `calculer_division` par une méthode `calculer` qui implémente le type `Division` (attention à la signature de la méthode `calculer`)
- remplacer l’appel à la méthode `calculer_division` par l’instanciation du type `Division` avec le paramètre qui vient de l’analyse de la ligne de commande, puis calculer le résultat et l’afficher.

A ce stade, vous devez avoir un programme qui compile et s’exécute, ce que vous pouvez vérifier à l’aide de la commande `cargo run 4` (code complet [https://git.io/v1XJL](https://git.io/v1XJL)).

S’il n’existe pas de "vrai" constructeur en Rust, les us et coutumes du langage encouragent cependant l’encapsulation de l’opération d’instanciation d’un type au sein d’une fonction statique déclarée dans l’implémentation de la struct elle-même, nommée par convention `new` et qui retourne évidemment une instance de votre type. Ce qui donne pour `Division` :

```rust
impl Division {
    fn new(x: i32, y: i32) -> Division {
        Division {
            numerateur: x,
            denominateur: y,
        }
    }
    ...
}
```

Pensez à remplacer l’instanciation manuelle de `Division` dans votre programme par l’usage de la fonction `new` (code complet https://git.io/v1XLo).

Outre les `struct`, Rust propose un autre mécanisme qui permet d’indiquer au compilateur les fonctionnalités qu’un type doit obligatoirement fournir : les `trait`. Par exemple, si nous souhaitons constraindre la façon dont une Division doit déclarer le symbole qu’elle représente, nous pouvons déclarer le *trait* suivant :

```rust
trait HasSymbol {
    fn symbol(&self) -> String;
}
```

Les *traits* ressemblent aux interfaces Java ou C#, avec globalement les mêmes caractéristiques :

- ensemble de fonctions que le type doit obligatoirement redéfinir
- certaines fonctions peuvent avoir une implémentation par défaut
- un *trait* peut implémenter un autre *trait*, et donc "hériter" de ses caractéristiques (concrètement, il faudra implémenter les deux traits sur votre type)

Implémentons notre *trait* pour la Division :

```rust
impl HasSymbol for Division {
    fn symbol(&self) -> String {
        "/".to_string() // "/" est du type &str, qu'il faut donc convertir
    }
}
```

Notez ce point important, impossible en Java par exemple : implémenter un *trait* pour un type est une section de code déclarée a posteriori de la déclaration de ce type, ce qui veut dire qu’un nouveau *trait *peut enrichir retrospectivement un type existant (avec quelques restrictions cependant).

Désormais, il est possible de tracer le symbole de la `Division`, juste après sa construction par exemple :

```rust
let division = Division::new(numerateur, 2);
println!("Symbole : {}", division.symbol());
```

Le résultat d’exécution de votre programme est désormais enrichi du symbole de la Division (code complet [https://git.io/v1X4I](https://git.io/v1X4I)) :

```
$ cargo run 2
    Running `target/debug/division 2`
Symbole: /
Résultat : 1
```

## Encore quelques concepts à maîtriser par le Rustacéen qui débute

Vous vous en souvenez peut-être, je vous avais promis dans la première partie de cet article de la sueur, en particulier avec la gestion de la mémoire : elle n’est ni à la charge du développeur, ni à la charge d’une machine virtuelle et de son ramasse-miette.

Il est temps de mettre les mains dans le camboui et de transpirer. Accrochez-vous !

Reprenons le code décrit plus haut, et ajoutons-y une instruction qui paraît anodine :

```rust
let division = Division::new(numerateur, 2);
let division2 = division; // <= instruction anodine :-)
println!("Symbole : {}", division.symbol());
```

Que se passe-t-il à la compilation (`cargo build`) ?

```
error[E0382]: use of moved value: `division`
  --> src/main.rs:48:30
  |
47 |     let division2 = division;
  |         --------- value moved here
48 |     println!("Symbole : {}", division.symbol());
  |                              ^^^^^^^^ value used here after move
  |
  = note: move occurs because `division` has type `Division`, which
  does not implement the `Copy` trait
```

Le compilateur Rust vous indique que la valeur pointée par la variable division est déplacée (*moved*). En effet, une valeur ne peut être référencée que par une seule variable dans tout notre code. C’est le concept de possession (*ownership*), contrôlée à la lettre par le compilateur, qui va garantir la sécurité et la robustesse d’exécution d’un programme Rust. Nous avons déplacé la possession de la valeur de division vers division2, par conséquent, division ne peut plus être utilisée.

Qui dit "possession", dit "emprunt" (*borrowing*) ou "copie" et pour illustrer l’une ou l’autre des solutions, nous allons introduire une nouvelle fonction statique et l’utiliser (code complet [https://git.io/v1X2m](https://git.io/v1X2m)):

```rust
fn display_symbol(division: Division) {
    println!("Symbole: {}", division.symbol());
}
...
let division = Division::new(numerateur, 2);
display_symbol(division); // <= à la place du 'let division2 = ...'
```

Ce code ne compile pas et vous optenez un message d’erreur très similaire au précédent. Trois solutions pour résoudre cette erreur de compilation s’offrent à nous :

1/ Ne plus utiliser la variable division après l’appel à la méthode `display_symbol`

2/ Copier toute la valeur de la variable

3/ Permettre l’emprunt de la variable et utiliser sa référence

La solution 1/ est une façon de mettre le problème sous le tapis ou de le repousser, ce n’est pas vraiment une solution (même si le programme compilera effectivement 😉

Copier toute la valeur de la variable (solution 2) est une façon intéressante de résoudre le problème. Dans cette solution, la valeur de la variable est entièrement copiée lors de l’appel à la fonction. Notez bien qu’il n’y a donc plus de lien entre la variable déclarée avant l’appel de la fonction et la variable utilisée dans la fonction.

Pour déclarer un type "copiable", il suffit d’implémenter le *trait* `Copy`, ainsi que le *trait* `Clone` dont hérite `Copy`. Le plus simple, est d’utiliser l’implémentation automatique de certains traits, proposée par l’attribut `#[derive(...)]` (documentation : [https://bit.ly/2hASRHt](https://bit.ly/2hASRHt)), à appliquer directement sur un type :

```rust
#[derive(Copy, Clone)]
Division {
  ...
```

Avec cet ajout, notre programme compile de nouveau (code complet [https://git.io/v11tL](https://git.io/v11tL)). Cependant, si cette solution peut sembler magique et simple (et de plus proposée par le compilateur), elle a ausi ses limites :

- un type peut implémenter `Copy` uniquement si l’ensemble de ses composants implémente aussi `Copy` (dans notre exemple, `i32` implémente `Copy`, donc `Division` peut implémenter `Copy`). Pour les Javaistes, on retrouve ici une propriété équivalente dans l’esprit aux objets sérialisables
- un certain nombre de types n’implémentent pas `Copy` ; par oubli du développeur ou par incompatibililté technique. Par exemple, les `String` ne sont pas compatibles avec `Copy`.

La troisième et dernière solution s’applique donc à tous les autres cas. Elle s’appuie sur le principe de l’emprunt, à savoir qu’on va passer à notre fonction non pas la valeur d’une variable, mais une référence vers la valeur de cette variable.

Concrètement, il faut faire évoluer la signature de notre fonction d’affichage pour permettre l’emprunt et l’usage de référence :

```rust
fn display_symbol(division: &Division) { // <= Notez le & ici
    ...
}
...
let division = Division::new(numerateur, 2);
display_symbol(&division); // <= Notez le & ici
```

La fonction `display_symbol` attend une référence vers un type `Division`, identifiée par le symbole `&` et nous pouvons passer la référence vers une valeur en ajoutant le même symbole devant le nom d’une variable (code complet [https://git.io/v11ml](https://git.io/v11ml)). Cette solution est la plus universelle, au prix d’une complexité intellectuelle plus élevée.

Finalement, grâce à ces différents mécanismes, le cycle de vie de toutes les données référencées par des variables, est connu dès la compilation de votre programme, ce qui garantit une bonne gestion de la mémoire et donc une certaine robustesse. En revanche, elle vous garantit aussi quelques "combats" contre le borrow checker du compilateur Rust.

## Conclusion

Au cours de ces trois articles d’introduction à Rust, nous avons abordés les outils et les concepts les plus importants du langage : *struct*, *trait*, *pattern matching*, *enum*, possession (*ownership*), emprunt (*borrowing*), notions de programmation fonctionnelle ... Ces connaissances devraient être suffisantes pour écrire vos premiers programmes avec Rust.

Vous découvrirez par vous-même des concepts plus avancés du langage comme les *lifetimes*, les types génériques, les modules et les *crates*, les *macros*, les tests, les itérateurs, la concurrence, la gestion des erreurs ... La lecture du livre "The Rust programming language" sera donc à ce propos un bon point de départ pour vous : [https://doc.rust-lang.org/book/](https://doc.rust-lang.org/book/). Enfin, tous ces concepts pourraient faire l’objet d’articles dédiés, avis donc aux amateurs !
