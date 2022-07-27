---
title: "How to Mount & Sync Google Drive to Linux File System"
date: 2022-07-27T19:27:38+05:30
draft: false
summary: "
If you are from linux background you would know that google drive doesn't have an official gdrive sync client for linux.

There are some community based, like KDE plasma and gnome based plugins (KIO Gdrive) which works smoothly.

"
---
![Google Drive Gif](https://elitetools-partner.com/wp-content/uploads/2020/07/google-drive-gif.gif)
# Introduction
If you are from linux background you would know that google drive doesn't have an official gdrive sync client for linux.

There are some community based, like KDE plasma and gnome based plugins (KIO Gdrive) which works smoothly.

# Need for mount based google-drive sync
The above apps provide file explorer type sync and the completely use different protocol which read-writing files.
We can't perform direct file operations and we don't have a proper filesystem base link (or directory/folder path) most most of the apps will not be able to direct access those files.

# How to mount & sync google drive with filesystem on linux/debian/ubuntu/kubuntu

There is a cli mli method to achieve that, I was amazed when I found it.
The program is called ==google-drive-ocamlfuse==

## Setup

- Ensure proper ppa is loaded

```bash
 sudo add-apt-repository ppa:alessandro-strada/ppa
```

- Install application

```bash
sudo apt install google-drive-ocamlfuse
```

- Authorize application

```bash
google-drive-ocamlfuse
```

- Make a local directory/folder to mount with google-drive

```bash
mkdir google-drive-mounted
```

- Finally mount the google drive

```bash
google-drive-ocamlfuse google-drive-mounted
```

- In case you want to unmount

```bash
fusermount -u google-drive-mounted
```

- In case of some errors you can debug

```bash
google-drive-ocamlfuse -debug google-drive-mounted
```


📔 **My Experience**

>This is an amazing feature I found it today (27-07-2022) and I followed an article [Article Link](https://www.techrepublic.com/article/how-to-mount-your-google-drive-on-linux-with-google-drive-ocamlfuse/) that was authored in 2016, I was surprised to see this is still working and being maintained as google OAuth forced every old app to migrate to new version

⚠️ **Warning**
> Since this is not official google drive package, don't connect your personal google drive with it, use some other google account whose info on being compromised will not make you question your existence 😅, Stay SAFU


## References
1. App backend https://gd-ocaml-auth.appspot.com/
2. App github https://github.com/astrada/google-drive-ocamlfuse
3. First found here: https://www.techrepublic.com/article/how-to-mount-your-google-drive-on-linux-with-google-drive-ocamlfuse/
