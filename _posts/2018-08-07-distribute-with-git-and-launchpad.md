---
layout: post
title: Distribute your work with Git and Launchpad
permalink: /posts/git-launchpad
---


# Introduction

Let us consider you have developped a nice tool (or a nice library). You make it work on your laptop but, more than that, you put all your code on [GitHub](https://github.com/) of [GitLab](https://about.gitlab.com/), for instance. As you are smart, you made a useful `Makefile` so as to help others to use your work through the famous process:
```bash
git clone https://remote.site/mytool.git
cd mytool/
make
make install
```

However, it looks a bit handmade: of course, you don't need to be a computer science expert to enter these commands, but this is not as easy as installing an app on your smartphone. Moreover, for all the linux users which can use yor work, this is not handled by their package manager: just think about the uninstall or update process ...

Obviously, many ways exist to solve these drawbacks. Here we want to explain how you can distribute your work through debian packages and make it available for ubuntu user with a personal package archive (you know the `ppa:/...` you can add to your `/etc/apt/sources.list`).

# Step 1: Creating a debian package

## Makefile

First we have to ensure one thing: your `Makefile` must use an variable `DESTDIR` in the installation step. This variable will be used at the package creation. As an example, if you want to install your executable in `/usr/bin`, your `Makefile` can look like this:
```
DESTDIR=
default: mytool

mytool:
    ...

install:
    install bin/mytool $(DESTDIR)/usr/bin

clean:
    rm -f bin/mytool

```

## Skeleton
Here we consider, we are in your git local repository. It may look like this:
```
mytool/
    | bin/
    | build/
    | include/
    | src/
    | test/
    | Makefile
```

To create a debian package, we just have to add a `debian/` folder (with some specific files) in this layout. We can use `dh_make` to do it from scratch (in `mytool/ `):
```bash
dh_make -p mytool_1.0 --createorig
```  
Then the type of the package is requeted:
```
Type of package: (single, indep, library, python)
[s/i/l/p]?
```
If we want to distribute an executable, we just have to select `s`. After that, other details are asked:
```
Maintainer Name     : YOU
Email-Address       : YOU@SOMEWHERE
Date                : Tue, 07 Aug 2018 10:00:00 +0200
Package Name        : mytool
Version             : 1.0
License             : blank
Package Type        : single
Are the details correct? [Y/n/q]
```
These details may depend on your linux configuration. So you can initially accept them. Henceforth, your layout is the following:
```
| mytool_1.0.orig.tar.xz
| mytool/
    | bin/
    | build/
    | debian/
    | include/
    | src/
    | test/
    | Makefile
```

## Editing debian/* files
Now, we have to edit files in the `debian/` folder. First there are a lot of examples that we can remove
```shell
cd debian/
rm *.ex *.EX
```

Without too much details we have to ensure that the file `source/format` contains "**3.0 (native)**" (and not "3.0 (quilt)"). The *quilt* format use sucessive patches to modify your code from the original `mytool_1.0.orig.tar.xz` to your current version. Here we prefer build the package independently (without commiting the package changes etc.), in a kind of snapshot way.

I have also mentionned your personal details in the package initialization. The `control` file gatheres these information but also those about the package itself. It look like this:
```
Source: mytool
Section: unknown
Priority: optional
Maintainer: YOU <YOU@SOMEWHERE>
Build-Depends: debhelper (>= 10)
Standards-Version: 4.1.2
Homepage: <insert the upstream URL, if relevant>

Package: mytool
Architecture: any
Depends: ${shlibs:Depends}, ${misc:Depends}
Description: <insert up to 60 chars description>
 <insert long description, indented with spaces>
```

First you can edit the **Maintainer**, **Homepage** and **Description** sections. Then it could be nice to put your work into a common [Section](https://www.debian.org/doc/debian-policy/ch-archive.html#s-subsections) (**cli-mono** for instance).

We can also notice the **Build-Depends** and **Depends** sections. In the former, you can precise if you need other packages to build your tool. For instance if you use a library (included in a debian package `libxxx-dev`), you can add it:
```
...
Build-Depends: debhelper (>= 10), libxxx-dev
...
```
The version can be precised in brackets. In the same way, the **Depends** section gives the dependencies to make your tool run:
```
...
Depends: ${shlibs:Depends}, ${misc:Depends}, libxxx
...
```
In this latter section, we notice the variables `shlibs:Depends` and `misc:Depends`. Actually we can use them instead of adding manually  `libxxx` (they are defined in the `mytool.substvars` file).



## First package

Once you have modifed the previous files, you are able to build your first package. First you have to add and commit your change with `git`
```bash
git add debian/
git commit -m "Towards a debian package"
```
Then we use `gbp` to create the debian package. In `mytool/` folder:
```bash
gbp buildpackage --git-ignore-new
```
The `git-ignore-new` options means that we want to build the package even if some changes have not been commited.
Then you package is ok. But, where is it ? In the parent folder! Your final layout may look like this:
```
| mytool_1.0.orig.tar.xz
| mytool_1.0-1_amd64.buildinfo
| mytool_1.0-1_amd64.changes
| mytool_1.0-1_amd64.deb
| mytool_1.0-1.dsc
| mytool_1.0-1.tar.xz
| mytool_1.0.orig.tar.xz
mytool/
    | bin/
    | build/
    | debian/
    | include/
    | src/
    | test/
    | Makefile
```

# Step 2: Launchpad

Here we present how to use launchpad to perform automatic ubunutu builds from your code.

## A new remote git repository
Launchpad allows you to create a Personal Package Archive (PPA). You can then distribute softwares and updates directly to Ubuntu users.

To log in to launchpad, you have to create a [Ubuntu One](https://login.ubuntu.com/) account. Then you can attach your ssh key
(https://launchpad.net/~USER/+editsshkeys, where USER is your username).
To host your future debian package, you have to create a new PPA (from your launchpad board).

Launchpad will host a remote git repository (the same you have in GitHub or GitLab). So, the idea would be to push your local commit to all your remote repo.
To make it easier, launchpad gives the following advice:
edit `~/.gitconfig` and add these lines, where USER is your username:
```
[url "git+ssh://USER@git.launchpad.net/"]
        insteadof = lp:
```

Now you can add a new repo (called "launchpad", but you can change it as you like)
```bash
git remote add launchpad lp:~USER/+git/REPOSITORY-NAME
```
where REPOSITORY-NAME is naturally the name you want to give to the repo (generally the same as the others, so "mytool").

Finally you can push your commits:
```bash
git push origin master
git push launchpad master
```

## Recipe
 Why using another remote repo?? Actually, launchpad can create **recipes** above your repo. A recipe is just a debian packaging step, a bit like we did in the previous part. The power of launchpad is to automatize it and then host the builded packages.

 Let us consider you are in your launchpad account. Go to the "Code" section. Here you may see nothing, in this case, click on "View Git repositories" (or directly https://code.launchpad.net/~USER/+git). Then you will see your repo:
 ```
Name 	                Status 	        Last Modified 	Last Commit
lp:~USER/+git/mytool 	Development 	... 	        ... 
 ```

You can click on it and then "Create packaging recipe". You have to fill some basic information. A noticable thing is the **Default distribution series**: they are the Ubuntu versions for which the package will be built.

Moreover, the text of the recipe can be customized. In particular, you can change the debian versioning scheme. A common pattern is the following: `{debupstream}-0~{revno}` where `debupstream` is the classical version (i.e. `1.0` is our case) and `revno` is a counter incremented at each change.

Actually, all the parameters of the recipe can be changed afterwards.

Finally, on your recipe board (https://code.launchpad.net/~USER/+recipe/RECIPE_NAME) you can request new builds. Once triggered, launchpad notifies the remaining time and after the status of the packaging step (success or fail) with logs.

If it fails, you have to check these logs and investigate errors.

## Distribute

Once your builds succeed, you have available package in your PPA. For Ubuntu users, then can add your PPA to their `/etc/apt/sources.list`. Then they will be able to browse your package, install and update it easily.
```bash
sudo add-apt-repository ppa:USER/REPOSITORY-NAME
sudo apt update
sudo apt install mytool
```


# Step 3: Workflow with GitHub

Ok, we have a local git repo and two remote ones (GitHub+Launchpad for example). Builds are made on Launchpad (and are available through yout ppa), but your releases can also be hosted on GitHub.