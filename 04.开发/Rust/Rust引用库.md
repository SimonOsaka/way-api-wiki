1. 引用crates.io
   
   ```toml
   [dependencies]
   rand = "0.8.4"
   ```

2. 引用git
   
   ```toml
   [dependencies]
   rand = { git = "https://github.com/rust-random/rand", branch = "0.8.4" }
   ```

3. 引用local
   
   ```toml
   [dependencies]
   rand = { path = "../rand", version = "0.8.4" }
   ```
