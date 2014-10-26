<properties linkid="services-linux-user-names" urlDisplayName="User Names in Linux" pageTitle="Selecting User Names for Linux on Azure" metaKeywords="" description="Learn how to select user names for a Linux virtual machine in Azure." metaCanonical="" services="virtual-machines" documentationCenter="" title="Selecting User Names for Linux on Azure" authors="szark" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="szark"></tags>

# Выбор имен пользователей для Linux в Azure

При создании экземпляра виртуальной машины в Azure у вас есть возможность выбрать имя пользователя для подготовки (имя пользователя по умолчанию — *azureuser*). В большинстве случаев этот новый пользователь не будет существовать в базовом образе, а будет создан в процессе подготовки. В некоторых случаях, если пользователь не существует в базовом образе виртуальной машины, агент Linux Azure просто настроит для такого пользователя пароль (и/или ключ SSH) на основе данных подготовки конфигурации, переданных при создании виртуальной машины.

**Тем не менее**, Linux определяет набор имен пользователей, которые нельзя использовать при создании новых пользователей. Процесс подготовки завершится **ошибкой**, если при подготовке виртуальной машины Linux вы попробуете использовать существующего системного пользователя, который определен как пользователь с идентификатором UID 0-99. Типичный пример — это `root` пользователь, имеющий UID 0.

-   См. также: [Стандартная база Linux: диапазоны идентификаторов пользователей][Стандартная база Linux: диапазоны идентификаторов пользователей]

Следующий список содержит имена пользователей, которых вам следует избегать при подготовке виртуальной машины Linux. Из соображений безопасности рекомендуем вам **не использовать эти имена пользователей** при подготовке виртуальной машины Linux, потому что из-за этого процесс подготовки может завершиться ошибкой.

## openSUSE

-   abrt
-   adm
-   audio
-   bin
-   bin
-   cdrom
-   cgred
-   daemon
-   dbus
-   dialout
-   dip
-   disk
-   floppy
-   ftp
-   games
-   gopher
-   haldaemon
-   halt
-   kmem
-   lock
-   lp
-   mail
-   man
-   mem
-   nfsnobody
-   nobody
-   ntp
-   operator
-   oprofile
-   postdrop
-   postfix
-   qpidd
-   root
-   rpc
-   rpcuser
-   saslauth
-   shutdown
-   slocate
-   sshd
-   stapdev
-   stapusr
-   sync
-   sys
-   tape
-   test
-   tcpdump
-   tty
-   users
-   utempter
-   utmp
-   uucp
-   vcsa
-   video
-   wheel

## SLES

-   audio
-   bin
-   cdrom
-   console
-   daemon
-   dialout
-   disk
-   floppy
-   ftp
-   ftp
-   games
-   haldaemon
-   kmem
-   lp
-   lp
-   mail
-   maildrop
-   man
-   messagebus
-   modem
-   news
-   news
-   nobody
-   nogroup
-   polkituser
-   postfix
-   public
-   root
-   shadow
-   sshd
-   sys
-   test
-   trusted
-   tty
-   users
-   utmp
-   uucp
-   uuidd
-   video
-   wheel
-   www
-   wwwrun
-   xok

## CentOS

-   abrt
-   adm
-   audio
-   bin
-   cdrom
-   cgred
-   daemon
-   dbus
-   dialout
-   dip
-   disk
-   floppy
-   ftp
-   ftp
-   games
-   gopher
-   haldaemon
-   halt
-   kmem
-   lock
-   lp
-   mail
-   man
-   mem
-   nfsnobody
-   nobody
-   ntp
-   operator
-   oprofile
-   postdrop
-   postfix
-   qpidd
-   root
-   rpc
-   rpcuser
-   saslauth
-   shutdown
-   slocate
-   sshd
-   stapdev
-   stapusr
-   sync
-   sys
-   tape
-   test
-   tcpdump
-   tty
-   users
-   utempter
-   utmp
-   uucp
-   vcsa
-   video
-   wheel

## Ubuntu

-   adm
-   admin
-   audio
-   backup
-   bin
-   cdrom
-   crontab
-   daemon
-   dialout
-   dip
-   disk
-   fax
-   floppy
-   fuse
-   games
-   gnats
-   irc
-   kmem
-   landscape
-   libuuid
-   list
-   lp
-   mail
-   man
-   messagebus
-   mlocate
-   netdev
-   news
-   nobody
-   nogroup
-   operator
-   plugdev
-   proxy
-   root
-   sasl
-   shadow
-   src
-   ssh
-   sshd
-   staff
-   sudo
-   sync
-   sys
-   syslog
-   tape
-   tty
-   users
-   utmp
-   uucp
-   video
-   voice
-   whoopsie
-   www-data

  [Стандартная база Linux: диапазоны идентификаторов пользователей]: http://refspecs.linuxfoundation.org/LSB_4.1.0/LSB-Core-generic/LSB-Core-generic/uidrange.html
