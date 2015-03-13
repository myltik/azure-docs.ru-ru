<properties 
	pageTitle="Как использовать VMAccess для виртуальных машин Linux" 
	description="Как использовать расширение VMAccess для сброса паролей, ключей SSH и конфигурации SSH в виртуальных машинах Linux" 
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
	ms.date="10/30/2014" 
	ms.author="kathydav"/>

#Как сбросить пароль или ключ SSH в виртуальных машинах Linux#

Если вы не можете подключиться к виртуальной машине Linux потому, что забыли пароль или ключ SSH либо возникла проблема, используйте расширение VMAccessforLinux, чтобы сбросить пароль, ключ SSH или конфигурацию SSH. 


##Требования

- Microsoft Azure Linux Agent версии 2.0.5 или более поздней версии. В большинстве образов Linux в коллекции виртуальной машины есть версия 2.0.5. Чтобы узнать, какая версия установлена, выполните  `waagent -version`. Чтобы обновить агент, следуйте указаниям в статье [Руководство пользователя агента Linux для Azure].

- Модуль Azure PowerShell. В модуль включен командлет **Set-AzureVMExtension**, с помощью которого вы будете выполнять команды, чтобы использовать расширение **VMAccessForLinux**. Дополнительную информацию о настройке модуля см. в статье [Установка и настройка Azure PowerShell].

- Новый пароль или новые ключи SSH, если вам необходимо сбросить один из них. Они не нужны, если вам необходимо исправить конфигурацию SSH. 

##Установка не нужна

Для использования VMAccess не требуется его установка. Если установлены агент Linux и модуль Azure, при запуске команды, которая вызывает командлет **Set-AzureVMExtension**, расширение будет загружаться автоматически. 

##Использование расширения для сброса пароля, ключа или конфигурации SSH, а также для добавления пользователя

Используйте командлет **Set-AzureVMExtension**, чтобы вносить любые изменения, которые можно сделать с помощью VMAccess. В любом случае сначала получите объект виртуальной машины, использовав имя облачной службы и имя виртуальной машины, и сохраните его в переменной.   

Откройте Azure PowerShell и введите в командной строке следующую команду. Обязательно замените заполнители MyServiceName и MyVMName фактическими именами:

	PS C:\> $vm = Get-AzureVM -ServiceName 'MyServiceName' -Name 'MyVMName'

После этого вы можете выполнить следующие задачи.

