<properties 
	pageTitle="Выбор имен пользователей для Linux | Microsoft Azure" 
	description="Узнайте, как выбирать имена пользователей для виртуальной машины Linux в Azure." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="szarkos" 
	manager="timlt" 
	editor=""
	tags="azure-service-management,azure-resource-manager" />

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/29/2015" 
	ms.author="szark"/>



#Выбор имен пользователей для Linux в Azure#

При подготовке виртуальной машины Linux в Azure необходимо указать имя непривилегированного пользователя, которое позже можно будет использовать для входа в виртуальную машину. Можно выбрать имя нового пользователя или принять имя по умолчанию azureuser при подготовке с помощью портала управления.

В большинстве случаев этот новый пользователь не существует в базовом образе и создается в процессе подготовки. Если пользователь существует в базовом образе виртуальной машины, агент Linux Azure просто настраивает для него пароль (и/или ключ SSH) на основе данных, указанных при создании виртуальной машины.

**Тем не менее** Linux определяет набор имен пользователей, которые нельзя использовать. Процесс подготовки завершится **ошибкой**, если при подготовке виртуальной машины Linux вы попробуете использовать данные существующего системного пользователя, который определен как пользователь с идентификатором в диапазоне 0—99. Типичный пример — пользователь `root` с идентификатором 0.

 - См. также: [База стандартов Linux: диапазоны идентификаторов пользователей](http://refspecs.linuxfoundation.org/LSB_4.1.0/LSB-Core-generic/LSB-Core-generic/uidrange.html)

Далее представлены имена пользователей, которые не следует использовать при подготовке виртуальной машины Linux. Мы советуем вам **не использовать эти имена пользователей**, потому что из-за этого процесс подготовки виртуальной машины может завершиться ошибкой.


## openSUSE
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


## SLES
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

 
## CentOS
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


## Ubuntu
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

 

<!---HONumber=Oct15_HO3-->