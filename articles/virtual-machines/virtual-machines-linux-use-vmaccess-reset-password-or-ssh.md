<properties
	pageTitle="Сброс пароля виртуальной машины Linux с помощью Azure CLI | Microsoft Azure"
	description="Использование расширения VMAccess на классическом портале Azure или Azure CLI для сброса паролей и ключей SSH, конфигурации SSH и удаления учетных записей пользователей виртуальной машины Linux."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/28/2015"
	ms.author="cynthn"/>

# Как сбросить пароль или ключ SSH в виртуальных машинах Linux #

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Модель диспетчера ресурсов.


Если вы не можете подключиться к виртуальной машине Linux из-за забытого пароля, неверного ключа Secure Shell (SSH) или проблем с конфигурацией SSH, воспользуйтесь порталом Azure или расширением VMAccessForLinux, чтобы сбросить пароль или ключ SSH либо исправить конфигурацию SSH. Обратите внимание, что сведения в этой статье применимы к виртуальным машинам, созданным с использованием **классической** модели развертывания.

## Портал Azure

Чтобы сбросить конфигурацию SSH на [портале Azure](https://portal.azure.com), щелкните **Обзор** > **Виртуальные машины** > *ваша виртуальная машина Linux* > **Сброс удаленного доступа**. Ниже приведен пример.

![](./media/virtual-machines-linux-use-vmaccess-reset-password-or-ssh/Portal-RDP-Reset-Linux.png)

Чтобы сбросить имя и пароль учетной записи пользователя с привилегиями sudo или открытым ключом SSH на [портале Azure](https://portal.azure.com), щелкните **Обзор** > **Виртуальные машины** > *ваша виртуальная машина Linux * > **Все параметры** > **Сброс пароля**. Ниже приведен пример.

![](./media/virtual-machines-linux-use-vmaccess-reset-password-or-ssh/Portal-PW-Reset-Linux.png)


## Azure CLI и PowerShell

Кроме этого, вам понадобится следующее ПО:

- Microsoft Azure Linux Agent версии 2.0.5 или более поздней версии. В большинстве образов Linux в коллекции виртуальной машины есть версия 2.0.5. Чтобы узнать, какая версия установлена, выполните **waagent -version**. Чтобы обновить агент, следуйте указаниям в статье [Руководство пользователя агента Linux для Azure].
- Интерфейс командной строки Azure (CLI). Сведения о настройке Azure CLI см. в разделе [Установка и настройка интерфейса командной строки Azure](../xplat-cli-install.md).
- Azure PowerShell. Используйте команды в командлете набора AzureVMExtension, чтобы автоматически загрузить и настроить расширение VMAccessForLinux. Дополнительные сведения о настройке Azure PowerShell см. в разделе [Установка и настройка Azure PowerShell].
- Новый пароль или набор ключей SSH, если вы хотите сбросить один из них. они не нужны, если вы хотите сбросить конфигурацию SSH.

### Установка не нужна

Для использования расширения VMAccess его не обязательно нужно устанавливать. Если на виртуальной машине установлен агент Linux, модуль загружается автоматически при запуске команды Azure PowerShell, использующей командлет **Set-AzureVMExtension**.

## Использование Azure CLI

Благодаря Azure CLI вы сможете использовать команду **azure** прямо в интерфейсе командной строки (Bash, терминал, командная строка) для доступа к командам. Запустите **azure vm extension set –help** для более полного использования модуля.

Благодаря Azure CLI вы сможете выполнять следующие задачи:

+ [Сброс пароля](#pwresetcli)
+ [Сброс ключа SSH](#sshkeyresetcli)
+ [Сброс пароля и ключа SSH](#resetbothcli)
+ [Создание новой учетной записи пользователя sudo](#createnewsudocli)
+ [Сброс конфигурации SSH](#sshconfigresetcli)
+ [Удаление пользователя](#deletecli)
+ [Отображение состояния расширения VMAccess](#statuscli)

### <a name="pwresetcli"></a>Сброс пароля

Шаг 1. Создайте файл с именем PrivateConf.json с таким содержимым, подставив значения заполнителей.

	{
	"username":"currentusername",
	"password":"newpassword",
	"expiration":"2016-01-01",
	}

Шаг 2. Запустите эту команду, подставив имя вашей виртуальной машины вместо "vmname".

	azure vm extension set vmname VMAccessForLinux Microsoft.OSTCExtensions 1.* –-private-config-path PrivateConf.json

### <a name="sshkeyresetcli"></a>Сброс ключа SSH

Шаг 1. Создайте файл с именем PrivateConf.json с таким содержимым, подставив значения заполнителей.

	{
	"username":"currentusername",
	"ssh_key":"contentofsshkey",
	}

Шаг 2. Запустите эту команду, подставив имя вашей виртуальной машины вместо "vmname".

	azure vm extension set vmname VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

### <a name="resetbothcli"></a>Сброс пароля и ключа SSH

Шаг 1. Создайте файл с именем PrivateConf.json с таким содержимым, подставив значения заполнителей.

	{
	"username":"currentusername",
	"ssh_key":"contentofsshkey",
	"password":"newpassword",
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

> [AZURE.NOTE]Файл конфигурации SSH расположен по следующему пути: /etc/ssh/sshd\_config.

Шаг 1. Создайте файл с именем PrivateConf.json с таким содержанием.

	{
	"reset_ssh":"True",
	}

Шаг 2. Запустите эту команду, подставив имя вашей виртуальной машины вместо "vmname".

	azure vm extension set vmname VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

### <a name="deletecli"></a>Удаление пользователя

Чтобы удалить учетную запись пользователя без выполнения входа в виртуальную машину, вы можете воспользоваться таким сценарием.

Шаг 1. Создайте файл с именем PrivateConf.json с таким содержимым, подставив значение заполнителя.

	{
	"remove_user":"usernametoremove",
	}

Шаг 2. Запустите эту команду, подставив имя вашей виртуальной машины вместо "vmname".

	azure vm extension set vmname VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

### <a name="statuscli"></a>Отображение состояния расширения VMAccess

Чтобы отобразить состояние расширения VMAccess, запустите следующую команду.

	azure vm extension get


## Использование Azure PowerShell

Используйте командлет **Set-AzureVMExtension**, чтобы вносить любые изменения, которые можно сделать с помощью VMAccess. В любом случае сначала получите объект виртуальной машины, использовав имя облачной службы и имя виртуальной машины, и сохраните его в переменной.

Укажите имена облачной службы и виртуальной машины, а затем выполните следующие команды в командной строке Azure PowerShell уровня администратора. Замените все содержимое внутри кавычек, включая символы < and >.

	$CSName = "<cloud service name>"
	$VMName = "<virtual machine name>"
	$vm = Get-AzureVM -ServiceName $CSName -Name $VMName

Если вы не знаете имя облачной службы и виртуальной машины, запустите командлет **Get-AzureVM**, чтобы отобразить эти сведения для всех виртуальных машин в вашей текущей подписке.


> [AZURE.NOTE]В строках команд PowerShell, начинающихся с $, задаются переменные PowerShell, которые впоследствии используются в командах PowerShell.

Если вы создали виртуальную машину с помощью классического портала Azure, выполните следующую дополнительную команду:

	$vm.GetInstance().ProvisionGuestAgent = $true

Эта команда будет предотвращать возникновение ошибки о том, что перед настройкой расширения виртуальных машин IaaS требуется включить гостевой агент подготовки в объекте виртуальной машины, при выполнении команды Set-AzureVMExtension в следующих разделах.

После этого вы можете выполнить следующие задачи.

+ [Сброс пароля](#password)
+ [Сброс ключа SSH](#SSHkey)
+ [Сброс пароля и ключа SSH](#both)
+ [Сброс конфигурации SSH](#config)
+ [Удаление пользователя](#delete)
+ [Отображение состояния расширения VMAccess](#status)

### <a name="password"></a>Сброс пароля

Введите имя текущего пользователя Linux и новый пароль, а затем выполните следующие команды.

	$UserName = "<current Linux account name>"
	$Password = "<new password>"
	$PrivateConfig = '{"username":"' + $UserName + '", "password": "' +  $Password + '"}'
	$ExtensionName = "VMAccessForLinux"
	$Publisher = "Microsoft.OSTCExtensions"
	$Version =  "1.*"
	Set-AzureVMExtension -ExtensionName $ExtensionName -VM $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

> [AZURE.NOTE]Если вам необходимо сбросить пароль или ключ SSH для существующей учетной записи, убедитесь, что вы ввели точное имя пользователя. Если вы введете другое имя, расширение VMAccess создаст новую учетную запись и назначит ей пароль.


### <a name="SSHKey"></a>Сброс ключа SSH

Введите имя текущего пользователя Linux и путь к сертификату с ключами SSH, а затем выполните следующие команды.

	$UserName = "<current Linux user name>"
	$Cert = Get-Content "<certificate path>"
	$PrivateConfig = '{"username":"' + $UserName + '", "ssh_key":"' + $cert + '"}'
	$ExtensionName = "VMAccessForLinux"
	$Publisher = "Microsoft.OSTCExtensions"
	$Version =  "1.*"
	Set-AzureVMExtension -ExtensionName $ExtensionName -VM  $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

### <a name="both"></a>Сброс пароля и ключа SSH

Введите имя текущего пользователя Linux, новый пароль и путь к сертификату с ключами SSH, а затем выполните следующие команды.

	$UserName = "<current Linux user name>"
	$Password = "<new password>"
	$Cert = Get-Content "<certificate path>"
	$PrivateConfig = '{"username":"' + $UserName + '", "password": "' +  $Password + '", "ssh_key":"' + $cert + '"}'
	$ExtensionName = "VMAccessForLinux"
	$Publisher = "Microsoft.OSTCExtensions"
	$Version =  "1.*"
	Set-AzureVMExtension -ExtensionName $ExtensionName -VM  $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

### <a name="config"></a>Сброс конфигурации SSH

Ошибки в конфигурации SSH могут заблокировать доступ к виртуальной машине. Это можно исправить, сбросив конфигурацию SSH в состояние по умолчанию. При этом удаляются все новые параметры доступа в конфигурации, такие как имя пользователя, пароль и ключ SSH, но не изменяется пароль или ключи SSH учетной записи пользователя. Расширение перезапускает сервер SSH, открывает порт SSH на вашей виртуальной машине и сбрасывает конфигурацию SSH в состояние по умолчанию.

Выполните следующие команды.

	$PrivateConfig = '{"reset_ssh": "True"}'
	$ExtensionName = "VMAccessForLinux"
	$Publisher = "Microsoft.OSTCExtensions"
	$Version = "1.*"
	Set-AzureVMExtension -ExtensionName $ExtensionName -VM  $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

> [AZURE.NOTE]Файл конфигурации SSH расположен по следующему пути: /etc/ssh/sshd\_config.

### <a name="delete"></a>Удаление пользователя

Введите имя пользователя Linux для удаления и выполните следующие команды.

	$UserName = "<Linux user name to delete>"
	$PrivateConfig = "{"remove_user": "' + $UserName + '"}"
	$ExtensionName = "VMAccessForLinux"
	$Publisher = "Microsoft.OSTCExtensions"
	$Version = "1.*"
	Set-AzureVMExtension -ExtensionName $ExtensionName -VM $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM


### <a name="status"></a>Отображение состояния расширения VMAccess

Чтобы отобразить состояние расширения VMAccess, запустите следующую команду.

	$vm.GuestAgentStatus


## Дополнительные ресурсы

[Расширения и компоненты виртуальных машин Azure][]

[Подключение к виртуальной машине Azure с помощью RDP или SSH][]


<!--Link references-->
[Руководство пользователя агента Linux для Azure]: virtual-machines-linux-agent-user-guide.md
[Установка и настройка Azure PowerShell]: ../install-configure-powershell.md
[Расширения и компоненты виртуальных машин Azure]: http://msdn.microsoft.com/library/azure/dn606311.aspx
[Подключение к виртуальной машине Azure с помощью RDP или SSH]: http://msdn.microsoft.com/library/azure/dn535788.aspx

<!---HONumber=AcomDC_1203_2015-->