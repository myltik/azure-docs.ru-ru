<properties
	pageTitle="Автоматическая установка исправлений для виртуальных машин SQL Server (Resource Manager) | Microsoft Azure"
	description="Описывает функцию автоматической установки исправлений для виртуальных машин SQL Server в Azure для модели Resource Manager."
	services="virtual-machines-windows"
	documentationCenter="na"
	authors="rothja"
	manager="jhubbard"
	editor=""
	tags="azure-resource-manager"/>
<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="05/18/2016"
	ms.author="jroth" />

# Автоматическая установка исправлений SQL Server на виртуальных машинах Azure (Resource Manager)

> [AZURE.SELECTOR]
- [Диспетчер ресурсов](virtual-machines-windows-sql-automated-patching.md)
- [Классический](virtual-machines-windows-classic-sql-automated-patching.md)

При автоматической установке исправлений на виртуальных машинах Azure с SQL Server задается период обслуживания. Установка автоматических обновлений возможна только в этот период обслуживания. Для SQL Server это гарантирует, что системные обновления и связанные с ними перезапуски системы будут происходить в наиболее удобное для базы данных время. Автоматическая установка исправлений зависит от [Расширения агента IaaS для SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] Классическая модель развертывания. Версию этой статьи для классической модели развертывания см. в разделе [Автоматическая установка исправлений SQL Server на виртуальных машинах Azure (классическая модель)](virtual-machines-windows-classic-sql-automated-patching.md).

## Предварительные требования

Для использования автоматической установки исправлений необходимо выполнить следующие предварительные требования.

**Операционная система**

- Windows Server 2012
- Windows Server 2012 R2

**Версия SQL Server**

- SQL Server 2012
- SQL Server 2014
- SQL Server 2016

**Azure PowerShell**

- [Установите последние команды Azure PowerShell](../powershell-install-configure.md), если планируете настраивать автоматические исправления с помощью PowerShell.

>[AZURE.NOTE] Автоматическая установка исправлений зависит от расширения агента IaaS для SQL Server. В текущей коллекции образов виртуальных машин SQL это расширение присутствует по умолчанию. Дополнительные сведения см. в статье [Расширение агента IaaS для SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

## Параметры

В приведенной ниже таблице описаны параметры для настройки автоматической установки исправлений. Фактическая процедура настройки может варьироваться в зависимости от того, используете вы портал Azure или команды Azure Windows PowerShell.

|Настройка|Возможные значения|Описание|
|---|---|---|
|**Автоматическое исправление**|Включено/отключено (отключено)|Включает или отключает автоматическую установку исправлений для виртуальной машины Azure.|
|**Расписание обслуживания**|Каждый день, понедельник, вторник, среда, четверг, пятница, суббота, воскресенье|Расписание для скачивания и установки обновлений Windows, SQL Server и обновлений Майкрософт для виртуальной машины.|
|**Время начала обслуживания**|0–24|Локальное время начала обновления виртуальной машины.|
|**Длительность периода обслуживания**|30–180|Допустимое количество минут для скачивания и установки обновлений.|
|**Категория исправления**|Важно!|Категория обновлений, которые будут скачаны и установлены.|

## Настройка на портале
Для настройки автоматизированной установки исправлений во время подготовки виртуальных машин или для существующих виртуальных машин можно использовать портал Azure.

### Новые виртуальные машины
При создании новой виртуальной машины SQL Server с моделью развертывания с помощью Resource Manager настройте автоматизированную установку исправлений, используя портал Azure.

В колонке **Параметры SQL Server** выберите **Автоматическая установка исправлений**. Колонка **Автоматическая установка исправлений SQL** показана на следующем снимке экрана портала Azure.

![Автоматизированная установка исправлений SQL на портале Azure](./media/virtual-machines-windows-sql-automated-patching/azure-sql-arm-patching.png)

Для контекста обратитесь к полному описанию в разделе [Подготовка виртуальной машины SQL Server в Azure](virtual-machines-windows-portal-sql-server-provision.md).

### Существующие виртуальные машины
Выберите существующую виртуальную машину SQL Server. Затем в колонке **Параметры** выберите раздел **Конфигурация SQL Server**.

![Автоматизированная установка исправлений SQL для существующих виртуальных машин](./media/virtual-machines-windows-sql-automated-patching/azure-sql-rm-patching-existing-vms.png)

В колонке **Конфигурация SQL Server** нажмите кнопку **Изменить** в разделе "Автоматизированное исправление".

![Настройка автоматизированной установки исправлений SQL для существующих виртуальных машин](./media/virtual-machines-windows-sql-automated-patching/azure-sql-rm-patching-configuration.png)

По завершении нажмите кнопку **ОК** в нижней части колонки **Конфигурация SQL Server**, чтобы сохранить изменения.

Если автоматизированная установка исправлений включается впервые, Azure настроит агент IaaS SQL Server в фоновом режиме. В течение этого времени портал Azure может не отображать информацию о том, что выполняется настройка автоматической установки исправлений. Установка и настройка агента занимают несколько минут. После этого новые параметры отобразятся на портале Azure.

>[AZURE.NOTE] Можно также настроить автоматизированную установку исправлений с помощью шаблона. Чтобы получить дополнительные сведения, ознакомьтесь с [шаблоном быстрого запуска Azure для автоматизированной установки исправлений](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-autopatching-update).

## Настройка с помощью PowerShell

После подготовки виртуальной машины SQL используйте PowerShell для настройки автоматической установки исправлений.

В следующем примере для настройки автоматической установки исправлений на существующей виртуальной машине SQL Server используется PowerShell. Команда **AzureRM.Compute\\New-AzureVMSqlServerAutoPatchingConfig** настраивает новый период обслуживания для автоматизированного обновления.

	$vmname = "vmname"
	$resourcegroupname = "resourcegroupname"
	$aps = AzureRM.Compute\New-AzureVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"

    Set-AzureRmVMSqlServerExtension -AutoPatchingSettings $aps -VMName $vmname -ResourceGroupName $resourcegroupname

В представленной ниже таблице показано фактическое воздействие на конечную виртуальную машину Azure на основе данного примера.

|Параметр|Результат|
|---|---|
|**DayOfWeek**|Исправления устанавливаются каждый четверг.|
|**MaintenanceWindowStartingHour**|Установка обновлений начинается в 11:00.|
|**MaintenanceWindowsDuration**|Обновления должны быть установлены в течение 120 минут. С учетом времени начала установка обновлений должна завершаться к 13:00.|
|**PatchCategory**|Единственное возможное значение для этого параметра — Important (Важно).|

Установка и настройка агента SQL Server IaaS занимают несколько минут.

Чтобы отключить автоматизированную установку исправлений, выполните тот же сценарий без параметра **-Enable** в команде **AzureRM.Compute\\New-AzureVMSqlServerAutoPatchingConfig**. Отсутствие параметра **-Enable** означает, что функцию нужно отключить.

## Дальнейшие действия

Сведения о других доступных задачах автоматизации см. в разделе [Расширение агента IaaS для SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

Дополнительные сведения о запуске SQL Server на виртуальных машинах Azure см. в [обзоре использования SQL Server на виртуальных машинах Azure](virtual-machines-windows-sql-server-iaas-overview.md).

<!----HONumber=AcomDC_0720_2016--->