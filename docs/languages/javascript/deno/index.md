# Deno

[Deno](https://deno.land) is "a simple, modern and secure runtime for JavaScript and TypeScript that uses V8 and is built in Rust".

Deno supports the system truststore on:

- Windows
- macOS

You activate this by setting the `DENO_TLS_CA_STORE` environment variable to `system`:

```bash
export DENO_TLS_CA_STORE=system

deno run ...
```

Deno gained support for this in v1.13.0. (Pull Request: [denoland/deno#11491](https://github.com/denoland/deno/pull/11491))
