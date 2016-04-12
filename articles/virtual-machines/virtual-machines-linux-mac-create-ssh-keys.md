<properties
	pageTitle="Использование ключей SSH в Linux и Mac | Microsoft Azure"
	description="Создание и использование SSH-ключей в Linux и Mac для развертывания с помощью диспетчера ресурсов и классической модели развертывания в Azure."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="vlivech"
	manager="timlt"
	editor=""
	tags="" />

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="04/04/2016"
	ms.author="v-livech"/>

# Создание ключей SSH для виртуальных машин Linux в ОС Linux и Mac в Azure

Чтобы создать защищенные паролем открытый и закрытый ключи SSH, вам понадобится [Azure CLI](../xplat-cli-install.md) в режиме Resource Manager (`azure config mode arm`).

## Краткий перечень команд

В примерах команд ниже замените значения между &lt; и &gt; значениями, соответствующими вашей среде.

```bash
[chrisL@fedora ~]$ ssh-keygen -t rsa -b 2048 -C "<your_user@yourdomain.com>"

#Enter the name of the file that will be saved in the `~/.ssh/` directory.
azure_fedora_id_rsa

#Enter (twice) a [secure](https://www.xkcd.com/936/) password for the SSH key.

#Enter passphrase for github_id_rsa:
correct horse battery staple

#Add the newly created key to `ssh-agent` on Linux and Mac (also added to OSX Keychain).
[chrisL@fedora ~]$ eval "$(ssh-agent -s)"
[chrisL@fedora ~]$ ssh-add ~/.ssh/azure_fedora_id_rsa

#Copy the SSH public key to your Linux Server.
[chrisL@fedora ~]$ ssh-copy-id -i ~/.ssh/azure_fedora_id_rsa.pub <youruser@yourserver.com>

#Test the login using keys instead of a password.
[chrisL@fedora ~]$ ssh -i ~/.ssh/azure_fedora_id_rsa <youruser@yourserver.com>

Last login: Tue Dec 29 07:07:09 2015 from 66.215.21.201
[chrisL@fedora ~]$

```

## Введение

