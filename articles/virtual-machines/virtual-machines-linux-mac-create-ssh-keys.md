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
	ms.date="04/12/2016"
	ms.author="v-livech"/>

# Создание ключей SSH для виртуальных машин Linux в ОС Linux и Mac в Azure

Создать открытый и закрытый ключи SSH с защитой паролем можно в окне терминала на рабочей станции. С помощью созданных ключей SSH вы можете создавать новые виртуальные машины; кроме того, можно добавлять открытый ключ к существующим виртуальным машинам. Обе операции выполняются с использованием интерфейса командной строки Azure или шаблонов Azure.

## Краткий перечень команд

В примерах команд ниже замените значения между &lt; и &gt; значениями, соответствующими вашей среде.

```bash
[ahmet@fedora ~]$ ssh-keygen -t rsa -b 2048 -C "<your_user@yourdomain.com>"

#Enter the name of the file that will be saved in the `~/.ssh/` directory.
<azure_fedora_id_rsa>

#Enter passphrase for azure_fedora_id_rsa:
<correct horse battery staple>

#Add the newly created key to `ssh-agent` on Linux and Mac (also added to OSX Keychain).
[ahmet@fedora ~]$ eval "$(ssh-agent -s)"
[ahmet@fedora ~]$ ssh-add ~/.ssh/azure_fedora_id_rsa

#Copy the SSH public key to your Linux Server.
[ahmet@fedora ~]$ ssh-copy-id -i ~/.ssh/azure_fedora_id_rsa.pub <youruser@yourserver.com>

#Test the login using keys instead of a password.
[ahmet@fedora ~]$ ssh -o PreferredAuthentications=publickey -o PubkeyAuthentication=yes -i ~/.ssh/azure_fedora_id_rsa <youruser@yourserver.com>

Last login: Tue April 12 07:07:09 2016 from 66.215.22.201
[ahmet@fedora ~]$

```

## Введение

