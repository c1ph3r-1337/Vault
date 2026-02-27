- By default, Rust has a set of items defined in the standard library that it brings into the scope of every program. This set is called the _"prelude"_. 
- for example "string" `String::new();`
- But for input/output we need to import those. using use `std::io;` in the starting of the code.
- in the `std::io` ,  `::` shows that the `io` is sub library of the `std`
- 
  ```
  let apples = 5; // immutable 
  let mut bananas = 5; // mutable
  ```
  
![[main.rs]]
