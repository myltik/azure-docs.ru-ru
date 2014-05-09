<properties linkid="services-linux-user-names" urlDisplayName="Имена пользователей в Linux" pageTitle="Выбор имен пользователей для Linux в Azure" metaKeywords="" description="Узнайте, как выбрать имена пользователей для виртуальной машины Linux в Azure." metaCanonical="" services="virtual-machines" documentationCenter="" title="Выбор имен пользователей для Linux в Azure" authors="" solutions="" manager="" editor="" />





#Выбор имен пользователей для Linux в Azure#

При создании экземпляра виртуальной машины для образов Linux в Azure имеется возможность выбрать имя пользователя для подготовки.

Linux определяет набор имен пользователей, которые нельзя использовать. Крайне важно **избегать использования этих имен**. Если вы используете одно из этих имен пользователя, то не сможете подготовить образ.

Кроме того, API управления службами возвратит ошибку "Не удалось создать пользователя *xxxxxx*". Такая ситуация также будет иметь место, если вы используете имя пользователя, которое уже присутствует в образе из-за предыдущей операции записи, которая не выполнила отзыв уже созданного в образе имени пользователя. 

Ниже перечислены имена пользователей, которые нельзя использовать. 



OpenSUSE
-------------------
- abrt
- adm
- audio
- bin
- bin
- cdrom
- cgred
- daemon
- dbus
- dialout
- dip
- disk
- floppy
- ftp
- games
- gopher
- haldaemon
- halt
- kmem
- lock
- lp
- mail
- man
- mem
- nfsnobody
- nobody
- ntp
- operator
- oprofile
- postdrop
- postfix
- qpidd
- root
- rpc
- rpcuser
- saslauth
- shutdown
- slocate
- sshd
- stapdev
- stapusr
- sync
- sys
- tape
- test
- tcpdump
- tty
- users
- utempter
- utmp
- uucp
- vcsa
- video
- wheel

SLES
------------------
- audio
- bin
- cdrom
- console
- daemon
- dialout
- disk
- floppy
- ftp
- ftp
- games
- haldaemon
- kmem
- lp
- lp
- mail
- maildrop
- man
- messagebus
- modem
- news
- news
- nobody
- nogroup
- polkituser
- postfix
- public
- root
- shadow
- sshd
- sys
- test
- trusted
- tty
- users
- utmp
- uucp
- uuidd
- video
- wheel
- www
- wwwrun
- xok

 
CentOS
-------------------
- abrt
- adm
- audio
- bin
- cdrom
- cgred
- daemon
- dbus
- dialout
- dip
- disk
- floppy
- ftp
- ftp
- games
- gopher
- haldaemon
- halt
- kmem
- lock
- lp
- mail
- man
- mem
- nfsnobody
- nobody
- ntp
- operator
- oprofile
- postdrop
- postfix
- qpidd
- root
- rpc
- rpcuser
- saslauth
- shutdown
- slocate
- sshd
- stapdev
- stapusr
- sync
- sys
- tape
- test
- tcpdump
- tty
- users
- utempter
- utmp
- uucp
- vcsa
- video
- wheel

UBUNTU
-------------------
- adm
- admin
- audio
- backup
- bin
- cdrom
- crontab
- daemon
- dialout
- dip
- disk
- fax
- floppy
- fuse
- games
- gnats
- irc
- kmem
- landscape
- libuuid
- list
- lp
- mail
- man
- messagebus
- mlocate
- netdev
- news
- nobody
- nogroup
- operator
- plugdev
- proxy
- root
- sasl
- shadow
- src
- ssh
- sshd
- staff
- sudo
- sync
- sys
- syslog
- tape
- tty
- users
- utmp
- uucp
- video
- voice
- whoopsie
- www-data

