# swift-bridge [![Actions Status](https://github.com/chinedufn/swift-bridge/workflows/test/badge.svg)](https://github.com/chinedufn/swift-bridge/actions) [![docs](https://docs.rs/swift-bridge/badge.svg)](https://docs.rs/swift-bridge)

> `swift-bridge` facilitates Rust and Swift interop.

`swift-bridge` is a library that lets you pass and share high-level types such as `Option<T>`, `String`, `Struct` and `Class` between Rust and Swift.

You declare the types and functions that you want to import and export using "bridge modules", and
then run `swift-bridge-build` at build time to parse your bridge modules and automatically generate the
`Swift` and `C` FFI layer to them work.

## Installation

```toml
# In your Cargo.toml

[build-dependencies]
swift-bridge-build = "0.1"

[dependencies]
swift-bridge = "0.1"
```

## Book

You can find information about using Rust and Swift together in [The `swift-bridge` book](https://chinedufn.github.io/swift-bridge).

## Quick Peek

You declare your FFI boundary using one or more bridge modules annotated with the `swift_bridge::bridge` attribute macro.

Later, during your Cargo build script, you use `swift-bridge-build` in order to parse your bridge modules and generate the
corresponding Swift and C code to make it all work.

Here's how you might declare a bridge module on the Rust side in order to export types to and import types from Swift.

```rust
// Use the `swift_bridge::bridge` macro to declare a bridge module that
// `swift-bridge-build` will parse during your Cargo build script
// in order to generate the necessary Swift and C FFI glue code.
#[swift_bridge::bridge]
mod ffi {
    // Create shared structs where both Rust and Swift can directly access the fields.
    struct AppConfig {
        file_manager: CustomFileManager,
    }

    // Export Rust types, functions and methods for Swift to use.
    extern "Rust" {
        type RustApp;

        #[swift_bridge(init)]
        fn new(config: AppConfig);
        
        fn insert_user(&mut self, user_id: u32, user: User);
        fn get_user(&self, user_id: u32) -> Option<&User>;
    }

    extern "Rust" {
        type User;

        #[swift_bridge(init)]
        fn new(user_id: u32, name: String, email: Option<String>) -> User;
    }

    // Import Swift classes and functions for Rust to use.
    extern "Swift" {
        type CustomFileManager;
        fn save_file(&self, name: &str, contents: &[u8]);
    }
}
```

## Quick Start

The `swift-bridge` repository contains [example applications](examples) that you use to quickly try out the library,
or as a starting point for your own `Swift` + `Rust` based application.

For example, here's how to run the [`ios-rust-analyzer`](examples/ios-rust-analyzer) example project locally.

```sh
git clone https://github.com/chinedufn/swift-bridge
cd swift-bridge/examples/ios-rust-analyzer

open IosRustAnalyzer/IosRustAnalyzer.xcodeproj
# *** Click the "Run" button at the top left of Xcode ***
```

---

You can find information about using Rust and Swift together in [The `swift-bridge` book](https://chinedufn.github.io/swift-bridge).

## Built-In Types

In addition to allowing you to share your own custom structs, enums and classes between Rust and Swift,
`swift_bridge` comes with support for a number of Rust and Swift standard library types.

<!-- NOTE: Whenever we modify this list we need to copy it over to the book's built in types chapter README  -->

| name in Rust                                                    | name in Swift                                                    | notes               |
| ---                                                             | ---                                                              | ---                 |
| u8, i8, u16, i16... etc                                         | UInt8, Int8, UInt16, Int16 ... etc                               |                     |
| bool                                                            | Bool                                                             |                     |
| String, &String, &mut String                                    | RustString, RustStringRef, RustStringRefMut                      |                     |
| &str                                                            | RustStr                                                          |                     |
| Vec\<T>                                                         | RustVec\<T>                                                      |                     |
| SwiftArray\<T>                                                  | Array\<T>                                                        | Not yet implemented |
| &[T]                                                            | UnsafeBufferPointer\<T>                                          |                     |
| &mut [T]                                                        | UnsafeMutableBufferPointer\<T>                                   | Not yet implemented |
| SwiftString                                                     | String                                                           |                     |
| Box<T>                                                          |                                                                  | Not yet implemented |
| [T; N]                                                          |                                                                  | Not yet implemented |
| *const T                                                        | UnsafePointer\<T>                                                |                     |
| *mut T                                                          | UnsafeMutablePointer\<T>                                         |                     |
| Option\<T>                                                      | Optional\<T>                                                     |                     |
| Result\<T>                                                      |                                                                  | Not yet implemented |
| Have a Rust standard library type in mind?<br /> Open an issue! |                                                                  |                     |
|                                                                 | Have a Swift standard library type in mind?<br /> Open an issue! |                     |

## To Test

To run the test suite.

```sh
# Clone the repository
git clone git@github.com:chinedufn/swift-bridge.git
cd swift-bridge

# Run tests
cargo test --all && ./test-integration.sh
```

## Phases

#### Phase 1 (Current Phase): Make it Possible

Bridging Rust and Swift is fairly unexplored territory, so it will take some experimentation in order to
figure out the right APIs and code generation.

During this phase we'll focus on adding support for more types, patterns and and common use cases
that we discover.

While we must always be thoughtful, we won't be obsessively focused on what the best names,
arguments or approaches are during this phase.

#### Phase 2: Make it Ergonomic

This phase will be focused on making `swift-bridge` feel really good to use.

During this phase we will:

- Simplify our APIs and make them consistent.

- Improve our error messages.

- Improve the information and examples in the book.

#### Phase 3: Make it Stable

This phase is about getting `swift-bridge` to version `1.0.0`.

We'll take inventory of all of our public APIs and aim to remove as much we
can without impacting the libraries usability.

---

The `0.1.x` versions will not follow semver.

We will maintain semver from `0.2` and onwards.

---

#### License

<sup>
Licensed under either of <a href="LICENSE-APACHE">Apache License, Version
2.0</a> or <a href="LICENSE-MIT">MIT license</a> at your option.
</sup>

<br>

<sub>
Unless you explicitly state otherwise, any contribution intentionally submitted
for inclusion in this project by you, as defined in the Apache-2.0 license,
shall be dual licensed as above, without any additional terms or conditions.
</sub>
