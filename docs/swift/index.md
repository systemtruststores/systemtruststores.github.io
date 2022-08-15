# Swift

[Swift](https://swift.org/) is "a high-performance system programming language [with] a clean and modern syntax". It is the standard language for native application development on Apple's platforms.

This guide also applies to Objective-C.

Swift itself is just a language, so it does not know anything about TLS. But due to Swift's Apple-centric nature, it is reasonable to assume that almost every Swift application does TLS connections in one of two ways. The high-level way is to use `URLSession`, the native HTTP(S) client in `Foundation`. The low-level way is to fetch TLS root certificates with `Security.framework`. On macOS, these APIs leverage Apple's Keychain internally. Therefore we can effectively make the following statements...

Swift supports native TLS certificate verification on macOS.

Native TLS certificate verification is the default behaviour for Swift. You do not need to do anything extra to use it.

---

A small number of Swift apps may use different networking libraries (e.g. Boost.ASIO), particularly if they are built around a cross-platform C library for code sharing. These apps depend on the networking library supporting native TLS certificate verification.