Использование открытого и закрытого ключей SSH — это самый безопасный **и** простой способ входа на серверы Linux. Кроме того, [шифрование с открытым ключом](https://en.wikipedia.org/wiki/Public-key_cryptography) также обеспечивает более безопасный вход в систему на виртуальную машину Linux или BSD в Azure, чем вход с помощью пароля, который является более простым решением. Открытый ключ можно предоставить любому пользователю, тогда как закрытый ключ принадлежит только вам (или локальной инфраструктуре безопасности).

В этой статье рассматривается создание файлов ключей в формате *SSH RSA*. Именно такие файлы рекомендуется использовать при развертывании с помощью Resource Manager, а также требуется использовать на [портале](https://portal.azure.com) при классическом развертывании и развертывании с помощью Resource Manager.


## Создание ключей SSH

В Azure необходимо использовать по крайней 2048-разрядные открытые и закрытые ключи в формате ssh-rsa. Чтобы создать такую пару, выполните команду `ssh-keygen`, которая задаст несколько вопросов, а затем запишет закрытый ключ и соответствующий открытый ключ. При создании виртуальной машины Azure осуществляется передача содержимого открытого ключа. Оно копируется в виртуальную машину Linux и используется при входе вместе с локальным безопасно хранимым закрытым ключом во время проверки подлинности пользователя.

### Использование `ssh-keygen`

Эта команда создает пару ключей SSH, используя 2048-разрядный RSA. К этой паре добавляются комментарии, по которым их будет легко найти.

```
chrisL@fedora$ ssh-keygen -t rsa -b 2048 -C "username@fedoraVMAzure"
```

##### Описание команды

`ssh-keygen` — программа, с помощью которой создаются ключи.

`-t rsa` — тип ключа, который нужно создать. Здесь — [формат RSA] (https://en.wikipedia.org/wiki/RSA_(cryptosystem)).

`-b 2048` — значение битов ключа.

`-C "username@fedoraVMAzure"` — комментарий для быстрой идентификации ключа. Комментарий добавляется в конец файла открытого ключа. Зачастую комментарий — это адрес электронной почты, но в этой статье мы разрешим использовать несколько ключей SSH, поэтому здесь будет использоваться универсальный комментарий.

#### Пошаговое руководство для `ssh-keygen`

```bash
chrisL@fedora$ ssh-keygen -t rsa -b 2048 -C "username@fedoraVMAzure"
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/steve/.ssh/id_rsa): azure_fedora_id_rsa
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in azure_fedora_id_rsa.
Your public key has been saved in azure_fedora_id_rsa.pub.
The key fingerprint is:
14:a3:cb:3e:79:ad:25:cc:65:e9:0c:07:e5:d1:a9:08 username@fedoraVMAzure
The key's randomart image is:
+--[ RSA 4096]----+
|        o o. .   |
|      E. = .o    |
|      ..o...     |
|     . o....     |
|      o S =      |
|     . + O       |
|      + = =      |
|       o +       |
|        .        |
+-----------------+

chrisL@fedora$ ls -al ~/.ssh
-rw------- 1 username staff  1675 Aug 25 18:04 azure_fedora_id_rsa
-rw-r--r-- 1 username staff   410 Aug 25 18:04 azure_fedora_id_rsa.pub
```

`Enter file in which to save the key (/Users/steve/.ssh/id_rsa): azure_fedora_id_rsa` — имя пары ключей для этой статьи. По умолчанию пара ключей называется **id\_rsa**, поэтому в некоторых средствах может быть предусмотрено имя **id\_rsa** для файла закрытого ключа. Поэтому стоит создать такой файл. (`~/.ssh/` — это расположение по умолчанию для всех пар ключей SSH и файла конфигурации SSH.)

`Enter passphrase (empty for no passphrase):` Настоятельно рекомендуем добавить пароль для своей пары ключей (`ssh-keygen` учитывает его в качестве парольной фразы). Если не защитить пару ключей паролем, любой пользователь, у которого есть копия файла закрытого ключа сможет использовать его, чтобы войти на любой из серверов, для которого предусмотрен соответствующий открытый ключ. Следовательно, добавив пароль, вы увеличите уровень защиты, на случай если доступ к файлу закрытого ключа получит другой пользователь. Это позволит выиграть время на изменение своих ключей проверки подлинности.

`chrisL@fedora$ ls -al ~/.ssh` — это новые пары ключей и их разрешения. `ssh-keygen` создает каталог `~/.ssh`, если его нет, а также задает правильный режим владения и файловый режим.

## Создание и настройка файла конфигурации SSH

Настройка и подготовка виртуальной машины Linux необязательны. Тем не менее рекомендуется создать и настроить файл `~/.ssh/config`, чтобы предотвратить случайное использование паролей для входа в виртуальные машины, автоматизировать использование разных пар ключей для разных виртуальных машин Azure, а также настроить другие программы, такие как **git** для нескольких серверов.

Ниже приведены шаги для выполнения стандартной конфигурации.

### Создание файла

```bash
chrisL@fedora$ touch ~/.ssh/config
```

### Изменение файла путем добавления новой конфигурации SSH

```bash
chrisL@fedora$ vim ~/.ssh/config

#Azure Keys
Host fedora22
  Hostname 102.160.203.241
  User username
  PubkeyAuthentication yes
  IdentityFile /Users/steve/.ssh/azure_fedora_id_rsa
# ./Azure Keys
# GitHub keys
Host github.com
  Hostname github.com
  User git
  PubKeyAuthentication yes
  IdentityFile /Users/steve/.ssh/github_id_rsa
Host github.private
  Hostname github.com
  User git
  PubKeyAuthentication yes
  IdentityFile /Users/steve/.ssh/private_repo_github_id_rsa
# ./Github Keys
# Default Settings
Host *
  PubkeyAuthentication=no
  IdentitiesOnly=yes
  ServerAliveInterval=60
  ServerAliveCountMax=30
  ControlMaster auto
  ControlPath /Users/steve/.ssh/Connections/ssh-%r@%h:%p
  ControlPersist 4h
  StrictHostKeyChecking=no
  IdentityFile /Users/steve/.ssh/id_rsa
  UseRoaming=no
```

Эта конфигурация SSH предусматривает отдельные разделы для служб, чтобы каждая из них могла использовать выделенную ей пару ключей. Параметры по умолчанию используются для всех узлов, на которые вы вошли и которые не принадлежат ни к одной из вышеуказанных групп. Такая конфигурация SSH также позволяет использовать два отдельных имени для входа в [GitHub](https://github.com): одно для общедоступных ресурсов, а другое для частных репозиториев, которые могут быть общими в вашей среде.


##### Описание файла конфигурации

`Host` — имя узла, вызываемого в терминале. `ssh fedora22` сообщает `SSH`, что нужно использовать значения блока параметров с меткой `Host fedora22`. ПРИМЕЧАНИЕ. Можно использовать любую логическую метку, в которой не содержится фактическое имя узла сервера.

`Hostname 102.160.203.241` — IP-адрес или DNS-имя сервера, на который будет выполняться вход. Он используется для маршрутизации на сервер и может быть внешним IP-адресом, сопоставимым с внутренним IP-адресом.

`User git` — удаленная учетная запись пользователя, которую нужно использовать при входе на виртуальную машину Azure.

`PubKeyAuthentication yes` — сообщает SSH, что для входа необходимо использовать ключ SSH.

`IdentityFile /Users/steve/.ssh/azure_fedora_id_rsa` — сообщает SSH, какую пару ключей указывать на сервере для проверки подлинности при входе.


## Вход в виртуальную машину Linux с использованием SSH без пароля

Теперь, когда есть пара ключей SSH и настроен файл конфигурации SSH, вход в виртуальную машину Linux выполняется быстро и безопасно. При первом входе на сервер с использованием ключа SSH команда отправляет запрос на парольную фразу для этого файла ключа.

`chrisL@fedora$ ssh fedora22`

##### Описание команды

После выполнения `chrisL@fedora$ ssh fedora22` SSH сначала находит и загружает все параметры из блока `Host fedora22`, а затем загружает все остальные параметры из последнего блока `Host *`.

## Дальнейшие действия

Теперь вы можете использовать файлы ключей, чтобы [создать защищенную виртуальную машину Linux в Azure с помощью шаблона](virtual-machines-linux-create-ssh-secured-vm-from-template.md).

<!---HONumber=AcomDC_0406_2016-->