- rust book https://doc.rust-lang.org/stable/book/
### "rustfmt"

- " rustfmt file_name.rs " - Used to Format the code the according to the standard style .
### "rustc"

- " rustc file_name.rs " is used to compile the rust code. 
### "cargo"

- rustc is fine for simple projects but for the bigger projects it get difficult to manage and  share the code , for that we need the "cargo" - a package manager just like "npm" . 
### "cargo new"

- for example "cargo new hello_rust" then it makes a the some files like main.rs , .gitingore , cargo.toml just like packages.json 


![[Pasted image 20260225140204.png]]
![[Pasted image 20260225140311.png]]

### "Cargo.toml"

- Lets talk about the cargo.toml -  "toml" means tom's obvious minimal language 
![[Pasted image 20260225230429.png]]

- Packages are referred to as crates

### "cargo build"

![[Pasted image 20260226180105.png]]
- cargo build compiles the code automatically and make a target folder and in the target folder it makes the debug folder and in that it it stores the binary of the code.
- a new file is also made named cargo.lock and it keep the record of the versions of the dependencies in the project. Don't ever change it manually .
- or simple run the debug binary using cargo run

### "cargo run"

![[Pasted image 20260226185711.png]]
- cargo run does two jobs it compile and run it .

### "cargo check"

- cargo check - it checks if there is any error in the code or syntax and informs .
- if everything is correct then it just compiles but doesn't make any binary or executable as in production grade project it would be faster than cargo build and to check if the code is right.

![[Pasted image 20260226191124.png]]

### "cargo build --release"

- it makes the release folder in the target folder for publication of the project 
![[Pasted image 20260226192258.png]]
- also used at the time of benchmark 