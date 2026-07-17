+++
title = "How to use GPG"
date = "2026-07-14T16:46:55+03:00"
description = "Guide of how to use GnuPG"
tags = ["guides","linux","tools"]
+++

GPG stands for **GNU Privacy Guard**. It’s a free, open-source tool that helps
you encrypt and sign your data. The focus of this blog won't be on the history of
gpg - you can read more here[^1] - however we will delve into how to use gpg
- from creating a gpg key
- to choosing which kind of key pair
- ending with some useful flags to keep in mind

### Creating a GPG key
first things first you'll need to make sure you have GPG installed on your system
you can check with `gpg --version`. If not. Check out your package manager or
[GnuPG official website](https://www.gnupg.org/download/)

Create a gpg key pair
```sh
gpg --full-gen-key
```

### Choosing which kind of GPG key

- You will be prompted with different types of keys. we will choose a key that 
supports both signing and encrypting
```sh
Please select what kind of key you want:
   (1) RSA and RSA
   (2) DSA and Elgamal
   (3) DSA (sign only)
   (4) RSA (sign only)
   (9) ECC (sign and encrypt) *default*
  (10) ECC (sign only)
  (14) Existing key from card
```

- My recommendation will be **(9)** same as the default

```sh
Please select which elliptic curve you want:
   (1) Curve 25519 *default*
   (4) NIST P-384
   (6) Brainpool P-256
```
- I will choose the default as well. The famous algorithm Curve 25519
- then you will be asked about expiration, name, email address, and finally the 
master password

Let's untangle what happened. We have made a pair of public/private keys for 
signing and encrypting any kind of data 

to view them 
>for binary
- public:   `gpg --export > pub.gpg`
- private:  `gpg --export-secret-keys  > private.pgp`

>for ASCII use `--armor` flag
- public:   `gpg --armor --export > pub.asc`
- private:  `gpg --armor --export-secret-keys > private.asc`

### GPG flags
those are some useful flags to keep in your pocket
- list pub keys:          `gpg --list-keys`
- list private keys:      `gpg --list-secret-keys`
- delete pub keys:        `gpg --delete-key`
- delete private keys:    `gpg --delete-secret-key`
- refresh keys:           `gpg --refresh-keys`

### References

[^1]: [What Is GPG? The Complete Guide to Open-Source Encryption](https://terrazone.io/what-is-gpg/)
