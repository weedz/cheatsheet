# OpenSSL

## Calculate checksum

Sha512 base64 (for example to use with pnpm `integrity` field):

```console
openssl dgst -binary -sha512 [FILE] | openssl base64
```

## rand

- `openssl rand`

Generate 32 bytes of random data and output as base64:

```console
openssl rand -base64 32
```
