> THIS IS A FORK OF [btree-slab](https://github.com/timothee-haudebourg/btree-slab). ALL CREDIT AND LICENSING GOES TO THE OWNER OF THE ORIGINAL REPOSITORY.

> The commit d845b7b66dfc790ddf7346cde6936bec56b0f652 within this fork is the entire history of the commit e92b987d87529773dd182cce0a4b8bd5123de2a9 in the upstream repository. (The commit does not exist in the upstream, but is the commit of version 0.2.1 of the crate.)

# Slab-based B-Tree implementation

<table><tr>
	<td><a href="https://docs.rs/btree-slab">Documentation</a></td>
	<td><a href="https://crates.io/crates/btree-slab">Crate informations</a></td>
	<td><a href="https://github.com/timothee-haudebourg/btree-slab">Repository</a></td>
</tr></table>

This crate provides an alternative implementation to
the standard `BTreeMap` and `BTreeSet` data structures
based on a slab data-structure.
In principle,
this implementation is more flexible and more memory efficient.
It is more flexible by providing an extended set of
low-level operations on B-Trees through the `BTreeExt` trait which can be used
to further extend the functionalities of the `BTreeMap` collection.
In addition,
the underlying node allocation scheme is abstracted by a type parameter
that can be instanciated by any data strucutre
implementing slab-like operations.
By default,
the `Slab` type (from the `slab` crate) is used,
which means that every node of the tree are allocated in a contiguous memory region,
reducing the number of allocations needed.
In theory, another type could be used to store the entire B-Tree on the stack.

## Usage

From the user point of view,
the collection provided by this crate can be used just like
the standard `BTreeMap` and `BTreeSet` collections.
```rust
use btree_slab::BTreeMap;

// type inference lets us omit an explicit type signature (which
// would be `BTreeMap<&str, &str>` in this example).
let mut movie_reviews = BTreeMap::new();

// review some movies.
movie_reviews.insert("Office Space",       "Deals with real issues in the workplace.");
movie_reviews.insert("Pulp Fiction",       "Masterpiece.");
movie_reviews.insert("The Godfather",      "Very enjoyable.");
movie_reviews.insert("The Blues Brothers", "Eye lyked it a lot.");

// check for a specific one.
if !movie_reviews.contains_key("Les Mis??rables") {
    println!("We've got {} reviews, but Les Mis??rables ain't one.",
             movie_reviews.len());
}

// oops, this review has a lot of spelling mistakes, let's delete it.
movie_reviews.remove("The Blues Brothers");

// look up the values associated with some keys.
let to_find = ["Up!", "Office Space"];
for movie in &to_find {
    match movie_reviews.get(movie) {
       Some(review) => println!("{}: {}", movie, review),
       None => println!("{} is unreviewed.", movie)
    }
}

// Look up the value for a key (will panic if the key is not found).
println!("Movie review: {}", movie_reviews["Office Space"]);

// iterate over everything.
for (movie, review) in &movie_reviews {
    println!("{}: \"{}\"", movie, review);
}
```

### Custom node allocation

One can use `btree_slab::generic::BTreeMap` to
use a custom slab type to handle nodes allocation.

```rust
use slab::Slab;
use btree_slab::generic::BTreeMap;

let mut map: BTreeMap<K, V, Slab<Node<K, V>>> = BTreeMap::new();
```

In this example,
the `Slab<Node<_, _>>` type is a slab-like data structure responsible for the nodes allocation.
It must implement all the traits defining the `cc_traits::Slab` trait alias.

## Extended API & Addressing

In this implementation of B-Trees, each node of a tree is addressed
by the `Address` type.
The extended API, visible through the `BTreeExt` trait,
allows the caller to explore, access and modify the
internal structure of the tree using this addressing system.
This can be used to further extend the functionalities of the `BTreeMap` collection,
for example in the `range-map` crate.

## License

Licensed under either of

 * Apache License, Version 2.0 ([LICENSE-APACHE](LICENSE-APACHE) or http://www.apache.org/licenses/LICENSE-2.0)
 * MIT license ([LICENSE-MIT](LICENSE-MIT) or http://opensource.org/licenses/MIT)

at your option.

### Contribution

Unless you explicitly state otherwise, any contribution intentionally submitted
for inclusion in the work by you, as defined in the Apache-2.0 license, shall be dual licensed as above, without any
additional terms or conditions.
