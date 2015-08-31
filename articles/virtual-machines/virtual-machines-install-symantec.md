<properties
	pageTitle="Установка и настройка Symantec Endpoint Protection на виртуальной машине Azure"
	description="Описывается установка и настройка расширения безопасности Symantec Endpoint Protection на новой или существующей виртуальной машине в Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/14/2015"
	ms.author="dkshir"/>

# Установка и настройка Symantec Endpoint Protection на виртуальной машине Azure

В этой статье показывается, как можно установить и настроить клиент Symantec Endpoint Protection на новой или существующей виртуальной машине (ВМ) под управлением Windows Server. Это полный клиент, включающий такие службы, как защита от вирусов и шпионских программ, брандмауэр и система предотвращения вторжений. Обратите внимание, что в этой статье говорится о виртуальных машинах, созданных с использованием классической модели развертывания.

Клиент устанавливается как модуль безопасности с помощью агента ВМ. На новой виртуальной машине этот агент будет устанавливаться вместе с клиентом конечной точки. на существующей виртуальной машине без агента необходимо сначала загрузить и установить этот агент. В данной статье рассматриваются обе ситуации.

При наличии действительной подписки от Symantec для локального решения можно использовать ее для защиты виртуальных машин Azure. Если у вас еще нет подписки, можно зарегистрироваться для получения пробной подписки. Дополнительные сведения об этом решении см. в статье [Symantec Endpoint Protection на платформе Microsoft Azure][Symantec]. На этой странице также приведены ссылки на сведения о лицензировании и инструкции по установке клиента для тех, кто уже использует Symantec.

## Установка Symantec Endpoint Protection на новой виртуальной машине

[Портал управления Azure][Portal] позволяет установить агент ВМ и модуль безопасности Symantec при использовании параметра **Из коллекции** для создания виртуальной машины. Это простейший способ добавления защиты от Symantec, если создается одна виртуальная машина.

Параметр **Из коллекции** открывает мастер, помогающий установить виртуальную машину. Последняя страница мастера используется для установки агента ВМ и модуля безопасности Symantec.

Общие инструкции см. в статье [Создание виртуальной машины под управлением Windows Server][Create]. Дойдя до последней страницы мастера:

1.	В разделе "Агент ВМ" должен быть установлен флажок **Установить агент ВМ**.

2.	В разделе модулей безопасности установите флажок **Symantec Endpoint Protection**.


	![Установка агента ВМ и клиента Endpoint Protection](./media/virtual-machines-install-symantec/InstallVMAgentandSymantec.png)

3.	Щелкните флажок в нижней части страницы, чтобы создать виртуальную машину.

## Установка Symantec Endpoint Protection на существующей виртуальной машине

Для этого потребуются следующие компоненты.

- Модуль Azure PowerShell версии не ниже 0.8.2, установленный на рабочем компьютере. Номер установленной версии Azure PowerShell можно уточнить с помощью команды **Get-Module azure | format-table version** Инструкции и ссылку на последнюю версию см. в статье [Установка и настройка Azure PowerShell][PS]. Войдите в подписку Azure.

- Агент VM запущен на виртуальной машине Azure.

Сначала убедитесь, что агент VM уже установлен на виртуальной машине. Укажите имя облачной службы и имя виртуальной машины, а затем выполните следующие команды в командной строке Azure PowerShell уровня администратора. Замените все содержимое внутри кавычек, включая символы < and >.

> [AZURE.TIP]Если вы не знаете имена облачной службы и виртуальной машины, выполните командлет **Get-AzureVM** и просмотрите список имен всех виртуальных машин в вашей текущей подписке.

	$CSName = "<cloud service name>"
	$VMName = "<virtual machine name>"
	$vm = Get-AzureVM -ServiceName $CSName -Name $VMName
	write-host $vm.VM.ProvisionGuestAgent

Если команда **write-host** отображает значение **True**, агент виртуальных машин установлен. Если она отображает значение **False**, см. инструкции и ссылку для скачивания во [второй части записи блога Azure, посвященной агенту и расширениям виртуальных машин][Agent].

Если агент ВМ установлен, выполните следующие команды, чтобы установить агент Symantec Endpoint Protection.

	$Agent = Get-AzureVMAvailableExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection

	Set-AzureVMExtension -Publisher Symantec –Version $Agent.Version -ExtensionName SymantecEndpointProtection -VM $vm | Update-AzureVM

Чтобы проверить, установлен ли модуль безопасности Symantec, и убедиться в актуальности его версии, сделайте следующее:

1.	Войдите в виртуальную машину. Указания см. в разделе [Как войти в виртуальную машину под управлением Windows Server][Logon].
2.	Для Windows Server 2008 R2: щелкните **Пуск > Symantec Endpoint Protection**. Для Windows Server 2012 или Windows Server 2012 R2: на начальном экране введите **Symantec**, а затем щелкните **Symantec Endpoint Protection**.
3.	На вкладке **Состояние** окна **Состояние Symantec Endpoint Protection** примените обновления или выполните перезагрузку, если это необходимо.

## Дополнительные ресурсы

[Как войти в виртуальную машину под управлением Windows Server][Logon]

[Расширения и компоненты виртуальных машин Azure][Ext]


<!--Link references-->
[Symantec]: http://go.microsoft.com/fwlink/p/?LinkId=403942

[Portal]: http://manage.windowsazure.com

[Create]: virtual-machines-windows-tutorial.md

[PS]: ../powershell-install-configure.md

[Agent]: http://go.microsoft.com/fwlink/p/?LinkId=403947

[Logon]: virtual-machines-log-on-windows-server.md

[Ext]: http://go.microsoft.com/fwlink/p/?linkid=390493

<!---HONumber=August15_HO8-->