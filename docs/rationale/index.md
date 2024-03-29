# Rationale

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

There are a number of hacks or workarounds for the problem today. As we shall see, none of them are really a solution.

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

This makes the TLS error go away, but is **a terrible idea** for many well-documented reasons.

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
- In an average development workstation, dozens of tools may require this fix. This requires adding (and maintaining) a bunch of workarounds to your shell profile.

### Bundle the custom TLS certificate(s) in a library

A less common workaround is to use a library as a bundle of the custom TLS certificate(s). This library is then shipped to developers through a repository like PyPi or NPM. (This is the essence of what Certifi does.)

However, as Seth Larson articulated during the PyCon 2022 [The future of trust stores in Python](https://youtu.be/1IiL31tUEVk?t=698) ([slides](https://speakerdeck.com/sethmlarson/the-future-of-trust-stores-in-python)) talk, package repositories don't make great TLS certificate distribution systems. There is a better way.

## The Solution

You could apply a bunch of workarounds, but why should you have to? Native applications on your system handle TLS certificates without fuss, so your other tools should be able to do this too.

**The real solution is for CLI tools and programming languages to integrate with the system truststore.**  (With the option to turn this off for debugging, or compatibility with legacy environments.)

This means:

- The system manages provisioning, updates, and revocation of certificates
- Intermediate fetching is supported
- No need for per-application certificate configuration
- No need to duplicate certificates outside of the trust store
- The system manages custom certificates (e.g. corporate TLS inspection certificates)

### on macOS

On macOS the truststore is the Keychain.

Language runtimes can integrate with the Keychain in the following ways:

- `Security.framework`: this is the low-level approach where you fetch TLS certificates from the Keychain, and your app code decides what to do with them.
- `URLSession`: this is the high-level approach where your language's HTTPS client becomes a wrapper for Apple's `URLSession` HTTPS client. This means that that you get any TLS certificate handling optimizations in `URLSession` (e.g. caching) 'for free'.

### on Windows

On Windows the truststore is the Windows Certificate Store.

Language runtimes can integrate with the Windows Certificate Store in the following ways:

- `schannel`
- `CryptoAPI`

### on Android

On Android the truststore is the System Trust Store.

Standard Android apps (which run on Android Runtime, ART) can integrate with the System Trust Store in the following ways:

- Java cryptography or TLS APIs (`KeyStore`, `SSLContext`, `TrustManager` and so on)
- Java HTTPS clients (e.g. `HttpClient`)

### on Linux

Linux distributions don't have a truststore API like the macOS Keychain. (The closest offerings are perhaps GNOME Keyring or KDE Wallet, but these are *keystores* for holding passwords, rather than *truststores* for holding root certificates.) Instead, certificates are stored directly on the filesystem, following the Filesystem Hierarchy Standard. An application's TLS library (like OpenSSL) then reads the certificates from this location.

The typical approach that system truststore integration wrappers take is to let OpenSSL look up the certificates on Linux (or at least, use OpenSSL conventions to determine the certificate search path), but step in to customize TLS certificate handling on other systems.
