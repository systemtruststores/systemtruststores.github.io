The **native-certs** project tracks initiatives to make major programming languages 'just work' with TLS certificates from operating system trust stores.

The following languages (or their runtimes) have **built-in support** for doing TLS certificate verification via the OS trust store:

| Language | Windows support? | macOS support? | Default behavior? | Since          | How to use                         |
|----------|------------------|----------------|-------------------|--------------- |------------------------------------|
| Deno     | Yes              | Yes            | No                | v1.13.0        | [Guide](javascript/deno/index.md)  |
| Go       | Yes              | Yes            | Yes               | v1.3           | [Guide](go/index.md)               |
| Java     | Partial          | Partial        | No                | Before Java 8  | [Guide](java/index.md)             |
| .NET     | Yes              | Yes            | Yes               | v2.0.0 (macOS) | [Guide](dotnet/index.md)           |
| Swift    | ?                | Yes            | Yes               | v1             | [Guide](swift/index.md)            |

The following languages require you to install a **library** that does TLS certificate verification via the OS trust store:

| Language | Library                                                              | Status |
|----------|----------------------------------------------------------------------|--------|
| Node     | [node-native-certs](https://github.com/bnoordhuis/node-native-certs) | WIP    |
| Python   | [truststore](https://github.com/sethmlarson/truststore)              | WIP    |
| Rust     | [rustls-native-certs](https://github.com/rustls/rustls-native-certs) | Stable |

You can help to improve these lists by opening a language support ticket at [GitHub Issues](https://github.com/native-certs/native-certs.github.io/issues).

If you're interested in the problem that this solves, or how the solutions are implemented, read on.

## The Problem

Have you ever seen an error like this while using your favourite language or CLI tool...

```
[SSL: CERTIFICATE_VERIFY_FAILED]
certificate verify failed:
unable to get local issuer certificate
```

...while visiting the same URL in your browser works just fine?

This error occurs because your tool or programming language does not integrate with your operating system's trust store, which houses your TLS certificates. As such, your OS may trust the certificate in question, but your tool (or language) does not know this, so it rejects the certificate (or asks you whether to trust it).

It often occurs in the following scenarios:

- Corporate environments which perform TLS inspection
- Development with a local TLS proxy (e.g. for debugging)

It causes a number of further difficulties:

- It breaks the vast majority of developer tools (unless tool-specific workarounds are applied). This significantly impedes development work.
- It trains users and developers to click through TLS certificate warnings without thinking. This is a Bad Thing because one day they will encounter a malicious TLS certificate, and they will simply accept it.

## The Workarounds

There are a number of hacks / workarounds for the problem today. As we shall see, none of them are really a solution.

### Disable TLS verification

The first hack that people usually resort to is turning off all TLS verification in the given tool or programming language.

Examples:

```shell
curl -k https://example.com
```

```shell
NODE_TLS_REJECT_UNAUTHORIZED=0
node ./example.js
```

This makes the TLS error go away, but is **a terrible idea** for many obvious reasons.

### Set tool-specific certificate options

The second way is to use the tool- or language-specific CLI options to add the extra certificate(s) to the search path.

Examples:

```shell
wget --ca-certificate=/path/to/your/certificate.crt  https://example.com
```

```shell
NODE_EXTRA_CA_CERTS=/path/to/your/certificate.crt
node ./example.js
```

This has the following problems:

- It requires duplication of the certificate(s) in question outside of the OS trust store. The user must then keep these duplicated certificates up to date when they are rotated.
- In an average development workstation, 10s to 100s of tools may require this fix. This requires adding (and maintaining) **a mile-long list of workarounds** to your shell profile!

### Bundle the custom TLS certificate(s) in a library

A less common workaround is to use a library as a bundle of the custom TLS certificate(s). This library is then shipped to developers through a repository like PyPi or NPM. (This is the essence of what Certifi does.)

However, as Seth Larson articulated during the PyCon 2022 [The future of trust stores in Python](https://youtu.be/1IiL31tUEVk?t=698) ([slides](https://speakerdeck.com/sethmlarson/the-future-of-trust-stores-in-python)) talk, package repositories don't make great TLS certificate distribution systems. There is a better way.

## The Solution

You could apply a bunch of workarounds, but why should you have to? Native applications on your system handle TLS certificates without fuss, so your other tools should be able to do this too.

**The real solution is for CLI tools and programming languages to integrate with the native OS trust store.**  (With the option to turn this off for debugging, or compatibility with legacy environments.)

This means:

- The system manages provisioning, updates, and revocation of certificates
- Intermediate fetching is supported
- No need for per-application certificate configuration
- No need to duplicate certificates outside of the trust store
- The system manages custom certificates (e.g. corporate TLS inspection certificates)

### on macOS

On macOS the trust store is the Keychain.

You can integrate with the Keychain in the following ways:

- `Security.framework`: this is the low-level approach where you fetch all TLS certificates from the Keychain as an array of ASCII-armored strings, and your app code decides what to do with them. This has the advantage that it will work with existing HTTPS libraries.
- `URLSession`: this is the high-level approach where your language's HTTPS client becomes a wrapper for Apple's `URLSession` HTTPS client. This means that that you get any TLS certificate handling optimizations in `URLSession` (e.g. caching) 'for free'.

### on Windows

On Windows the trust store is the Windows Certificate Store.

You can integrate with the Windows Certificate Store in the following ways:

- `schannel`
- `CryptoAPI`

### on Linux

Linux distributions don't have a single keychain-like trust store API that you can use. (The closest offerings are perhaps GNOME Keyring or KDE Wallet, but they are tied to their respective ecosystems.) Instead certificates are stored directly on the filesystem, following the Filesystem Hierarchy Standard.

The typical approach that native trust store integration wrappers take is to let OpenSSL look up the certificates on Linux (or at least, use OpenSSL conventions to determine the certificate search path), but step in to customize TLS certificate handling on other systems.
