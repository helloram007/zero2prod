# zero2prod Documentation

A minimal Rust project used to demonstrate development workflow and tooling tips.

## Table of contents

- Inner development loop
- Faster linking
- Tooling to speed the loop (cargo-watch)
- Continuous integration (CI)
  - Tests
  - Code coverage
  - Linting
  - Formatting
  - Security checks
  - Ready-to-go CI pipelines

## Inner development loop

Typical cycle when developing:
- Make a change
- Compile or check the code
- Run tests
- Run the application

Speeding up this loop increases iteration velocity and developer productivity.

## Faster linking

Linking can dominate incremental rebuild time. Using LLVM's lld (or other fast linkers) often reduces that time.

Example `.cargo/config.toml` to prefer lld:

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

Notes on alternatives:
- On Linux try `mold` as an alternative linker (fast, younger project).
- On macOS there are equivalents (e.g., `sold`), but verify stability for your toolchain.

### Platform install notes

Windows (MSVC)
```powershell
# Install helper tools
cargo install -f cargo-binutils
rustup component add llvm-tools-preview
```

Linux
```bash
# Ubuntu
sudo apt-get install lld clang

# Arch
sudo pacman -S lld clang
```

macOS
```bash
brew install llvm
# Follow `brew info llvm` for PATH/ld64.lld location on Apple Silicon
```

After installing, run incremental builds to measure improvements.

## Tooling to speed the loop — cargo-watch

cargo-watch runs commands automatically when files change, reducing perceived wait time.

Install:
```bash
cargo install cargo-watch
```

Examples:
```bash
# Run `cargo check` on every change
cargo watch -x check

# Chain commands (check → test → run)
cargo watch -x "check" -x "test" -x "run"
```

When you edit in your IDE, cargo-watch can already be compiling in the background.

## Continuous Integration (CI)

A healthy CI pipeline ensures the main branch is deployable at any time. Typical automated checks:

### Tests
Run unit and integration tests:
```bash
cargo test
```

CI often runs `cargo build` or uses incremental caching before tests to speed execution.

### Code coverage
Use `cargo-llvm-cov` to compute LLVM-based coverage.

Install:
```bash
rustup component add llvm-tools-preview
cargo install cargo-llvm-cov
```

Run:
```bash
cargo llvm-cov
```

`cargo-llvm-cov` supports uploading coverage to services like Codecov.

### Linting
Use Clippy for static analysis.

Install (if missing):
```bash
rustup component add clippy
```

Run and treat warnings as errors in CI:
```bash
cargo clippy -- -D warnings
```

Mute specific lints in code with `#[allow(clippy::lint_name)]` or configure `clippy.toml`.

### Formatting
Auto-format with rustfmt to avoid formatting nitpicks in PR reviews.

Install (if missing):
```bash
rustup component add rustfmt
```

Format locally:
```bash
cargo fmt
```

CI check:
```bash
cargo fmt -- --check
```

### Security checks
Scan dependencies for known vulnerabilities with `cargo-audit`.

Install:
```bash
cargo install cargo-audit
```

Run:
```bash
cargo audit
```

Consider running these checks on every commit and on a daily schedule for projects in production.

Other tools: `cargo-deny` provides more advanced dependency policies (license checks, duplicates, unmaintained crates).

### Ready-to-go CI pipelines
Use prepared CI templates (for GitHub Actions, GitLab CI, etc.) that run the steps above. Start from a template and adapt provider-specific configuration to your project.

## Further reading / next steps

- Add a `.cargo/config.toml` if you plan to switch linkers.
- Add CI workflow files in `.github/workflows/` to enforce checks on pushes and PRs.
- Consider editor integrations: format-on-save and Clippy runs to keep the inner loop fast.

// ...existing code...
{ changed code }
// ...existing code...