---
layout: post
title:  "Panic! within a panic! can make you panic!"
date:   2020-03-18 21:20:00 +0100
categories: rust
tags: [rust]
excerpt_separator:  <!--more-->
---

Overall, you probably want to avoid "panic!" as much as possible. However, there are many scenarios where "panic!" is exactly what you want. Be careful though, because if by accident you call a panicing function inside your `panic!`, you may encounter quite missleading error message, such as "signal: 4, SIGILL: illegal statement".

tl;dr

Don't:
```
fn foo(bar: u8) {
  if bar == 0 { panic!("I don't like 0"); }
}

fn main() {
  panic!("foo: {}", foo(0))
}
```

Do:
```
fn foo(bar: u8) {
  if bar == 0 { panic!("I don't like 0"); }
}

fn main() {
  let msg = foo(0);
  panic!("foo: {}", msg)
}
```

<!--more-->

When I first encountered this problem, I thought there was a compilation error problem and LLVM generated an instruction that is not supported by my CPU. It was counterintuitive because I wasn't playing with `target-cpu` or anything like that, but the exception kept telling me

```
Caused by:
  process didn't exit successfully: `/foo/target/debug/deps/bar-f73819a7f382a28f baz` (signal: 4, SIGILL: illegal instruction)
```

Even the syslog tried to convince me - a developer who knows only some basic facts about low-level stuff - that it is indeed a problem with unsupported instruction:

```
Mar 18 11:47:22 sasik520 kernel: [ 9073.821168] traps: qux::quux72318] trap invalid opcode ip:562a86d375a2 sp:7f9a98ff87f0 error:0 in bar-f73819a7f382a28f[562a85aa7000+12bb000]
```

The `RUST_BACKTRACE = 1` variable was not very helpful in this case. To my surprise, running the program (which in my case was a test ran with `cargo test`) under `gdb` also didn't bring much detail! Fortunately, after a few more investigations and with the help of friends and the internet, I found out how to deal with this error! This is what I've learned:

- to run tests with gdb, use

```
gdb --args cargo test
```

- to enable access to the backtrace, enter

```
set follow-fork-mode child
```

- it's also worth not to stop at SIGUSR1, which are spawned quite often during tests:

```
handle SIGUSR1 noprint nostop
```

- finally, run your program with the command `run` and when the panic! occurs, check the backtrace with `bt`

- additionally, these instructions can be useful for generating and making use of a core dump if you don't already have one: https://stackoverflow.com/a/40223712/540761

Happy debugging!