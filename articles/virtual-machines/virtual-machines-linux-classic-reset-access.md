<properties
	pageTitle="Сброс пароля и ключа SSH виртуальной машины Linux из интерфейса командной строки | Microsoft Azure"
	description="Сведения об использовании расширения VMAccess из интерфейса командной строки Azure (Azure CLI) для сброса пароля или ключа SSH, исправления конфигурации SSH и проверки согласованности диска виртуальной машины Linux."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/20/2016"
	ms.author="cynthn"/>

# Сброс пароля для доступа, управление пользователями и проверка дисков с помощью расширения Azure VMAccess для Linux


Если вы не можете подключиться к виртуальной машине Linux в Azure из-за утерянного пароля, неправильного ключа SSH или проблем с конфигурацией SSH, воспользуйтесь расширением VMAccessForLinux с интерфейсом командной строки Azure, чтобы сбросить пароль или ключ SSH либо исправить конфигурацию SSH и проверить согласованность диска.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager model](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess).




## Инфраструктура CLI Azure

Кроме этого, вам понадобится следующее ПО:

- Интерфейс командной строки Azure (CLI). Вам потребуется [установить интерфейс командной строки Azure](../xplat-cli-install.md) и [подключиться к подписке](../xplat-cli-connect.md), чтобы использовать ресурсы Azure, связанные с вашей учетной записью.
- Новый пароль или набор ключей SSH, если вы хотите сбросить один из них. они не нужны, если вы хотите сбросить конфигурацию SSH.


## Использование команды azure vm extension set

Благодаря Azure CLI команду **azure vm extension set** можно использовать прямо в интерфейсе командной строки (Bash, Terminal, командная строка) для доступа к командам. Выполните команду **azure help vm extension set** для более полного использования расширения.

Благодаря Azure CLI вы сможете выполнять следующие задачи:

