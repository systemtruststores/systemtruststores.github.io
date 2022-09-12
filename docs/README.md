The **systemtruststores** project tracks initiatives to make major programming languages 'just work' with TLS certificates from your operating system's native truststore.

If you've ever encountered a TLS error like this:

```
[SSL: CERTIFICATE_VERIFY_FAILED]
certificate verify failed:
unable to get local issuer certificate
```

Then system truststores can help you!

If you're interested in the problem that they solve, or how language support can be implemented for them, read the in-depth [Rationale](rationale/index.md).

## Built-in Support

The following languages (or their runtimes) have **built-in support** for doing TLS certificate verification via the system truststore on the following platforms:

### macOS

On macOS the truststore is the Keychain.

| Language | System truststore support? | Default behavior? | Since          | How to use                                   |
|----------|----------------------------|-------------------|----------------|----------------------------------------------|
| Deno     | Yes                        | No                | v1.13.0        | [Guide](languages/javascript/deno/index.md)  |
| Go       | Yes                        | Yes               | v1.3           | [Guide](languages/go/index.md)               |
| Java     | Partial                    | No                | Before Java 8  | [Guide](languages/java/index.md)             |
| .NET     | Yes                        | Yes               | v2.0.0         | [Guide](languages/dotnet/index.md)           |
| Swift    | Yes                        | Yes               | v1             | [Guide](languages/swift/index.md)            |

### Windows

On Windows the truststore is the Windows Certificate Store.

| Language | System truststore support? | Default behavior? | Since          | How to use                                   |
|----------|----------------------------|-------------------|----------------|----------------------------------------------|
| Deno     | Yes                        | No                | v1.13.0        | [Guide](languages/javascript/deno/index.md)  |
| Go       | Yes                        | Yes               | v1.3           | [Guide](languages/go/index.md)               |
| Java     | Partial                    | No                | Before Java 8  | [Guide](languages/java/index.md)             |
| .NET     | Yes                        | Yes               | v1             | [Guide](languages/dotnet/index.md)           |
| Swift    | Yes                        | Yes               | v1             | [Guide](languages/swift/index.md)            |

### Android

On Android the truststore is the System Trust Store.

| Language | System truststore support? | Default behavior? | Since      | How to use                       |
|----------|----------------------------|-------------------|------------|----------------------------------|
| Java     | Yes                        | Yes               | Android v? | [Guide](languages/java/index.md) |

### Linux

Linux distributions don't have a truststore API like the platforms above. See the [Rationale](rationale/index.md) for more details.

## Libraries

The following languages require you to install a **library** that does TLS certificate verification via the system truststore:

| Language | Library                                                              | Status           |
|----------|----------------------------------------------------------------------|------------------|
| Node     | [node-native-certs](https://github.com/bnoordhuis/node-native-certs) | Work In Progress |
| Python   | [truststore](https://github.com/sethmlarson/truststore)              | Work In Progress |
| Rust     | [rustls-native-certs](https://github.com/rustls/rustls-native-certs) | Stable           |

Don't see your favourite language in these lists? You can help by opening a language support ticket at [GitHub Issues](https://github.com/native-certs/native-certs.github.io/issues).
