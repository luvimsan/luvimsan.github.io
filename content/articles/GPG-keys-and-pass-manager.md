+++
date = "2026-02-14T18:41:24+03:00"
title = "GPG keys and pass manager"
tags = ['linux']
+++
## Backup folders

1. back up ~/.gnupg
1. back up ~/.password-store

## Tips for smooth transfer

- fix permisson --> .gnupg (700)
- remove `*.lock`
```sh
rm -f ~/.gnupg/*.lock
rm -f ~/.gnupg/public-keys.d/*.lock 2>/dev/null
rm -f ~/.gnupg/private-keys.d/*.lock 2>/dev/null
```

- use terminal mode if ssh and can`t view gui
    - `sudo update-alternatives --config pinentry`
    - choose cursor (probably 1)


## Exporting gpg key pairs

>for binary
- public:   `gpg --export > pub.gpg`
- private:  `gpg --export-secret-keys  > private.pgp`

>for ascii use `--armor` flag
- public:   `gpg --armor --export > pub.asc`
- private:  `gpg --armor --export-secret-keys > private.asc`


## GPG flags

list pub keys:          `gpg --list-keys`
list private keys:      `gpg --list-secret-keys`

delete pub keys:        `gpg --delete-key`
delete private keys:    `gpg --delete-secret-key`

refresh keys:           `gpg --refresh-keys`

---
## SSH keys

- create the ed25519: `ssh-keygen -t ed25519 -C "email"`
- add the pub key in github
