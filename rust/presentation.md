---
marp: true
theme: renuo
---
<!-- _class: renuo -->

# Rust
## Two hours into Rust

##### 2020-05-28 by Alessandro

---

# Goal

Learn more about Rust and refactor a small part of the code of https://github.com/renuo/hamster

---

# What I want to improve

```
let output_filename = format!("data_out/4_{}", image_name);
h.to_rgb().save(output_filename.clone()).unwrap();
let _result = Command::new("open").arg(output_filename).spawn();
```

---

# What I want to improve

```
let output_filename = format!("data_out/4_{}", image_name);
h.to_rgb().save(output_filename).unwrap();
let _result = Command::new("open").arg(output_filename).spawn();
```

---

```
error[E0382]: use of moved value: `output_filename`
  --> src/bin/main.rs:10:48
   |
8  |         let output_filename = format!("data_out/4_{}", image_name);
   |             --------------- move occurs because `output_filename` has type `std::string::String`, which does not implement the `Copy` trait
9  |         h.to_rgb().save(output_filename).unwrap();
   |                         --------------- value moved here
10 |         let _result = Command::new("open").arg(output_filename).spawn();
   |                                                ^^^^^^^^^^^^^^^ value used here after move

error: aborting due to previous error

For more information about this error, try `rustc --explain E0382`.
```

---

# rustc --explain E0382

:smile:

A variable was used after its contents have been moved elsewhere.

Erroneous code example:

```
struct MyStruct { s: u32 }

fn main() {
    let mut x = MyStruct{ s: 5u32 };
    let y = x;
    x.s = 6;
    println!("{}", x.s);
}
```

---

Since `MyStruct` is a type that is not marked `Copy`, the data gets moved out
of `x` when we set `y`. This is fundamental to Rust's ownership system: outside
of workarounds like `Rc`, a value cannot be owned by more than one variable.

Sometimes we don't need to move the value. Using a reference, we can let another
function borrow the value without changing its ownership. In the example below,
we don't actually have to move our string to `calculate_length`, we can give it
a reference to it with `&` instead.

```
fn main() {
    let s1 = String::from("hello");

    let len = calculate_length(&s1);

    println!("The length of '{}' is {}.", s1, len);
}

fn calculate_length(s: &String) -> usize {
    s.len()
}
```

---

```
h.to_rgb().save(&output_filename).unwrap();
let _result = Command::new("open").arg(&output_filename).spawn();
```

---

# Really nice error explanation!

But what am I really doing, and why?

---

# Stack and Heap

Stack is fixed size. Heap is dynamic.

When new space is needed, is allocated on the heap, and a pointer is returned.

When that data is not needed anymore, the pointer is deleted, and data can be removed/overridden.

---

# That's Aaron job

In Rust you must understand the concept of Ownership

---

# Ownership

Keeping track of what parts of code are using what data on the heap, minimizing the amount of duplicate data on the heap, and cleaning up unused data on the heap so you don’t run out of space are all problems that ownership addresses. 

---

# Three rules of Ownership

There are three rules about ownership in Rust:

* Each value in Rust has a variable that’s called its owner.

* There can only be one owner at a time.

* When the owner goes out of scope, the value will be dropped.

---

<!-- _class: renuo -->

# ![drop-shadow portrait](../images/alessandro.jpg)

* https://github.com/renuo/hamster
* https://medium.com/@thomascountz/ownership-in-rust-part-1-112036b1126b
* https://doc.rust-lang.org/book

# Thank you!