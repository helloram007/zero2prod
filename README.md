// ...existing code...
# zero2prod

A minimal Rust project used to demonstrate development workflow and tooling tips.

## Inner development loop

When working on the project you will repeatedly:
- Make a change
- Compile the application
- Run tests
- Run the application

This cycle (the "inner development loop") determines how many iterations you can complete per unit time. Faster build/link cycles let you experiment more quickly.

## Faster linking

A significant portion of incremental rebuild time can be spent in the linking phase. The LLVM lld linker is often faster than the default linker. To use lld, install the appropriate tools for your platform and add a Cargo configuration to the project.

Example .cargo/config.toml to prefer lld:

```toml
# .cargo/config.toml
[target.x86_64-pc-windows-msvc]
rustflags = ["-C", "link-arg=-fuse-ld=lld"]

[target.x86_64-pc-windows-gnu]
rustflags = ["-C", "link-arg=-fuse-ld=lld"]

[target.x86_64-unknown-linux-gnu]
rustflags = ["-C", "linker=clang", "-C", "link-arg=-fuse-ld=lld"]

[target.x86_64-apple-darwin]
rustflags = ["-C", "link-arg=-fuse-ld=lld"]

[target.aarch64-apple-darwin]
rustflags = ["-C", "link-arg=-fuse-ld=/opt/homebrew/opt/llvm/bin/ld64.lld"]
```

Platform install notes:

- Windows (MSVC):
  - Install rust tool components:
    ```powershell
    cargo install -f cargo-binutils
    rustup component add llvm-tools-preview
    ```
  - If using the GNU toolchain, ensure lld is installed via your toolchain/package manager.

- Linux:
  - Ubuntu:
    ```bash
    sudo apt-get install lld clang
    ```
  - Arch:
    ```bash
    sudo pacman -S lld clang
    ```

- macOS:
  - Install LLVM via Homebrew:
    ```bash
    brew install llvm
    ```
  - Follow `brew info llvm` for adding the Homebrew LLVM tools to your PATH and for the correct lld path on Apple Silicon.

After installing and configuring, run a few incremental builds to measure improvement and adjust configuration as needed.
```// filepath: c:\Users\rrapo\rust_projects\zero2prod\README.md
// ...existing code...
# zero2prod

A minimal Rust project used to demonstrate development workflow and tooling tips.

## Inner development loop

When working on the project you will repeatedly:
- Make a change
- Compile the application
- Run tests
- Run the application

This cycle (the "inner development loop") determines how many iterations you can complete per unit time. Faster build/link cycles let you experiment more quickly.

## Faster linking

A significant portion of incremental rebuild time can be spent in the linking phase. The LLVM lld linker is often faster than the default linker. To use lld, install the appropriate tools for your platform and add a Cargo configuration to the project.

Example .cargo/config.toml to prefer lld:

```toml
# .cargo/config.toml
[target.x86_64-pc-windows-msvc]
rustflags = ["-C", "link-arg=-fuse-ld=lld"]

[target.x86_64-pc-windows-gnu]
rustflags = ["-C", "link-arg=-fuse-ld=lld"]

[target.x86_64-unknown-linux-gnu]
rustflags = ["-C", "linker=clang", "-C", "link-arg=-fuse-ld=lld"]

[target.x86_64-apple-darwin]
rustflags = ["-C", "link-arg=-fuse-ld=lld"]

[target.aarch64-apple-darwin]
rustflags = ["-C", "link-arg=-fuse-ld=/opt/homebrew/opt/llvm/bin/ld64.lld"]
```

Platform install notes:

- Windows (MSVC):
  - Install rust tool components:
    ```powershell
    cargo install -f cargo-binutils
    rustup component add llvm-tools-preview
    ```
  - If using the GNU toolchain, ensure lld is installed via your toolchain/package manager.

- Linux:
  - Ubuntu:
    ```bash
    sudo apt-get install lld clang
    ```
  - Arch:
    ```bash
    sudo pacman -S lld clang
    ```

- macOS:
  - Install LLVM via Homebrew:
    ```bash
    brew install llvm
    ```
  - Follow `brew info llvm` for adding the Homebrew LLVM tools to your PATH and for the correct lld path on Apple Silicon.

After installing and configuring, run a few incremental builds to measure improvement and adjust configuration as needed.