+ [Сброс пароля](#password)
+ [Сброс ключа SSH](#SSHkey)
+ [Сброс пароля и ключа SSH](#both)
+ [Сброс конфигурации SSH](#config)

### <a name="password"></a>Сброс пароля
Введите имя пользователя и пароль, сохраните их в переменных, а затем создайте единую переменную, чтобы сохранить значения в том виде, в котором их смогут использовать следующие команды.

	PS C:\> $UserName = "CurrentName"
	PS C:\> $Password = "NewPassword"
	PS C:\> $PrivateConfig = '{"username":"' + $UserName + '", "password": "' +  $Password + '"}' 

Сохраните имя, данные об издателе и номер версии в переменных: 

	PS C:\> ExtensionName = 'VMAccessForLinux'
	PS C:\> $Publisher = 'Microsoft.OSTCExtensions'
	PS C:\> $Version =  '1.0'

Если все необходимые значения сохранены в переменных, выполните следующую команду:

	PS C:\> Set-AzureVMExtension -ExtensionName $ExtensionName -VM  $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

> [AZURE.NOTE] Если вам необходимо сбросить пароль или ключ SSH для существующей учетной записи, убедитесь, что вы ввели точное имя пользователя. Если вы введете другое имя, расширение VMAccess создаст новую учетную запись и назначит ей пароль.

### <a name="SSHkey"></a>Сброс ключа SSH

Введите имя пользователя и путь к новому открытому ключу SSH, а затем сохраните их в переменных:

	PS C:\> $UserName = "CurrentName"
	PS C:\> $cert = Get-Content "CertPath"
	PS C:\> $PrivateConfig = '{"username":"' + $UserName + '", "ssh_key":"' + $cert + '"}'

Выполните следующие команды:

	PS C:\> $ExtensionName = 'VMAccessForLinux'
	PS C:\> $Publisher = 'Microsoft.OSTCExtensions'
	PS C:\> $Version =  '1.0'
	PS C:\> Set-AzureVMExtension -ExtensionName $ExtensionName -VM  $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM


### <a name="both"></a>Сброс пароля и ключа SSH

Введите новый пароль и путь к новому сертификату с открытым ключом для текущего пользователя и сохраните их в переменных: 

	PS C:\> $UserName = "CurrentName"	
	PS C:\> $Password = "NewPassword"
	PS C:\> $cert = Get-Content "CertPath"
	PS C:\> $PrivateConfig = '{"username":"' + $UserName + '", "password": "' +  $Password + '", "ssh_key":"' + $cert + '"}' 

Выполните следующие команды:

	PS C:\> $ExtensionName = 'VMAccessForLinux'
	PS C:\> $Publisher = 'Microsoft.OSTCExtensions'
	PS C:\> $Version =  '1.0'
	PS C:\> Set-AzureVMExtension -ExtensionName $ExtensionName -VM  $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

###  <a name="config"></a>Сброс конфигурации SSH

Ошибки в конфигурации SSH могут препятствовать получению доступа к виртуальной машине. Вы можете исправить это, сбросив конфигурацию и задав значения по умолчанию. Это действие удалит все новые параметры доступа в конфигурации (имя пользователя, пароль или ключ SSH). При этом пароль или ключи SSH учетной записи не изменятся. Расширение перезапускает SSH-сервер, открывает SSH-порт в вашей виртуальной машине и сбрасывает настройки SSH по умолчанию.  

Установите флажок, который указывает, что вам необходимо сбросить конфигурацию и сохранить ее в переменной: 
	
	PS C:\> $PrivateConfig = '{"reset_ssh": "True"}' 

Выполните следующие команды:

	PS C:\> $ExtensionName = 'VMAccessForLinux'
	PS C:\> $Publisher = 'Microsoft.OSTCExtensions'
	PS C:\> $Version =  '1.0'
	PS C:\> Set-AzureVMExtension -ExtensionName $ExtensionName -VM  $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

> [AZURE.NOTE] Файл конфигурации SSH расположен по следующему пути: /etc/ssh/sshd_config.

## Устранение неполадок

При использовании командлета **Set-AzureVMExtension ** вы можете получить такую ошибку: "Чтобы настроить расширение доступа к виртуальной машине IaaS, в объекте виртуальной машины должна быть включена функция подготовки гостевого агента". 

Это может произойти, если вы использовали портал управления, чтобы создать виртуальную машину Linux. Причина заключается в том, что для свойства гостевого агента нельзя установить значение True. Чтобы исправить проблему, выполните следующие команды:

	PS C:\> $vm = Get-AzureVM -ServiceName 'MyServiceName' -Name 'MyVMName'

	PS C:\> $vm.GetInstance().ProvisionGuestAgent = $true

#Дополнительные ресурсы
[Расширения и компоненты виртуальных машин Azure] []

[Подключение к виртуальной машине Azure с помощью RDP или SSH] []


<!--Link references-->
[Руководство пользователя агента Linux для Azure]: ../virtual-machines-linux-agent-user-guide
[Установка и настройка Azure PowerShell]: ../install-configure-powershell
[Расширения и компоненты виртуальных машин Azure]: http://msdn.microsoft.com/library/azure/dn606311.aspx
[Подключение к виртуальной машине Azure с помощью RDP или SSH]: http://msdn.microsoft.com/library/azure/dn535788.aspx

<!--HONumber=42-->
