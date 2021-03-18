---
layout: post
title:  "Save (y)our time by reducing the number of required dependencies"
date:   2020-01-24 21:17:00 +0100
categories: rust
tags: [rust]
excerpt_separator:  <!--more-->
---

The ease of adding new dependencies in Rust is a great power that, as usual, comes with great responsibility. Here are some dos and don'ts to avoid too many dependencies in your Cargo.lock and thus reduce the amount of coffee you drink during the compilation process!
<!--more-->

# Required-features

If your project is both, an executable and a library and you have some dependencies that are only used in the executable, then instead of this:

```toml
[lib]
doctest = false

[[bin]]
name = "my_binary"

[dependencies]
docopt = "1.1.0" # this is only required in the binary
```

do this:

```toml
[lib]
doctest = false

[[bin]]
name = "my_binary"
required-features = ["docopt"]

[dependencies]
docopt = { version = "1.1.0", optional = true }
```

**Note**:
Currently, the above soltion means you have to add `--features docopt` when executing `cargo run`. It can be inconvenient.
One possible workaround is to use `default-features`:

```toml
[lib]
doctest = false

[[bin]]
name = "my_binary"
required-features = ["docopt"]

[features]
default = ["docopt"]

[dependencies]
docopt = { version = "1.1.0", optional = true }
```

This at least allows your users to opt-out from the docopt dependency using `default_features = false`.

References:
https://github.com/rust-lang/cargo/issues/4663
https://github.com/dmarcuse/rfcs/blob/master/text/0000-cargo-artifact-specific-dependencies.md

# Enable features only when really needed

If you only need some features of your dependencies for testing, then instead of

```toml
[dependencies]
toml = { version = "0.5", features = ["preserve_order"] }
```

do this:

```toml
[dependencies]
toml = { version = "0.5" }

[dev-dependencies]
toml = { version = "0.5", features = ["preserve_order"] }
```
