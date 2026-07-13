+++
date = "2026-02-18T18:19:28+03:00"
title = "Neomutt guide to set up (proton, gmail)"
tags = ['linux', 'tools']
+++
Neomutt is a terminal based email client, that can connect to multiple emails.
in this blog, we will discuss how to use Neomutt with proton and gmail

## Installing mw (mutt-wizard) from lukesmith
check out lukesmith github for more documentation
[Luke Smith mutt-wizard github](https://github.com/lukesmithxyz/mutt-wizard#blank)

```sh
git clone https://github.com/LukeSmithxyz/mutt-wizard
cd mutt-wizard
sudo make install
```

## Setting up proton

### 1. Install hydroxide

```sh
go install github.com/emersion/hydroxide@latest # make sure you have go installed

```

2. `hydroxide auth username@proton.me` --> and add your proton passwd
3. save the bridge passwd to be used when setting up neomutt
4. set up the hydroxide server using as a service unit

```sh
mkdir -p ~/.config/systemd/user
nvim hydroxide.service
```

### 5. Set up the service
```service
[Unit]
Description=Hydroxide daemon
After=network.target

[Service]
ExecStart=%h/go/bin/hydroxide serve
Restart=on-failure
RestartSec=5
Environment=HOME=%h
StandardOutput=append:%h/.cache/hydroxide.log
StandardError=append:%h/.cache/hydroxide.log


[Install]
WantedBy=default.target
```

### 6. Start the service

`systemctl --user enable --now hydroxide.service`
`systemctl --user status hydroxide.service` see if it worked


### 7. Add the account to neomutt
```bash
mw -a username@proton.me \
-i 127.0.0.1  -I 1143 \
-s 127.0.0.1 -S 1025 \
-u username@proton.me -f
```

>for reference
-i --> imap server
-I --> imap port
-s --> smtp server
-S --> smtp port
-u username
-f no connection

> you are good to go

---
## Setting up gmail

1. turn on 2fa
2. generate pass from app password [here](https://myaccount.google.com/apppasswords#blank)
3. add it with mw `mw -a email@gmail.com`


---
## References

[Luke Smith mutt-wizard github](https://github.com/lukesmithxyz/mutt-wizard#blank)

[App password Google](https://myaccount.google.com/apppasswords#blank)

`man mw`
