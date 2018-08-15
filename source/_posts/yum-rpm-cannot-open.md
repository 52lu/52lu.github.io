---
title: yum error cannot open Packages database in ...rpm
date: 2018-08-01 17:05:26
tags:
 - linux
categories:
 - yum
---


报错：yum error: cannot open Packages database in /var/lib/rpm

<!--more-->
1、前提条件：安装软件包的时候，被我手动终止了
```angular2html
# yum clean all
rpmdb: Thread/process 4541/140619363587840 failed: Thread died in Berkeley DB library
error: db3 error(-30974) from dbenv->failchk: DB_RUNRECOVERY: Fatal error, run database recovery
error: cannot open Packages index using db3 -  (-30974)
error: cannot open Packages database in /var/lib/rpm
CRITICAL:yum.main:
Error: rpmdb open failed

# yum makecache
rpmdb: Thread/process 4541/140619363587840 failed: Thread died in Berkeley DB library
error: db3 error(-30974) from dbenv->failchk: DB_RUNRECOVERY: Fatal error, run database recovery
error: cannot open Packages index using db3 -  (-30974)
error: cannot open Packages database in /var/lib/rpm
CRITICAL:yum.main:
```

2、根据提示可以知道rpm数据库被损坏，如是按照下面的方法进行重建

```angular2html
# cd /var/lib/rpm/
# ls
Basenames     __db.004     Name            Pubkeys         Triggername
Conflictname  Dirnames     Obsoletename    Requirename
__db.001      Filedigests  Packages        Requireversion
__db.002      Group        Providename     Sha1header
__db.003      Installtid   Provideversion  Sigmd5

# rm __db.* -rf
# rpm --rebuilddb

# yum clean all
Loaded plugins: aliases, changelog, downloadonly, kabi, presto, product-id,
              : refresh-packagekit, security, subscription-manager, tmprepo,
              : verify, versionlock
Updating certificate-based repositories.
Unable to read consumer identity
Loading support for Red Hat kernel ABI
Cleaning repos: rhel-source
Cleaning up Everything
0 delta-package files removed, by presto
# yum update
Loaded plugins: aliases, changelog, downloadonly, kabi, presto, product-id,
              : refresh-packagekit, security, subscription-manager, tmprepo,
              : verify, versionlock
Updating certificate-based repositories.
Unable to read consumer identity
Loading support for Red Hat kernel ABI
rhel-source                                                | 4.0 kB     00:00 ...
rhel-source/primary_db                                     | 3.1 MB     00:00 ...
Setting up Update Process
No Packages marked for Update
```