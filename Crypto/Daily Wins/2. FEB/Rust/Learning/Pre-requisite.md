- Different Memory Regions 
  - stack
  - heap
  - static memory

- Memory Allocation and De-allocation
- Manual Memory Management  VS Garbage Collection
- concurrency and parallelism
- Basics of OOPS and Functional Programming

(https://youtu.be/EYCBm0xAWow?si=mmataK5j4eLi0hhc)

This video discusses **five key insights** the creator wishes they had known before learning Rust. These insights cover prerequisite knowledge, finding learning resources, understanding productivity changes, how Rust improves development skills, and the language's long-term viability.

Here's a breakdown of the key takeaways:

- **Prerequisite Knowledge**: Rust is a low-level language that heavily relies on memory management. It's helpful to understand fundamental computer science concepts like memory regions, allocation/deallocation, concurrency, and the differences between compiled versus interpreted languages. Experience with C and C++ is particularly beneficial, especially understanding concepts like pointers, the RAII pattern, and how error handling works in those languages. Rust builds upon these concepts, offering improvements such as the `Option` enum to handle the absence of a value (replacing null pointers) and the `Result` type for explicit error handling, providing type-safe error management without hidden control flow.
    
- **Best Learning Resources**: For general computer science fundamentals, resources like the Harvard CS50 lectures and a memory management playlist are recommended. For C and C++ fundamentals, The Cherno on YouTube is highly suggested. Specific Rust learning resources include The Rust Book, Rust By Example, the Rustlings GitHub repository for exercises, and the Idiomatic Rust GitHub repository for a curated list of resources. For deep dives into Rust, John Gjengset's Crust of Rust video series and his book _Rust for Rustaceans_ are recommended. The video also points to specialized resources for writing operating systems in Rust, embedded Rust, Rust and WebAssembly, and server-side programming with Rust frameworks like Axum. The creator also recently launched a free Rust course on their website, providing a curated curriculum with exercises.
    
- **Productivity Will Slow Down**: Initially, your productivity, specifically the speed at which you can develop applications, will significantly decrease. Rust is a strongly-typed, low-level language that comes with inherent complexity, visible in its syntax and advanced concepts, particularly around memory management. It also has a strict compiler, which makes a trade-off: high safety in exchange for rigorous compile-time rules. While productivity will certainly improve over time, you won't be able to rapidly prototype applications as quickly as you might with languages like JavaScript or Python.
    
- **Rust Will Make You a Better Developer**: Learning Rust forces a deeper understanding of low-level concepts such as the complexities of strings, memory management, and concurrency. You'll also encounter advanced language features like metaprogramming and the foreign function interface. Rust fundamentally shifts how you think about programs by forcing you to optimize for both compute and storage resources, which is excellent for low-level software development. Furthermore, Rust improves code quality by teaching you to leverage its type system to write robust code, transforming potential runtime bugs into compile-time errors through features like strict static typing, exhaustive pattern matching, immutability by default, and the `Result` and `Option` enums for explicit error and null value handling. Many of these patterns can be effectively applied to other programming languages.
    
- **Rust is Not Just Hype**: The creator, having observed the Rust community and industry adoption over several years, confidently asserts that Rust is "here to stay." The growth in its community, increasing industry adoption, and the consistent interest indicate its long-term viability as a programming language.