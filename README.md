# oppgave - A simple Redis-based task queue

[![crates.io](http://meritbadge.herokuapp.com/oppgave)](https://crates.io/crates/oppgave)
[![Build Status](https://travis-ci.org/badboy/oppgave.svg?branch=master)](https://travis-ci.org/badboy/oppgave)
[![Clippy Linting Result](https://clippy.bashy.io/github/badboy/oppgave/master/badge.svg)](https://clippy.bashy.io/github/badboy/oppgave/master/log)

Inspired by [Ost](https://github.com/soveran/ost) and [Kute](https://github.com/moonglum/kute).

A small reliable queue on top of Redis. Allows to push tasks and fetch them again.
Can handle whatever task object you hand it, as long as it can be encoded and decoded to and from JSON.

## The name: oppgave - task

`oppgave` is Norwegian for task.
So `oppgave` is a **oppgave kø**, a **task queue**.

Sadly, characters like `ø` don't play to well with stable Rust. [Non-ASCII identifiers are feature-gated](https://github.com/rust-lang/rust/issues/28979).

It would be possible with nightly and the `non_ascii_idents` feature to use `oppgave-kø`, but *crates.io* doesn't accept crates with non-ascii characters in the name. :(

## [Documentation][]

[Documentation is available online.][documentation]

[documentation]: http://badboy.github.io/oppgave

## Installation

Add it to your dependencies in `Cargo.toml`

```toml
[dependencies]
oppgave = "0.1.0"
```

## Example: Producer

See [`examples/worker.rs`](examples/worker.rs) for a working example.
Run it with `cargo run --example worker`.

```rust
#[derive(Deserialize, Serialize)]
struct Job { id: u64 }

let client = redis::Client::open("redis://127.0.0.1/").unwrap();
let con = client.get_connection().unwrap();
let producer = Queue::new("default".into(), con);

producer.push(Job{ id: 42 });
```

## Example: Worker

See [`examples/worker.rs`](examples/worker.rs) for a working example.
Run it with `cargo run --example worker`.

```rust
#[derive(Deserialize, Serialize)]
struct Job { id: u64 }

let client = redis::Client::open("redis://127.0.0.1/").unwrap();
let con = client.get_connection().unwrap();
let worker = Queue::new("default".into(), con);

while let Some(task) = worker.next() {
    println!("Working with Job {}", job.id);
}
```

## License

MIT. See [LICENSE](LICENSE).
