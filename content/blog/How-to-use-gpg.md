+++
title = "How to Use GPG"
date = "2026-07-14T16:46:55+03:00"
tags = ["guides","linux","tools"]
+++
GPG stands for **GNU Privacy Guard** 
 
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

```c
int x = 53;
printf("hello world");
```

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
