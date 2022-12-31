+++
title = "PGP Key"
date = 2022-12-30
+++

Use strong cryptography! This post is publicizing my primary PGP key (`0x9C7AE751869E6A22`).

<!-- more -->

```
λ gpg --keyid-format 0xlong --fingerprint borlaag@proton.me
pub   ed25519/0x9C7AE751869E6A22 2022-12-30 [SC]
      Key fingerprint = 15F3 35C8 6BC7 3B54 FD3E  E5DE 9C7A E751 869E 6A22
uid                   [ultimate] Børlaag <borlaag@proton.me>
sub   cv25519/0xE702275C26B1BBD4 2022-12-30 [E]
```

## Public Key

```
-----BEGIN PGP PUBLIC KEY BLOCK-----

mDMEY65cLxYJKwYBBAHaRw8BAQdAl8+avAL1d4eYuyyIXETobTrQNWDVyAlH/wEq
ikGcUMW0HELDuHJsYWFnIDxib3JsYWFnQHByb3Rvbi5tZT6IkAQTFggAOBYhBBXz
NchrxztU/T7l3px651GGnmoiBQJjrlwvAhsDBQsJCAcCBhUKCQgLAgQWAgMBAh4B
AheAAAoJEJx651GGnmoi5a0BAMLMX8cucgZWRlTmW337aPk84PW+4t4+2bmvCGEQ
+NaaAP4uA3/z4ejpBNf+6UUDPFvqJsRGAONEjokrGBr9QBr6Abg4BGOuXC8SCisG
AQQBl1UBBQEBB0CNBdoYoYioF1a2URoYJf5Bluk+gLQoVs7mExpHMhMtEQMBCAeI
eAQYFggAIBYhBBXzNchrxztU/T7l3px651GGnmoiBQJjrlwvAhsMAAoJEJx651GG
nmoioM8BALvRxiLtxp2rrXnAYNPYtnj/qOJHU5qQMeim4B+JiKeYAP9oEY3t0r58
q8rFJLQ+vt/UDGOTYpECoXOfR1br5EsbBg==
=MiDM
-----END PGP PUBLIC KEY BLOCK-----
```

## Plaintext

[https://borlaag.com/pgp.txt](https://borlaag.com/pgp.txt). If you're working in a terminal,
feel free to `curl` my key directly.

```
curl -L https://borlaag.com/pgp.txt
```