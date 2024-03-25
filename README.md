# Understanding Reference Counting and Cyclic Data Structures in Rust

This repository provides a demonstration of managing cyclic data structures using reference counting in Rust. The code showcases how to create a cyclic linked list structure using Rust's `Rc` (Reference Counted) and `RefCell` types.

## Overview

The Rust code in `main.rs` defines a linked list data structure with two variants: `Cons` representing a list node holding an integer value and a reference to the next node, and `Nil` representing the end of the list.

```rust
use std::cell::RefCell;
use std::rc::Rc;

#[derive(Debug)]
enum List {
    Cons(i32, RefCell<Rc<List>>),
    Nil,
}
```

## Implementation

The `List` enum is implemented with methods to facilitate operations on the linked list, such as accessing the next item (`tail()` method).

```rust
impl List {
    fn tail(&self) -> Option<&RefCell<Rc<List>>> {
        match self {
            Cons(_, item) => Some(item),
            Nil => None,
        }
    }
}
```

## Demonstration

The `main()` function demonstrates the creation of a cyclic linked list using reference counting. It creates two nodes, `a` and `b`, where `b` points back to `a`, creating a cycle.

```rust
fn main() {
    let a = Rc::new(Cons(5, RefCell::new(Rc::new(Nil))));

    // Printing initial reference counts and next item of 'a'
    println!("a initial rc count = {}", Rc::strong_count(&a));
    println!("a next item = {:?}", a.tail());

    // Creating 'b' with a reference to 'a'
    let b = Rc::new(Cons(10, RefCell::new(Rc::clone(&a))));
    println!("a rc count after b creation = {}", Rc::strong_count(&a));
    println!("b initial rc count = {}", Rc::strong_count(&b));
    println!("b next item = {:?}", b.tail());

    // Creating a cycle by changing 'a' to point to 'b'
    if let Some(link) = a.tail() {
        *link.borrow_mut() = Rc::clone(&b);
    }

    // Printing reference counts after creating a cycle
    println!("b rc count after changing a = {}", Rc::strong_count(&b));
    println!("a rc count after changing a = {}", Rc::strong_count(&a));

    // Uncomment the next line to see that we have a cycle;
    // it will overflow the stack
    // println!("a next item = {:?}", a.tail());
}
```

## Running the Code

To run the code, simply clone the repository and execute the following command:

```
cargo run
```

## Note

Uncommenting the last line in `main()` will demonstrate that the cyclic reference indeed exists, potentially causing a stack overflow due to infinite recursion.

## Conclusion

This example illustrates how Rust's ownership model, combined with reference counting and interior mutability, enables the creation and management of cyclic data structures safely and efficiently.
