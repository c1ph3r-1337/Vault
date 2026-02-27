- In Rust by default all variables are immutable.
- we can make it mutable by adding `mut` before the variable name.
- Before![[Pasted image 20260227155722.png]]
  ![[Pasted image 20260227155745.png]]
- After
  ```
	fn main() {
	let mut age = 20;
	age = 32;
	println!("My age is {age} ")
	```
![[Pasted image 20260227160000.png]]