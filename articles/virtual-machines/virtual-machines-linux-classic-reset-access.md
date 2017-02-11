---
title: "Сброс пароля и ключа SSH виртуальной машины Linux из интерфейса командной строки | Документация Майкрософт"
description: "Сведения об использовании расширения VMAccess из интерфейса командной строки Azure (Azure CLI) для сброса пароля или ключа SSH, исправления конфигурации SSH и проверки согласованности диска виртуальной машины Linux."
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: d975eb70-5ff1-40d1-a634-8dd2646dcd17
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 06/14/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 63cf1a5476a205da2f804fb2f408f4d35860835f
ms.openlocfilehash: b820e3011a1cd5472604e8cfa98fc22f9a4ebf77


---
# <a name="how-to-reset-a-linux-vm-password-or-ssh-key-fix-the-ssh-configuration-and-check-disk-consistency-using-the-vmaccess-extension"></a>Использование расширения VMAccess для сброса пароля или ключа SSH, исправления конфигурации SSH и проверки согласованности диска виртуальной машины Linux
Если вы не можете подключиться к виртуальной машине Linux в Azure из-за утерянного пароля, неправильного ключа SSH или проблем с конфигурацией SSH, воспользуйтесь расширением VMAccessForLinux с интерфейсом командной строки Azure, чтобы сбросить пароль или ключ SSH либо исправить конфигурацию SSH и проверить согласованность диска. 

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Узнайте, как [выполнить эти действия с помощью модели Resource Manager](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess).

Благодаря Azure CLI команду **azure vm extension set** можно использовать прямо в интерфейсе командной строки (это может быть Bash, окно терминала, командная строка) для доступа к командам. Выполните команду **azure help vm extension set** для получения подробных сведений об использовании расширения.

Благодаря Azure CLI вы сможете выполнять следующие задачи:

