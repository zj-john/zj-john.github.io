---
title: vagrant up报错：unable to mount VirtualBox shared folders
categories:
  - tips
tags:
  - vagrant
  - error
toc: false
date: 2018-05-03 14:16:42
---

vagrant up报以下错误：
```
Vagrant was unable to mount VirtualBox shared folders. This is usually
because the filesystem "vboxsf" is not available. This filesystem is
made available via the VirtualBox Guest Additions and kernel module.
Please verify that these guest additions are properly installed in the
guest. This is not a bug in Vagrant and is usually caused by a faulty
Vagrant box. For context, the command attempted was:

mount -t vboxsf -o uid=1000,gid=1000 vagrant /vagrant

The error output from the command was:

/sbin/mount.vboxsf: mounting failed with the error: No such device
```

<!-- more -->

参考[文档](https://github.com/scotch-io/scotch-box/issues/296)

看到有2个方法：

1.
```
vagrant plugin install vagrant-vbguest
vagrant vbguest
```


2.
```
vagrant plugin install vagrant-winnfsd
vagrant plugin install vagrant-vbguest
```
