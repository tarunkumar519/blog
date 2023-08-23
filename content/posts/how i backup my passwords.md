---
title: "How i backup my Passwords"
date: 2023-07-23T21:25:47+05:30
draft: false
tags: ["passwords"]
categories: ["selfhosting"]
cover:
    image: /how-i-backup-my-passwords.png
    alt: 'How i backup my Passwords'
    caption: 'Bitwarden is an open-source password manager'
---

## Why use a password manager?

Lot of people use the same password for different websites. They mostly use the same emails too. This is because we as humans cannot remember complex passwords. But using same passwords is not recommended. 

There are lot of data leaks happening all around the world due to hackers. Imagine if some company gets hacked. Their database contains your emails, passwords (hopefully encrypted) and lots of other data. If you use the same password for other websites- hackers can write some simple scripts or programs to automatically test your email-password combo on lot of websites. If you use the same password, you’re hacked.

This is the most common way accounts get hacked around the world. Millions of accounts get hacked every year.

But we can’t remember complex and different passwords for each and every website right?

We don’t need to.

There are password managers that store all our passwords and lock them with one simple master password. 

The encryption is so strong in modern password managers that if you forget your master password, your account is locked forever. Only you have access to your passwords.

## Why i self-host password manager

Even the most famous password managers are susceptible to attacks. Your passwords should be safe and encrypted but what if someone makes a mistake so your password vaults are encrypted with less iterations?

As GPUs are getting faster and faster, a company needs to increase the iterations. What if they don’t?

This is what happened in the case of the password manager Last Pass. There are some very serious flaws in their design which made it the [most attacked password manager in 2023](https://thehackernews.com/2023/02/lastpass-reveals-second-attack.html).

This is why people always want to look at the code, so even individual security companies can look and detect flaws in the software.

Bitwarden does this in the best way. They are open-source and encourage people to host their own servers if they don’t trust bitwarden. Security audits are conducted on the company every year to make sure they are following all the necessary policies.

And Bitwarden follows Zero-trust-policy.

Self-hosting on our own server makes it less prone to attacks, since hackers aim to attack a company’s database and not our own.

But now it’s our turn to manage our data, which can be a big task in case of passwords.
Lots of my friends and family use my self-hosted password manager. And it is my responsibility to keep their data safe and backed up always.

## How do i backup?

I run [Vaultwarden](https://github.com/dani-garcia/vaultwarden), an open source version of Bitwarden written in Rust.
This makes it fast and less resource intensive.

Vaultwarden is run in a docker container. Before each backup the container needs to be stopped. Not doing this may lead to data corruption.
I use cron jobs to run backups every night around 3AM. If the server is not online at that moment, backup is done when the server is back online.

By default, all the files of Bitwarden are stored inside `/portainer/Files/AppData/Config/Bitwarden`

This folder is the one that needs to be backed up.

Lets write a simple bash script.

```
#!/bin/bash
docker stop vaultwarden
```
As we already know we need to stop vaultwarden container using docker.

```
rclone copy /portainer/Files/AppData/Config/Bitwarden/ gd:vaultwarden —exclude=“/icon_cache**”
```
We use rclone to copy our files to remote drives such as Google Drive , OneDrive and Dropbox in our case.

`gd:` in this case means GoogleDrive and `vaultwarden` is the remote folder on the drive which we configured using rclone setup. [Rclone configuration instructions here](https://rclone.org/remote_setup/).
We are telling rclone to exclude `icon_cache` folder because these are just icons for different websites for which passwords are stored. Bitwarden will download these automatically once configured.

Let’s create a text file that contains date and time of backup. This should be updated everytime the backup is done. So we can check everything is working. We use the date command in Linux for this.

```
date > /portainer/Files/AppData/Config/Bitwarden/stamp.txt
```
Our final bash script looks like this

```
#!/bin/bash
  docker stop vaultwarden
  date > /portainer/Files/AppData/Config/Bitwarden/stamp.txt
  rclone copy /portainer/Files/AppData/Config/Bitwarden/ gd:vaultwarden —exclude=“/icon_cache**”
  docker start vaultwarden
```

## Setting up cron

This is a simple cron job set with `crontab -e` and runs every night at 3AM for example.

```
0 3 * * * /usr/local/bin/cronbvw >/dev/null 2>&1
```
Our bash script is put inside `/usr/local/bin` folder, it is named as `cronbvw` and is given necessary permissions using `chmod`.

Let’s also make it executable using
```
chmod +x /usr/local/bin/cronbvw
```

Since we don’t want any output from the bash, the `>/dev/null 2>&1` sends the output to /dev/null and it’s discarded automatically.

`2>&1` means discarding stardard error 2 to standard output 1 which then discards it as well.

Once setup, this is done automatically everyday and we can check our drives for files with last modified dates and `stamp.txt` for when backups happened.

## How to restore from backup

This part is simple. Wherever you want to run vaultwarden, just install it and run it. Vaultwarden will create the `Bitwarden` folder fresh. 

Just stop vaultwarden.
Copy and paste your original Bitwarden folder - overwrite it.
Start vaultwarden.

Everything should be back to normal. You unlock it using your master password and even all the configuration you did inside `config.json` is still there since it is also in the backed up files.
