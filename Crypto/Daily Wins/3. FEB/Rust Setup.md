# Rust Setup Guide for Arch Linux + VS Code

This guide explains:

1. How to install Rust properly on Arch Linux
    
2. How to configure VS Code for professional Rust development
    

---

# 1️⃣ Install Rust on Arch Linux

## Step 1: Update System

```
sudo pacman -Syu
```


---

## Step 2: Install rustup (Recommended Method)

```
sudo pacman -S rustup
```

> Do NOT install the `rust` package directly. Use `rustup` for proper toolchain management.

---

## Step 3: Install Stable Toolchain

```
rustup default stable
```

This installs:

- `rustc` (compiler)
    
- `cargo` (package manager)
    
- Standard library
    
- Rust documentation
    
- Clippy and rustfmt
    

---

## Step 4: Verify Installation

```
rustup show rustup which rustc rustc --version cargo --version
```

You should see:

- Active toolchain: `stable-x86_64-unknown-linux-gnu`
    
- rustc path inside `~/.rustup/toolchains/`
    

---

## Step 5: Test Rust

```
cargo new test_project cd test_project cargo run
```

Expected output:

`Hello, world!`

---

# 2️⃣ Optional: Install Nightly Toolchain

Useful for experimental features and advanced projects.

```
rustup toolchain install nightly
```

Switch to nightly:

`rustup default nightly`

Switch back to stable:

`rustup default stable`

---

# 3️⃣ Add Useful Targets

## WASM Target (for smart contracts / web)

```
rustup target add wasm32-unknown-unknown
```

## Static Linux Builds


```
rustup target add x86_64-unknown-linux-musl
```

---

# 4️⃣ Install Development Components

```
rustup component add clippy rustfmt
```

- `clippy` → advanced linter
    
- `rustfmt` → auto formatter
    

---

# 5️⃣ Setup VS Code for Rust

---

## Step 1: Install VS Code (if not installed)

`sudo pacman -S code
```

---

## Step 2: Install Required Extensions

Install these extensions:

- rust-analyzer
    
- CodeLLDB
    
- Even Better TOML
    

Or via terminal:

`code --install-extension rust-lang.rust-analyzer code --install-extension vadimcn.vscode-lldb code --install-extension tamasfe.even-better-toml`

---

## Step 3: Configure rust-analyzer

Open VS Code → Settings → Open `settings.json`

Add:

`{   "rust-analyzer.checkOnSave.command": "clippy",   "rust-analyzer.cargo.allFeatures": true,   "rust-analyzer.procMacro.enable": true,   "editor.formatOnSave": true,   "[rust]": {     "editor.defaultFormatter": "rust-lang.rust-analyzer"   } }`

This enables:

- Clippy on save
    
- Procedural macros
    
- Auto formatting
    
- Full feature builds
    

---

# 6️⃣ Setup Debugging

Inside your Rust project:

Create:

`.vscode/launch.json`

Add:

`{   "version": "0.2.0",   "configurations": [     {       "type": "lldb",       "request": "launch",       "name": "Debug executable",       "cargo": {         "args": ["build", "--bin=${workspaceFolderBasename}"],         "filter": {           "name": "${workspaceFolderBasename}",           "kind": "bin"         }       },       "args": [],       "cwd": "${workspaceFolder}"     }   ] }`

Now press **F5** to debug.

---

# 7️⃣ Optional: Optimize Cargo Workflow

Edit:

`nano ~/.cargo/config.toml`

Add:

`[alias] r = "run" b = "build" t = "test" c = "clippy" rr = "run --release" br = "build --release"`

Now you can use:

`cargo r cargo c cargo br`

---

# Final Environment Structure (Arch)

`/usr/bin/rustc          → rustup shim /usr/bin/cargo          → rustup shim ~/.rustup/              → toolchains ~/.cargo/config.toml    → cargo config`

This is the correct Arch-native Rust setup.