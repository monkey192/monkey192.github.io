---
title: Git cmd cheatsheet
date: 2022-01-09 13:34:42
tags: 
    - github
    - ssh
category:
    - github
---
### ssh cmd cheatsheet
- Delete all cached
```sh
ssh-add -D
```
- Add a key
```bash
ssh-add ~/.ssh/<private key name>
```
- Test ssh connection
```sh
$ ssh -T git@github.com
# Attempts to ssh to GitHub
```
- Create ssh-key
```sh
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```
  
### ssh config file `~/.ssh/config`
```
Host github.com.main # <random for yourself>
  HostName github.com
  User git  # optional
  Port 22   # optional
  IdentityFile ~/.ssh/id_rsa_main  # Your key path
```
