# .NET

This page covers the .NET framework's integration with native certificate stores.

It applies to any language that runs on .NET (e.g. C#, F#).

There are two main runtimes for .NET:

- The CLR (https://github.com/dotnet/runtime) 
- [Mono](https://www.mono-project.com)

Since at least .NET 5, the official CLR has been open source and available for all major operating systems. Therefore these instructions will focus on how the official CLR works.

## Windows

.NET applications that use the [System.Net](https://docs.microsoft.com/en-us/dotnet/api/system.net) APIs (for example, [System.Net.Http.HttpClient](https://docs.microsoft.com/en-us/dotnet/api/system.net.http.httpclient) for HTTPS connections, and [System.Net.Security.SslStream](https://docs.microsoft.com/en-us/dotnet/api/system.net.security.sslstream) for other TLS connections) will retrieve TLS certificates from the Windows Certificate Store.

This has been the default behavior in all versions of the Windows CLR. You do not need to do anything extra to use it.

## macOS

.NET applications that use the [System.Net](https://docs.microsoft.com/en-us/dotnet/api/system.net) APIs will retrieve TLS certificates from the macOS Keychain.

This has been the default behavior of the macOS CLR since version 2.0.0, when it was migrated from OpenSSL to the Apple cryptography APIs (https://github.com/dotnet/runtime/issues/17597). You do not need to do anything extra to use it.
