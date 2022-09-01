# Swift

[Swift](https://swift.org/) is "a high-performance system programming language [with] a clean and modern syntax". It is the standard language for native application development on Apple's platforms.

(This guide also essentially applies to Objective-C.)

In Swift, TLS certificates are accessed in three main ways:

- HTTPS connections (`URLSession` in `Foundation`)
- TCP and UDP connections (`NWConnection` in `Network.framework`)
- Direct certificate lookup (`Sec...` APIs in `Security.framework`)

## macOS

On macOS, Swift uses Apple's implementations of these frameworks. Apple's implementations leverage the Keychain internally.

Therefore, Swift supports native TLS certificate verification on macOS. This the default behaviour; you do not need to do anything extra to use it.

## Windows

On Windows, Swift is built with open source implementations of these frameworks. For example, [swift-corelibs-foundation](https://github.com/apple/swift-corelibs-foundation) reimplements `Foundation`. That library uses Curl as its HTTPS driver, and on Windows, it builds Curl with the Schannel backend.

Therefore, Swift supports native TLS certificate verification on Windows. This the default behaviour; you do not need to do anything extra to use it.

## Footnote

A small number of Swift apps may use different networking libraries (e.g. Boost.ASIO), particularly if they are built around a cross-platform C library for code sharing. These apps depend on the networking library supporting native TLS certificate verification.
