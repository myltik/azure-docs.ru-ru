<properties
	pageTitle="Установка Trend Micro Deep Security на виртуальную машину | Microsoft Azure"
	description="В этой статье описывается установка и настройка защиты Trend Micro на виртуальной машине, созданной с помощью классической модели развертывания в Azure."
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
	ms.date="10/14/2015"
	ms.author="dkshir"/>


# Установка и настройка Trend Micro Deep Security как услуги на ВМ Windows

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Модель диспетчера ресурсов.


В этой статье показывается, как можно установить и настроить Trend Micro Deep Security как услуги на новой или существующей виртуальной машине (ВМ) под управлением Windows Server. Deep Security как услуга включает защиту от вредоносных программ, брандмауэр, систему предотвращения вторжений и мониторинг целостности.

Клиент устанавливается как расширение безопасности с помощью агента виртуальной машины. На новой виртуальной машине агент ВМ будет устанавливаться вместе с агентом Deep Security. На существующей виртуальной машине без агента ВМ необходимо сначала загрузить и установить этот агент. В данной статье рассматриваются обе ситуации.

При наличии действительной подписки от Trend для локального решения можно использовать ее для защиты виртуальных машин Azure. Если у вас еще нет подписки, можно зарегистрироваться для получения пробной подписки. Дополнительную информацию об этом решении см. в [записи блога Trend Micro, посвященной расширению агента виртуальных машин Microsoft Azure для Deep Security](http://go.microsoft.com/fwlink/p/?LinkId=403945).

## Установка агента Deep Security на новой виртуальной машине

[Классический портал Azure](http://manage.windowsazure.com) позволяет установить агент виртуальной машины и расширение безопасности Trend Micro при создании виртуальной машины в режиме **Из коллекции**. Это простой способ добавления защиты от Trend Micro, если создается одна виртуальная машина.

Параметр **Из коллекции** открывает мастер, помогающий установить виртуальную машину. Последняя страница мастера используется для установки агента ВМ и модуля безопасности Trend Micro. Общие указания см. в статье [Создание виртуальной машины под управлением Windows на классическом портале Azure](virtual-machines-windows-tutorial-classic-portal.md). Дойдя до последней страницы мастера, выполните следующие действия.

1.	В разделе **Агент ВМ** установите флажок **Установить агент ВМ**.

2.	В разделе **Модули безопасности** установите флажок **Агент Trend Micro Deep Security**.

	![Установка агента ВМ и агента Deep Security](./media/virtual-machines-install-trend/InstallVMAgentandTrend.png)

3.	Чтобы создать виртуальную машину, поставьте галочку.

## Установка агента Deep Security на существующей виртуальной машине

Для этого потребуются следующие компоненты.

- Модуль Azure PowerShell версии не ниже 0.8.2, установленный на локальном компьютере. Установленную версию Azure PowerShell можно проверить с помощью команды **Get-Module azure | format-table version**. Указания и ссылку на последнюю версию см. в разделе [Установка и настройка Azure PowerShell](../install-configure-powershell.md).

- Агент ВМ, установленный на конечной виртуальной машине.

Сначала убедитесь, что агент ВМ уже установлен. Укажите имя облачной службы и имя виртуальной машины, а затем выполните следующие команды в командной строке Azure PowerShell уровня администратора. Замените все содержимое внутри кавычек, включая символы < and >.

	$CSName = "<cloud service name>"
	$VMName = "<virtual machine name>"
	$vm = Get-AzureVM -ServiceName $CSName -Name $VMName
	write-host $vm.VM.ProvisionGuestAgent

Если вы не знаете имя облачной службы и виртуальной машины, запустите командлет **Get-AzureVM**, чтобы отобразить эти сведения для всех виртуальных машин в вашей текущей подписке.

Если команда **write-host** возвращает значение **True**, агент виртуальных машин установлен. Если она возвращает значение **False**, см. инструкции и ссылку для скачивания во [второй части записи блога Azure, посвященной агенту и расширениям виртуальных машин](http://go.microsoft.com/fwlink/p/?LinkId=403947).

Если агент ВМ установлен, выполните следующие команды.

	$Agent = Get-AzureVMAvailableExtension TrendMicro.DeepSecurity -ExtensionName TrendMicroDSA

	Set-AzureVMExtension -Publisher TrendMicro.DeepSecurity –Version $Agent.Version -ExtensionName TrendMicroDSA -VM $vm | Update-AzureVM

## Дальнейшие действия

После установки агента потребуется несколько минут для его запуска. Затем необходимо будет активировать Deep Security на виртуальной машине, чтобы можно было осуществлять управление с помощью диспетчера Deep Security. Дополнительные указания см. в следующей документации:

- статья Trend об этом решении, [Мгновенное включение облачной защиты для Microsoft Azure](http://go.microsoft.com/fwlink/?LinkId=404101);
- [пример сценария Windows PowerShell](http://go.microsoft.com/fwlink/?LinkId=404100) для настройки виртуальной машины;
- [указания](http://go.microsoft.com/fwlink/?LinkId=404099) для данного примера сценария.

## Дополнительные ресурсы

[Как войти в виртуальную машину под управлением Windows Server]

[Расширения и компоненты виртуальных машин Azure]


<!--Link references-->
[Как войти в виртуальную машину под управлением Windows Server]: virtual-machines-log-on-windows-server.md
[Расширения и компоненты виртуальных машин Azure]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409

<!---HONumber=AcomDC_1203_2015-->