Использование открытого и закрытого ключей SSH — это самый простой способ войти на серверы Linux. Кроме того, [шифрование с открытым ключом](https://en.wikipedia.org/wiki/Public-key_cryptography) обеспечивает более надежную защиту при входе на виртуальную машину Linux или BSD в Azure, чем использование пароля, так как пароли можно взломать, используя грубые методы. Открытый ключ можно предоставить любому пользователю, тогда как закрытый ключ принадлежит только вам (или локальной инфраструктуре безопасности). Созданный закрытый ключ SSH будет защищен с помощью [безопасного пароля](https://www.xkcd.com/936/). Этот пароль используется только для доступа к закрытому ключу SSH, но **не** для входа в учетную запись пользователя. Любой пользователь, имеющий в своем распоряжении закрытый ключ без пароля, может получить доступ к любому серверу, на котором установлен открытый ключ. Закрытый ключ не может использоваться без пароля.


В этой статье рассматривается создание файлов ключей в формате *SSH RSA*. Мы рекомендуем использовать именно их при развертывании с помощью Resource Manager; кроме того они являются обязательными при развертывания на [портале](https://portal.azure.com) (как при классическом развертывании, так и с помощью Resource Manager).


## Создание ключей SSH

В Azure необходимо использовать по крайней 2048-разрядные открытые и закрытые ключи в формате ssh-rsa. Чтобы создать такую пару, выполните команду `ssh-keygen`. Вам нужно будет ответить на несколько вопросов, после чего будут записаны закрытый ключ и соответствующий открытый ключ. При создании виртуальной машины Azure осуществляется передача содержимого открытого ключа. Оно копируется в виртуальную машину Linux и используется при входе вместе с локальным безопасно хранимым закрытым ключом во время проверки подлинности пользователя.

### Использование `ssh-keygen`

Эта команда создает пару ключей SSH, защищенных паролем, используя 2048-разрядный RSA. К этой паре можно добавить комментарий, чтобы ее потом можно было легко определить.

```
ahmet@fedora$ ssh-keygen -t rsa -b 2048 -C "ahmet@fedoraVMAzure"
```

##### Описание команды

`ssh-keygen` — программа, с помощью которой создаются ключи.

`-t rsa` — тип ключа, который нужно создать [RSA](https://en.wikipedia.org/wiki/RSA_(cryptosystem)).

`-b 2048` — количество битов в ключе.

`-C "ahmet@fedoraVMAzure"` — комментарий, который будет добавлен в конец файла открытого ключа для идентификации. Обычно в качестве комментария используется адрес электронной почты, но вы можете выбрать для своей инфраструктуры любой удобный метод идентификации.

#### `ssh-keygen`: пошаговое руководство

```bash
ahmet@fedora$ ssh-keygen -t rsa -b 2048 -C "ahmet@fedoraVMAzure"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/ahmet/.ssh/id_rsa): azure_fedora_id_rsa
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in azure_fedora_id_rsa.
Your public key has been saved in azure_fedora_id_rsa.pub.
The key fingerprint is:
14:a3:cb:3e:78:ad:25:cc:55:e9:0c:08:e5:d1:a9:08 ahmet@fedoraVMAzure
The key's randomart image is:
+--[ RSA 2048]----+
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

ahmet@fedora$ ls -al ~/.ssh
-rw------- 1 ahmet staff  1675 Aug 25 18:04 azure_fedora_id_rsa
-rw-r--r-- 1 ahmet staff   410 Aug 25 18:04 azure_fedora_id_rsa.pub
```

`Enter file in which to save the key (/home/ahmet/.ssh/id_rsa): azure_fedora_id_rsa` — имя пары ключей, которое мы используем в этой статье. По умолчанию пара ключей называется **id\_rsa**. Так как некоторые средства ожидаемо ищут закрытый ключ в файле с именем **id\_rsa**, есть смысл создать такой файл. (По умолчанию все пары ключей SSH и файл конфигурации SSH располагаются в каталоге `~/.ssh/`.)

`Enter passphrase (empty for no passphrase):` Настоятельно рекомендуем добавить пароль (в `ssh-keygen` он называется passphrase) для своей пары ключей. Если не защитить пару ключей паролем, любой пользователь, у которого есть копия файла закрытого ключа сможет использовать его, чтобы войти на любой из серверов, для которого предусмотрен соответствующий открытый ключ. Следовательно, добавив пароль, вы увеличите уровень защиты, на случай если доступ к файлу закрытого ключа получит другой пользователь. Это позволит выиграть время на изменение своих ключей проверки подлинности.

`ahmet@fedora$ ls -al ~/.ssh` — эта команда покажет новые пары ключей и соответствующие разрешения. `ssh-keygen` создает каталог `~/.ssh`, если его нет, и устанавливает для него надлежащий файловый режим и режим владения.

## Использование ssh-agent для хранения пароля закрытого ключа

Чтобы не вводить вручную пароль закрытого ключа при каждом входе с использованием SSH, вы можете с помощью команды `ssh-agent` создать кэш пароля от файла закрытого ключа. Это позволит вам быстрее выполнять защищенный вход на виртуальные машины Linux. Если вы используете операционную систему OS X, пароль закрытого ключа при вызове `ssh-agent` будет надежно сохранен в цепочке ключей.

Сначала убедитесь, что `ssh-agent` выполняется.

`[ahmet@fedora ~]$ eval "$(ssh-agent -s)"`

Теперь добавьте закрытый ключ `ssh-agent` с помощью команды `ssh-add`. На OS X эта команда запустит цепочку ключей, которая будет хранить учетные данные.

`[ahmet@fedora ~]$ ssh-add ~/.ssh/azure_fedora_id_rsa`

Пароль закрытого ключа теперь сохранен; вам не нужно будет вводить этот пароль при каждом входе с использованием SSH.

## Создание и настройка файла конфигурации SSH

Файл `~/.ssh/config` не является необходимым для установки и запуска виртуальной машины Linux, но мы рекомендуем все же создать и настроить его. Это позволит предотвратить непреднамеренное использование паролей для входа в ВМ, автоматизировать использование разных пар ключей для разных виртуальных машин Azure, а также настроить для работы с несколькими серверами другие программы, например **git**.

Ниже приведены шаги для выполнения стандартной конфигурации.

### Создание файла

```bash
ahmet@fedora$ touch ~/.ssh/config
```

### Изменение файла путем добавления новой конфигурации SSH

```bash
ahmet@fedora$ vim ~/.ssh/config

#Azure Keys
Host fedora22
  Hostname 102.160.203.241
  User ahmet
  PubkeyAuthentication yes
  IdentityFile /home/ahmet/.ssh/azure_fedora_id_rsa
# ./Azure Keys
# GitHub keys
Host github.com
  Hostname github.com
  User git
  PubKeyAuthentication yes
  IdentityFile /home/ahmet/.ssh/azure_fedora_id_rsa
Host github.private
  Hostname github.com
  User git
  PubKeyAuthentication yes
  IdentityFile /home/ahmet/.ssh/private_repo_azure_fedora_id_rsa
# ./Github Keys
# Default Settings
Host *
  PubkeyAuthentication=no
  IdentitiesOnly=yes
  ServerAliveInterval=60
  ServerAliveCountMax=30
  ControlMaster auto
  ControlPath /home/ahmet/.ssh/Connections/ssh-%r@%h:%p
  ControlPersist 4h
  StrictHostKeyChecking=no
  IdentityFile /home/ahmet/.ssh/id_rsa
  UseRoaming=no
```

Эта конфигурация SSH предусматривает отдельные разделы для служб, чтобы каждая из них могла использовать выделенную ей пару ключей. Параметры по умолчанию используются для всех узлов, на которые вы вошли и которые не принадлежат ни к одной из вышеуказанных групп. Такая конфигурация SSH также позволяет применять два отдельных имени для входа в [GitHub](https://github.com): одно для общедоступных ресурсов, а другое для частных репозиториев, которые могут использоваться в вашей работе.


##### Описание файла конфигурации

`Host` — имя узла, вызываемого в терминале. Команда `ssh fedora22` сообщает `SSH`, что нужно использовать значения из блока параметров с меткой `Host fedora22`. Примечание: вы можете использовать любую удобную для вас метку, не обязательно связанную с именем узла любого из серверов.

`Hostname 102.160.203.241` — IP-адрес или DNS-имя сервера, на который будет выполняться вход. Он используется для маршрутизации на сервер и может быть внешним IP-адресом, сопоставимым с внутренним IP-адресом.

`User git` — учетная запись пользователя удаленного узла, которую нужно использовать при входе на виртуальную машину Azure.

`PubKeyAuthentication yes` — сообщает SSH, что для входа вы используете ключ SSH.

`IdentityFile /home/ahmet/.ssh/azure_fedora_id_rsa` — сообщает SSH, какую пару ключей следует использовать для прохождения проверки подлинности при входе на сервер.


## Вход в виртуальную машину Linux с использованием SSH без пароля

Теперь, когда есть пара ключей SSH и настроен файл конфигурации SSH, вход в виртуальную машину Linux выполняется быстро и безопасно. При первом входе на сервер с использованием ключа SSH команда отправляет запрос на парольную фразу для этого файла ключа.

`ahmet@fedora$ ssh fedora22`

##### Описание команды

Когда выполняется команда `ahmet@fedora$ ssh fedora22`, SSH сначала находит и загружает все параметры из блока `Host fedora22`, а затем загружает все недостающие параметры из последнего блока (`Host *`).

## Дальнейшие действия

Теперь вы можете использовать файлы ключей SSH для выполнения следующих задач.

- [Создание защищенной виртуальной машины Linux с помощью шаблона Azure](virtual-machines-linux-create-ssh-secured-vm-from-template.md)
- [Создание защищенной виртуальной машины Linux с помощью портала Azure](virtual-machines-linux-quick-create-portal.md)
- [Создание защищенной виртуальной машины Linux с помощью интерфейса командной строки Azure](virtual-machines-linux-quick-create-cli.md)

<!---HONumber=AcomDC_0420_2016-->