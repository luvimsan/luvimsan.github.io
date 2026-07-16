+++
date = "2026-02-18T18:19:28+03:00"
title = "Getting started with NeoMutt"
description = "Setting up NeoMutt with proton and gmail"
tags = ['linux', 'tools', 'guides']
+++
NeoMutt is a terminal based email client, that can connect to multiple emails.
in this blog, we will discuss how to use it and getting it connected to proton 
and gmail

## Installing mw (mutt-wizard) 
check out lukesmith[^1] github for more documentation

```sh
git clone https://github.com/LukeSmithxyz/mutt-wizard
cd mutt-wizard
sudo make install
```

## Setting up proton

1. Install hydroxide

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

5. Set up the service
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

6. Start the service and monitor

```sh
systemctl --user enable --now hydroxide.service
systemctl --user status hydroxide.service 
```


7. Add the account to neomutt
```bash
mw -a username@proton.me \
-i 127.0.0.1  -I 1143 \
-s 127.0.0.1 -S 1025 \
-u username@proton.me -f
```

>for reference  
>-i -->     imap server  
>-I -->     imap port  
>-s -->     smtp server  
>-S -->     smtp port  
>-u -->     username  
>-f -->     no connection  

---
### Setting up gmail
It's much easier to setup gmail because it supports imap and smtp by default.
But for authentication we need to have a pass from google. as well as having 2fa
enabled.

1. turn on 2fa
2. generate pass from app password[^2]
3. add it with mw `mw -a email@gmail.com`

## References

[^1]: [Luke Smith mutt-wizard github](https://github.com/lukesmithxyz/mutt-wizard#blank)
[^2]: [App password Google](https://myaccount.google.com/apppasswords#blank)
