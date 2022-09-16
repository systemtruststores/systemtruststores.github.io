# Java

This page covers Java's integration with native certificate stores.

It also applies to any other language that can run on the JVM (e.g. Scala, Groovy, Clojure, Ruby).

---

Java has `keystore`s and `truststore`s. Of the two, we are interested in `truststore`s.

The default `truststore` strategy queries a limited set of certificates that ship with the JVM. You must specify an alternate strategy to make Java use your OS native trust store. This is done by running Java with the `-Djavax.net.ssl.trustStoreType` and `-Djavax.net.ssl.trustStore` properties, set to the appropriate values.

These Java properties can be made persistent by setting an environment variable in your shell profile:

```bash
export JAVA_TOOL_OPTS="-Djavax.net.ssl.trustStoreType=<foo> -Djavax.net.ssl.trustStore=<bar>"
```

They can alternatively be made persistent by setting them in a Maven profile, which you can then conditionally activate.

## Windows

The Java truststore strategies which access the Windows Certificate Store are:

### WINDOWS-ROOT

Run Java with these properties:

- `javax.net.ssl.trustStoreType=WINDOWS-ROOT`
- `javax.net.ssl.trustStore=NUL`

### WINDOWS-MY

Run Java with these properties:

- `javax.net.ssl.trustStoreType=WINDOWS-MY`
- `javax.net.ssl.trustStore=NUL`

### Update

The existing truststore strategies only access the **current user's** certificate store - not the **local computer** certificate store (where most TLS root certificates live). Therefore, there have been some updates...

In JDK 19 Early Access Build 23 (<https://jdk.java.net/19/release-notes#JDK-6782021>) Windows KeyStore support in the SunMSCAPI provider was expanded to include access to the local machine location. This was in response to <https://bugs.openjdk.org/browse/JDK-6782021>.

The new keystore types are:

- `Windows-MY-LOCALMACHINE`
- `Windows-ROOT-LOCALMACHINE`

The following keystore types were also added, allowing developers to make it clear that they map to the current user:

- `Windows-MY-CURRENTUSER` (same as `Windows-MY`)
- `Windows-ROOT-CURRENTUSER` (same as `Windows-ROOT`)

Note: this information might change before the final release of Java 19.

## macOS

TODO add instructions
