<properties 
	pageTitle="Установка и настройка Symantec Endpoint Protection на виртуальной машине Azure" 
	description="Описывается установка и настройка Symantec Endpoint Protection на виртуальной машине в Azure" 
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
	ms.date="1/26/2015" 
	ms.author="kathydav"/>

#Установка и настройка Symantec Endpoint Protection на виртуальной машине Azure

В этой статье показывается, как можно установить и настроить клиент Symantec Endpoint Protection на новой или существующей виртуальной машине (ВМ) под управлением Windows Server. Это полный клиент, включающий такие службы, как защита от вирусов и шпионских программ, брандмауэр и система предотвращения вторжений. 

Клиент устанавливается как модуль безопасности с помощью агента ВМ. На новой виртуальной машине этот агент будет устанавливаться вместе с клиентом конечной точки. на существующей виртуальной машине без агента необходимо сначала загрузить и установить этот агент. В данной статье рассматриваются обе ситуации.

При наличии действительной подписки от Symantec для локального решения можно использовать ее для защиты виртуальных машин Azure. Если у вас еще нет подписки, можно зарегистрироваться для получения пробной подписки. Дополнительную информацию об этом решении см. в статье [Symantec Endpoint Protection на платформе Microsoft Azure](http://go.microsoft.com/fwlink/p/?LinkId=403942). В этой статье также предоставляются ссылки на сведения о лицензировании и другие инструкции по установке клиента для тех, кто уже является клиентом Symantec.

##Установка Symantec Endpoint Protection на новой виртуальной машине

[Портал управления Azure](http://manage.windowsazure.com) позволяет установить агент виртуальной машины и модуль безопасности Symantec при использовании параметра **Из коллекции** для создания виртуальной машины. Это простейший способ добавления защиты от Symantec, если создается одна виртуальная машина. 

Параметр **Из коллекции** открывает мастер, помогающий настроить виртуальную машину. Последняя страница мастера используется для установки агента ВМ и модуля безопасности Symantec. 

Общие указания см. в статье [Создание виртуальной машины под управлением Windows Server](http://go.microsoft.com/fwlink/p/?LinkId=403943). Дойдя до последней страницы мастера:

1.	В разделе "Агент ВМ" должен быть установлен флажок **Установить агент ВМ**.

2.	В разделе модулей безопасности установите флажок **Symantec Endpoint Protection**.


	![Install the VM Agent and the Endpoint Protection Client](./media/virtual-machines-install-symantec/InstallVMAgentandSymantec.png)

3.	Щелкните флажок в нижней части страницы, чтобы создать виртуальную машину.

## Установка Symantec Endpoint Protection на существующей виртуальной машине

Для этого потребуются следующие компоненты.

- Модуль Azure PowerShell версии не ниже 0.8.2. Указания и ссылку на последнюю версию см. в статье [Установка и настройка Azure PowerShell](http://go.microsoft.com/fwlink/p/?LinkId=320552).  

- Агент ВМ. Указания и ссылку на скачивание см. в записи блога [Агент виртуальной машины и расширения - часть 2](http://go.microsoft.com/fwlink/p/?LinkId=403947).

Чтобы установить модуль безопасности Symantec в существующей виртуальной машине:

1.	Получите имя облачной службы и имя виртуальной машины. Если они вам неизвестны, используйте команду **Get-AzureVM**, чтобы отобразить эту информацию для всех виртуальных машин в текущей подписке. Затем замените значения переменных в кавычках, в том числе символы < и >, и выполните следующие команды:

	<p>`$servicename = "<YourServiceName>"`
<p>`$name = "<YourVmName>"`
<p>`$vm = Get-AzureVM -ServiceName $servicename -Name $name`
<p>`Get-AzureVMAvailableExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection`

2.	В окне отображения команды Get-AzureVMAvailableExtension найдите номер версии в ее свойствах и выполните следующие команды:

	<p>`$ver=<version number from the Version property>`
<p>`Set-AzureVMExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection -Version $ver -VM $vm.VM`
<p>`Update-AzureVM -ServiceName $servicename -Name $name -VM $vm.VM`

Чтобы проверить, установлен ли модуль безопасности Symantec, и убедиться в актуальности его версии, сделайте следующее:

1.	Войдите в виртуальную машину.
2.	Для Windows Server 2008 R2 щелкните **Пуск > Все программы > Symantec Endpoint Protection**. Для Windows Server 2012 на начальном экране введите **Symantec**, а затем щелкните **Symantec Endpoint Protection**.
3.	При необходимости примените обновления в окне состояния.

## Дополнительные ресурсы
[Как войти в виртуальную машину под управлением Windows Server]

[Управление расширениями]

<!--Link references-->
[Как войти в виртуальную машину под управлением Windows Server]: ../virtual-machines-log-on-windows-server/

[Управление расширениями]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409


<!--HONumber=45--> 
