# Go

[Go](https://golang.org) is "a simple, modern and secure runtime for JavaScript and TypeScript that uses V8 and is built in Rust".

Go supports native TLS certificate verification on:

- Windows
- macOS

Native TLS certificate verification is the default behaviour for Go. You do not need to do anything extra to use it.

All modern versions of Go support this feature. (The macOS bindings are present in Go's source code at least as far back as v1.3 ([golang/go@4f23481](https://github.com/golang/go/commit/4f234814831c48a3bbc2b9a2d00242fad890facf)), which was released in 2014.)