* [Сброс пароля](#pwresetcli)
* [Сброс ключа SSH](#sshkeyresetcli)
* [Сброс пароля и ключа SSH](#resetbothcli)
* [Создание новой учетной записи пользователя sudo](#createnewsudocli)
* [Сброс конфигурации SSH](#sshconfigresetcli)
* [Удаление пользователя](#deletecli)
* [Отображение состояния расширения VMAccess](#statuscli)
* [проверка согласованности добавленных дисков](#checkdisk)
* [восстановление дисков, добавленных на виртуальной машине Linux.](#repairdisk)

## <a name="prerequisites"></a>Предварительные требования
Выполните следующие действия:

* Вам также нужно [установить интерфейс командной строки Azure](../xplat-cli-install.md) и [подключиться к своей подписке](../xplat-cli-connect.md) для использования ресурсов Azure, связанных с вашей учетной записью.
* Задайте режим, необходимый для классической модели развертывания, введя в командной строке следующее:
  
        azure config mode asm
* Имейте при себе новый пароль или набор ключей SSH, если хотите сбросить один из них. они не нужны, если вы хотите сбросить конфигурацию SSH.

## <a name="a-namepwresetcliareset-the-password"></a><a name="pwresetcli"></a>Сброс пароля
1. Создайте файл с именем PrivateConf.json с такими строками. Замените значения в скобках и &#60;заполнители&#62; собственными данными.
   
        {
        "username":"<currentusername>",
        "password":"<newpassword>",
        "expiration":"<2016-01-01>"
        }
2. Запустите эту команду, подставив имя вашей виртуальной машины вместо &#60;vm-name&#62;.
   
        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* –-private-config-path PrivateConf.json

## <a name="a-namesshkeyresetcliareset-the-ssh-key"></a><a name="sshkeyresetcli"></a>Сброс ключа SSH
1. Создайте файл с именем PrivateConf.json с таким содержимым. Замените значения в скобках и &#60;заполнители&#62; собственными данными.
   
        {
        "username":"<currentusername>",
        "ssh_key":"<contentofsshkey>"
        }
2. Запустите эту команду, подставив имя вашей виртуальной машины вместо &#60;vm-name&#62;.
   
        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

## <a name="a-nameresetbothcliareset-both-the-password-and-the-ssh-key"></a><a name="resetbothcli"></a>Сброс пароля и ключа SSH
1. Создайте файл с именем PrivateConf.json с таким содержимым. Замените значения в скобках и &#60;заполнители&#62; собственными данными.
   
        {
        "username":"<currentusername>",
        "ssh_key":"<contentofsshkey>",
        "password":"<newpassword>"
        }
2. Запустите эту команду, подставив имя вашей виртуальной машины вместо &#60;vm-name&#62;.
   
        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

## <a name="a-namecreatenewsudocliacreate-a-new-sudo-user-account"></a><a name="createnewsudocli"></a>Создание новой учетной записи пользователя sudo
Если вы забыли имя пользователя, вы можете воспользоваться расширением VMAccess для создания нового имени с полномочиями sudo. В этом случае существующие имя пользователя и пароль останутся без изменений.

Чтобы создать нового пользователя sudo с доступом по паролю, используйте сценарий для [Сброса пароля](#pwresetcli) и укажите новое имя пользователя.

Чтобы создать нового пользователя sudo с доступом по ключу SSH, используйте сценарий для [Сброса ключа SSH](#sshkeyresetcli) и укажите новое имя пользователя.

Также вы можете использовать сценарий для [Сброса пароля и ключа SSH](#resetbothcli) для создания нового пользователя с доступом как по паролю, так и по ключу SSH.

## <a name="a-namesshconfigresetcliareset-the-ssh-configuration"></a><a name="sshconfigresetcli"></a>Сброс конфигурации SSH
Если конфигурация SSH нарушена, вы можете потерять доступ к виртуальной машине. Чтобы сбросить конфигурацию до состояния по умолчанию, вы можете использовать расширение VMAccess. Для этого вам необходимо просто установить ключ "reset_ssh" в значение "True". Расширение перезапускает SSH-сервер, открывает порт SSH в вашей виртуальной машине и сбрасывает настройки SSH до значений по умолчанию. Учетная запись пользователя (имя, пароль или ключи SSH) останется без изменений.

> [!NOTE]
> Файл конфигурации SSH расположен по следующему пути: /etc/ssh/sshd_config.
> 
> 

1. Создайте файл с именем PrivateConf.json с таким содержимым.
   
        {
        "reset_ssh":"True"
        }
2. Запустите эту команду, подставив имя вашей виртуальной машины вместо &#60;vm-name&#62;. 
   
        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

## <a name="a-namedeletecliadelete-a-user"></a><a name="deletecli"></a>Удаление пользователя
Чтобы удалить учетную запись пользователя без выполнения входа в виртуальную машину, вы можете воспользоваться таким сценарием.

1. Создайте файл с именем PrivateConf.json с таким содержимым, подставив имя пользователя, которое необходимо удалить, вместо &#60;usernametoremove&#62;. 
   
        {
        "remove_user":"<usernametoremove>"
        }
2. Запустите эту команду, подставив имя вашей виртуальной машины вместо &#60;vm-name&#62;. 
   
        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

## <a name="a-namestatuscliadisplay-the-status-of-the-vmaccess-extension"></a><a name="statuscli"></a>Отображение состояния расширения VMAccess
Чтобы отобразить состояние расширения VMAccess, запустите следующую команду.

        azure vm extension get

## <a name="a-namecheckdiskacheck-consistency-of-added-disks"></a><a name='checkdisk'<</a>Проверка согласованности добавленных дисков
Для запуска служебной программы fsck на всех дисках виртуальной машины Linux необходимо сделать следующее.

1. Создайте файл с именем PublicConf.json с таким содержимым. Проверка диска принимает логическое значение, указывающее, нужно ли выполнять проверку дисков, подключенных к виртуальной машине. 
   
        {   
        "check_disk": "true"
        }
2. Запустите выполнение этой команды, подставив имя вашей виртуальной машины вместо &#60;vm-name&#62;.
   
        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* --public-config-path PublicConf.json 

## <a name="a-namerepairdiskarepair-disks"></a><a name='repairdisk'></a>Восстановление дисков
Чтобы восстановить диски, которые не подключаются или при подключении которых возникают ошибки конфигурации, используйте расширение VMAccess для сброса конфигурации подключения на виртуальной машине Linux. При этом подставьте имя своего диска вместо &#60;yourdisk&#62;.

1. Создайте файл с именем PublicConf.json с таким содержимым. 
   
        {
        "repair_disk":"true",
        "disk_name":"<yourdisk>"
        }
2. Запустите выполнение этой команды, подставив имя вашей виртуальной машины вместо &#60;vm-name&#62;.
   
        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* --public-config-path PublicConf.json

## <a name="next-steps"></a>Дальнейшие действия
* Сведения об использовании командлетов Azure PowerShell или шаблонов Azure Resource Manager для сброса пароля или ключа SSH, исправления конфигурации SSH и проверки согласованности диска см. в [документации по расширению VMAccess на портале GitHub](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess). 
* Для сброса пароля или ключа SSH для виртуальной машины Linux, развернутой в классической модели развертывания, можно также использовать [портал Azure](https://portal.azure.com) . Использовать портал для виртуальной машины Linux, развернутой в модели развертывания Resource Manager, пока невозможно.
* В статье [Обзор расширений и компонентов виртуальной машины](virtual-machines-linux-extensions-features.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) приводятся дополнительные сведения об использовании расширений для виртуальных машин Azure.




<!--HONumber=Nov16_HO3-->