+ [Сброс пароля](#pwresetcli)
+ [Сброс ключа SSH](#sshkeyresetcli)
+ [Сброс пароля и ключа SSH](#resetbothcli)
+ [Создание новой учетной записи пользователя sudo](#createnewsudocli)
+ [Сброс конфигурации SSH](#sshconfigresetcli)
+ [Удаление пользователя](#deletecli)
+ [Отображение состояния расширения VMAccess](#statuscli)
+ [проверка согласованности добавленных дисков;](#checkdisk)
+ [восстановление дисков, добавленных на виртуальной машине Linux.](#repairdisk)

### <a name="pwresetcli"></a>Сброс пароля

Шаг 1. Создайте файл с именем PrivateConf.json с таким содержимым, подставив значения заполнителей.

	{
	"username":"currentusername",
	"password":"newpassword",
	"expiration":"2016-01-01"
	}

Шаг 2. Запустите эту команду, подставив имя вашей виртуальной машины вместо "vmname".

	azure vm extension set vmname VMAccessForLinux Microsoft.OSTCExtensions 1.* –-private-config-path PrivateConf.json

### <a name="sshkeyresetcli"></a>Сброс ключа SSH

Шаг 1. Создайте файл с именем PrivateConf.json с таким содержимым, подставив значения заполнителей.

	{
	"username":"currentusername",
	"ssh_key":"contentofsshkey"
	}

Шаг 2. Запустите эту команду, подставив имя вашей виртуальной машины вместо "vmname".

	azure vm extension set vmname VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

### <a name="resetbothcli"></a>Сброс пароля и ключа SSH

Шаг 1. Создайте файл с именем PrivateConf.json с таким содержимым, подставив значения заполнителей.

	{
	"username":"currentusername",
	"ssh_key":"contentofsshkey",
	"password":"newpassword"
	}

Шаг 2. Запустите эту команду, подставив имя вашей виртуальной машины вместо "vmname".

	azure vm extension set vmname VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

### <a name="createnewsudocli"></a>Создание новой учетной записи пользователя sudo

Если вы забыли имя пользователя, вы можете воспользоваться расширением VMAccess для создания нового имени с полномочиями sudo. В этом случае существующие имя пользователя и пароль останутся без изменений.

Чтобы создать нового пользователя sudo с доступом по паролю, используйте сценарий для [Сброса пароля](#pwresetcli) и укажите новое имя пользователя.

Чтобы создать нового пользователя sudo с доступом по ключу SSH, используйте сценарий для [Сброса ключа SSH](#sshkeyresetcli) и укажите новое имя пользователя.

Также вы можете использовать сценарий для [Сброса пароля и ключа SSH](#resetbothcli) для создания нового пользователя с доступом как по паролю, так и по ключу SSH.

### <a name="sshconfigresetcli"></a>Сброс конфигурации SSH

Если конфигурация SSH нарушена, вы можете потерять доступ к виртуальной машине. Чтобы сбросить конфигурацию до состояния по умолчанию, вы можете использовать расширение VMAccess. Для этого вам необходимо просто установить ключ "reset\_ssh" в значение "True". Расширение перезапускает SSH-сервер, открывает порт SSH в вашей виртуальной машине и сбрасывает настройки SSH до значений по умолчанию. Учетная запись пользователя (имя, пароль или ключи SSH) останется без изменений.

> [AZURE.NOTE] Файл конфигурации SSH расположен по следующему пути: /etc/ssh/sshd\_config.

Шаг 1. Создайте файл с именем PrivateConf.json с таким содержанием.

	{
	"reset_ssh":"True"
	}

Шаг 2. Запустите эту команду, подставив имя вашей виртуальной машины вместо "vmname".

	azure vm extension set vmname VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

### <a name="deletecli"></a>Удаление пользователя

Чтобы удалить учетную запись пользователя без выполнения входа в виртуальную машину, вы можете воспользоваться таким сценарием.

Шаг 1. Создайте файл с именем PrivateConf.json с таким содержимым, подставив значение заполнителя.

	{
	"remove_user":"usernametoremove"
	}

Шаг 2. Запустите эту команду, подставив имя вашей виртуальной машины вместо "vmname".

	azure vm extension set vmname VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

### <a name="statuscli"></a>Отображение состояния расширения VMAccess

Чтобы отобразить состояние расширения VMAccess, запустите следующую команду.

	azure vm extension get

### <a name='checkdisk'<</a>Проверка согласованности добавленных дисков

Для запуска служебной программы fsck на всех дисках виртуальной машины Linux необходимо сделать следующее.

Шаг 1. Создайте файл с именем PublicConf.json и приведенным ниже содержимым. Проверка диска принимает логическое значение, указывающее, нужно ли выполнять проверку дисков, подключенных к виртуальной машине.

    {   
    "check_disk": "true"
    }

Шаг 2. Запустите выполнение этой команды, подставив значения заполнителей.

   azure vm extension set vm-name VMAccessForLinux Microsoft.OSTCExtensions 1.* --public-config-path PublicConf.json

### <a name='repairdisk'></a>Восстановление дисков, добавленных на виртуальной машине Linux

Чтобы восстановить диски, которые не подключаются или при подключении которых возникают ошибки конфигурации, используйте расширение VMAccess для сброса конфигурации подключения на виртуальной машине Linux.

Шаг 1. Создайте файл с именем PublicConf.json и приведенным ниже содержимым.

    {
    "repair_disk":"true",
    "disk_name":"yourdisk"
    }

Шаг 2. Запустите выполнение этой команды, подставив значения заполнителей.

    azure vm extension set vm-name VMAccessForLinux Microsoft.OSTCExtensions 1.* --public-config-path PublicConf.json



## Дополнительные ресурсы

* Сведения об использовании командлетов Azure PowerShell или шаблонов Azure Resource Manager для сброса пароля или ключа SSH, исправления конфигурации SSH и проверки согласованности диска см. в [документации о расширении VMAccess на портале GitHub](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess). 

* Для сброса пароля или ключа SSH для виртуальной машины Linux, развернутой в классической модели развертывания, можно также использовать [портал Azure](https://portal.azure.com). Использовать портал для виртуальной машины Linux, развернутой в модели развертывания Resource Manager, пока невозможно.

* В статье [Обзор расширений и компонентов виртуальной машины](virtual-machines-linux-extensions-features.md) приводятся дополнительные сведения об использовании расширений виртуальных машин для виртуальных машин Azure.

<!---HONumber=AcomDC_0504_2016-->