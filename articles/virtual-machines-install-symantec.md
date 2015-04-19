<properties 
	pageTitle="Установка и настройка Symantec Endpoint Protection на виртуальной машине Azure" 
	description="Описывается установка и настройка расширения безопасности Symantec Endpoint Protection на новой или существующей виртуальной машине в Azure." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-multiple" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/24/2015" 
	ms.author="kathydav"/>

#Установка и настройка Symantec Endpoint Protection на виртуальной машине Azure

В этой статье показывается, как можно установить и настроить клиент Symantec Endpoint Protection на новой или существующей виртуальной машине (ВМ) под управлением Windows Server. Это полный клиент, включающий такие службы, как защита от вирусов и шпионских программ, брандмауэр и система предотвращения вторжений. 

Клиент устанавливается как модуль безопасности с помощью агента ВМ. На новой виртуальной машине этот агент будет устанавливаться вместе с клиентом конечной точки. на существующей виртуальной машине без агента необходимо сначала загрузить и установить этот агент. В данной статье рассматриваются обе ситуации.

При наличии действительной подписки от Symantec для локального решения можно использовать ее для защиты виртуальных машин Azure. Если у вас еще нет подписки, можно зарегистрироваться для получения пробной подписки. Дополнительную информацию об этом решении см. в статье [Symantec Endpoint Protection на платформе Microsoft Azure](http://go.microsoft.com/fwlink/p/?LinkId=403942). В этой статье также предоставляются ссылки на сведения о лицензировании и другие инструкции по установке клиента для тех, кто уже является клиентом Symantec.

##Установка Symantec Endpoint Protection на новой виртуальной машине

[Портал управления Azure](http://manage.windowsazure.com) позволяет установить агент виртуальной машины и модуль безопасности Symantec при использовании параметра **Из коллекции** для создания виртуальной машины. Это простейший способ добавления защиты от Symantec, если создается одна виртуальная машина. 

Параметр **Из коллекции** открывает мастер, помогающий настроить виртуальную машину. Последняя страница мастера используется для установки агента ВМ и модуля безопасности Symantec. 

Общие указания см. в статье [Создание виртуальной машины под управлением Windows Server](virtual-machines-windows-tutorial.md). Дойдя до последней страницы мастера:

1.	В разделе "Агент ВМ" должен быть установлен флажок **Установить агент ВМ**.

2.	В разделе модулей безопасности установите флажок **Symantec Endpoint Protection**.


	![Install the VM Agent and the Endpoint Protection Client](./media/virtual-machines-install-symantec/InstallVMAgentandSymantec.png)

3.	Щелкните флажок в нижней части страницы, чтобы создать виртуальную машину.

## Установка Symantec Endpoint Protection на существующей виртуальной машине

Для этого потребуются следующие компоненты.

- Модуль Azure PowerShell версии не ниже 0.8.2. Установленную версию Azure PowerShell можно проверить с помощью команды **Get-Module azure | format-table version**. Указания и ссылку на последнюю версию см. в статье [Установка и настройка Azure PowerShell](install-configure-powershell.md).  

- Агент ВМ. 

Сначала убедитесь, что агент ВМ уже установлен. Укажите имя облачной службы и имя виртуальной машины, а затем выполните следующие команды в командной строке Azure PowerShell уровня администратора. Замените все содержимое внутри кавычек, включая символы < и >.

	$CSName = "<cloud service name>"
	$VMName = "<virtual machine name>"
	$vm = Get-AzureVM -ServiceName $CSName -Name $VMName 
	write-host $vm.VM.ProvisionGuestAgent

Если вы не знаете имя облачной службы и виртуальной машины, запустите командлет **Get-AzureVM**, чтобы отобразить эти сведения для всех виртуальных машин в вашей текущей подписке.

Если команда **write-host** отображает значение **True**, агент ВМ установлен. Если она отображает значение **False**, см. инструкции и ссылку для скачивания в записи блога Azure [Агент ВМ и расширения - часть 2](http://go.microsoft.com/fwlink/p/?LinkId=403947).

Если агент ВМ установлен, выполните следующие команды, чтобы установить агент Symantec Endpoint Protection.

	$Agent = Get-AzureVMAvailableExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection
	Set-AzureVMExtension -Publisher Symantec -Version $Agent.Version -ExtensionName SymantecEndpointProtection -VM $vm | Update-AzureVM

Чтобы проверить, установлен ли модуль безопасности Symantec, и убедиться в актуальности его версии, сделайте следующее:

1.	Войдите в виртуальную машину. Дополнительную информацию см. в разделе [Как войти в виртуальную машину под управлением Windows Server](virtual-machines-log-on-windows-server.md).
2.	Для Windows Server 2008 R2 щелкните **Пуск > Symantec Endpoint Protection**. Для Windows Server 2012 или Windows Server 2012 R2 на начальном экране введите **Symantec**, а затем щелкните **Symantec Endpoint Protection**.
3.	На вкладке **Состояние** окна **Состояние Symantec Endpoint Protection** примените обновления или выполните перезагрузку, если это необходимо.

## Дополнительные ресурсы

[Как войти в виртуальную машину под управлением Windows Server](virtual-machines-log-on-windows-server.md)

[Управление расширениями](https://msdn.microsoft.com/library/dn606311.aspx)


<!--HONumber=47-->
