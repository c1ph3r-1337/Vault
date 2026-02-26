- rust book https://doc.rust-lang.org/stable/book/

- " rustfmt file_name.rs " - Used to Format the code the according to the standard style .
- " rustc file_name.rs " is used to compile the rust code. 
- rustc is fine for simple projects but for the bigger projects it get difficult to manage and  share the code , for that we need the "cargo" - a package manager just like "npm" . 
- for example "cargo new hello_rust" then it makes a the some files like main.rs , .gitingore , cargo.toml just like packages.json 
![[Pasted image 20260225140204.png]]
![[Pasted image 20260225140311.png]]

- Lets talk about the cargo.toml -  "toml" means tom's obvious minimal language 
![[Pasted image 20260225230429.png]]

- Packages are referred to as crates
![[Pasted image 20260226180105.png]]
- cargo build compiles the code automatically and make a target folder and in the target folder it makes the debug folder and in that it it stores the binary of the code.
- a new file is also made named cargo.lock and it keep the record of the versions of the dependencies in the project. Don't ever change it manually .
- or simple run the debug binary using cargo run