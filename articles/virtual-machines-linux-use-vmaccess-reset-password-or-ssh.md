<properties 
	pageTitle="Как использовать VMAccess для виртуальных машин Linux" 
	description="Как использовать расширение VMAccess для Linux для сброса паролей и ключей SSH, повторной отправки конфигураций SSH и удаления пользователей Linux" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/17/2015" 
	ms.author="kathydav"/>

# Как сбросить пароль или ключ SSH в виртуальных машинах Linux #

Если вы не можете подключиться к виртуальной машине Linux из-за забытого пароля, неверного ключа Secure Shell (SSH) или проблем с конфигурацией SSH, воспользуйтесь расширением VMAccessForLinux, чтобы сбросить пароль или ключ SSH либо исправить конфигурацию SSH. 

## Требования

- Microsoft Azure Linux Agent версии 2.0.5 или более поздней версии. В большинстве образов Linux в коллекции виртуальной машины есть версия 2.0.5. Чтобы узнать, какая версия установлена, выполните `waagent -version`. Чтобы обновить агент, следуйте указаниям в статье [Руководство пользователя агента Linux для Azure].
- Azure PowerShell. Используйте команды в командлете **набора AzureVMExtension**, чтобы автоматически загрузить и настроить расширение **VMAccessForLinux**. Дополнительные сведения о настройке Azure PowerShell см. в разделе [Установка и настройка Azure PowerShell].
- Новый пароль или набор ключей SSH, если вы хотите сбросить один из них. они не нужны, если вы хотите сбросить конфигурацию SSH. 

## Установка не нужна

Для использования расширения VMAccess его необязательно нужно устанавливать. Если на виртуальной машине установлен агент Linux, модуль загружается автоматически при запуске команды Azure PowerShell, использующей командлет **Set-AzureVMExtension**. 

## Используйте это расширение для сброса пароля, ключа SSH или конфигурации SSH или для удаления пользователя.

Используйте командлет **Set-AzureVMExtension**, чтобы вносить любые изменения, которые можно сделать с помощью VMAccess. В любом случае сначала получите объект виртуальной машины, использовав имя облачной службы и имя виртуальной машины, и сохраните его в переменной. 

Укажите имена облачной службы и виртуальной машины, а затем выполните следующие команды в командной строке Azure PowerShell уровня администратора. Замените все содержимое внутри кавычек, включая символы < и >.

	$CSName = "<cloud service name>"
	$VMName = "<virtual machine name>"
	$vm = Get-AzureVM -ServiceName $CSName -Name $VMName

Если вы не знаете имя облачной службы и виртуальной машины, запустите командлет **Get-AzureVM**, чтобы отобразить эти сведения для всех виртуальных машин в вашей текущей подписке.


> [AZURE.NOTE] В строках команд PowerShell, начинающихся с $, задаются переменные PowerShell, которые впоследствии используются в командах PowerShell.

В случае создания виртуальной машины с помощью портала управления Azure выполните следующую дополнительную команду:

	$vm.GetInstance().ProvisionGuestAgent = $true

Эта команда будет предотвращать возникновение ошибки о том, что перед настройкой расширения виртуальных машин IaaS требуется включить гостевой агент подготовки в объекте виртуальной машины, при выполнении команды Set-AzureVMExtension в следующих разделах. 

После этого вы можете выполнить следующие задачи.

+ [Сброс пароля](#password)
+ [Сброс ключа SSH](#SSHkey)
+ [Сброс пароля и ключа SSH](#both)
+ [Сброс конфигурации SSH](#config)
+ [Удаление пользователя](#delete)

### <a name="password"></a>Сброс пароля

Введите имя текущего пользователя Linux и новый пароль, а затем выполните следующие команды.

	$UserName = "<current Linux account name>"
	$Password = "<new password>"
	$PrivateConfig = '{"username":"' + $UserName + '", "password": "' +  $Password + '"}' 
	$ExtensionName = "VMAccessForLinux"
	$Publisher = "Microsoft.OSTCExtensions"
	$Version =  "1.*"
	Set-AzureVMExtension -ExtensionName $ExtensionName -VM $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

> [AZURE.NOTE] Если вам необходимо сбросить пароль или ключ SSH для существующей учетной записи, убедитесь, что вы ввели точное имя пользователя. Если вы введете другое имя, расширение VMAccess создаст новую учетную запись и назначит ей пароль.


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

> [AZURE.NOTE] Файл конфигурации SSH расположен по следующему пути: /etc/ssh/sshd_config.

### <a name="delete"></a> Удаление пользователя

Введите имя пользователя Linux для удаления и выполните следующие команды.

	$UserName = "<Linux user name to delete>"
	$PrivateConfig = "{"remove_user": "' + $UserName + '"}"
	$ExtensionName = "VMAccessForLinux"
	$Publisher = "Microsoft.OSTCExtensions"
	$Version = "1.*"
	Set-AzureVMExtension -ExtensionName $ExtensionName -VM $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

## Дополнительные ресурсы

[Расширения и компоненты виртуальных машин Azure] []

[Подключение к виртуальной машине Azure с помощью RDP или SSH] []


<!--Link references-->
[Руководство пользователя агента Linux для Azure]: ../virtual-machines-linux-agent-user-guide
[Установка и настройка Azure PowerShell]: ../install-configure-powershell
[Расширения и компоненты виртуальных машин Azure]: http://msdn.microsoft.com/library/azure/dn606311.aspx
[Подключение к виртуальной машине Azure с помощью RDP или SSH]: http://msdn.microsoft.com/library/azure/dn535788.aspx






<!--HONumber=45--> 
