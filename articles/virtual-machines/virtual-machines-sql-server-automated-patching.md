<properties
   pageTitle="Автоматическая установка исправлений на виртуальные машины SQL Server | Microsoft Azure"
   description="Описывает функцию автоматической установки исправлений для виртуальных машин SQL Server в Azure."
   services="virtual-machines"
   documentationCenter="na"
   authors="rothja"
   manager="jeffreyg"
   editor="monicar"
   tags="azure-resource-manager" />
<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows-sql-server"
   ms.workload="infrastructure-services"
   ms.date="08/05/2015"
   ms.author="jroth" />

# Автоматическая установка исправлений для SQL Server на виртуальных машинах Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]В этой статье описывается процесс управления ресурсом с помощью классической модели развертывания.

При автоматической установке исправлений на виртуальных машинах Azure с SQL Server 2012 или 2014 устанавливается период обслуживания. Установка автоматических обновлений возможна только в этот период обслуживания. Для SQL Server это гарантирует, что системные обновления и связанные с ними перезапуски системы будут происходить в наиболее удобное для базы данных время. Выбор времени зависит от агента SQL Server IaaS.

>[AZURE.NOTE]Для автоматической установки исправлений используется агент SQL Server IaaS. Для установки и настройки агента на конечной виртуальной машине должен быть установлен агент виртуальных машин Azure. На более новых виртуальных машинах этот параметр по умолчанию включен, но если машина используется давно, агент виртуальных машин Azure может отсутствовать. Если вы используете собственный образ виртуальной машины, установите также агент SQL Server IaaS. Дополнительные сведения см. в статье [Агент виртуальных машин и расширения](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/).

## Настройка автоматической установки исправлений на портале

При создании новой виртуальной машины SQL Server можно настроить автоматическую установку исправлений с помощью [предварительной версии портала Azure](http://go.microsoft.com/fwlink/?LinkID=525040&clcid=0x409). На представленном ниже снимке экрана необходимые параметры отображаются в разделе **ДОПОЛНИТЕЛЬНАЯ НАСТРОЙКА** | **АВТОМАТИЧЕСКАЯ УСТАНОВКА ИСПРАВЛЕНИЙ SQL**.

![Автоматическая установка исправлений SQL на портале Azure](./media/virtual-machines-sql-server-automated-patching/IC778484.jpg)

Для уже существующих виртуальных машин SQL Server 2012 или SQL Server 2014 выберите параметры **автоматической установки исправлений** в разделе **Конфигурация** свойств виртуальной машины. В окне **Автоматическая установка исправлений** можно включить эту функцию, установить периодичность обслуживания и время его начала, а также выбрать длительность периода обслуживания. Это показано на следующем снимке экрана.

![Настройка автоматической установки исправлений на портале Azure](./media/virtual-machines-sql-server-automated-patching/IC792132.jpg)

>[AZURE.NOTE]Когда автоматическая установка исправлений активируется впервые, Azure настраивает агент SQL Server IaaS в фоновом режиме. При этом портал не сообщает о том, что выполняется настройка автоматической установки исправлений. Установка и настройка агента занимают несколько минут. После этого новые параметры отобразятся на портале.

## Настройка автоматической установки исправлений с помощью PowerShell

Для настройки автоматической установки исправлений можно также использовать PowerShell.

В следующем примере для настройки автоматической установки исправлений на существующей виртуальной машине SQL Server используется PowerShell. Команда **New-AzureVMSqlServerAutoPatchingConfig** настраивает новый период обслуживания для автоматической установки обновлений.

    $aps = New-AzureVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoPatchingSettings $aps | Update-AzureVM

В представленной ниже таблице показано фактическое воздействие на конечную виртуальную машину Azure на основе данного примера.

|Параметр|Результат|
|---|---|
|**DayOfWeek**|Исправления устанавливаются каждый четверг.|
|**MaintenanceWindowStartingHour**|Установка обновлений начинается в 11:00.|
|**MaintenanceWindowsDuration**|Обновления должны быть установлены в течение 120 минут. С учетом времени начала установка обновлений должна завершаться к 13:00.|
|**PatchCategory**|Единственное возможное значение для этого параметра — Important (Важно).|

Установка и настройка агента SQL Server IaaS занимают несколько минут.

Чтобы отключить автоматическую установку обновлений, выполните тот же скрипт без параметра -Enable в команде New-AzureVMSqlServerAutoPatchingConfig. Как и установка, отключение автоматической установки исправлений занимает несколько минут.

## Отключение и удаление агента SQL Server IaaS

Чтобы отключить агент SQL Server IaaS для автоматической архивации и установки исправлений, используйте следующую команду:

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -Disable | Update-AzureVM

Чтобы удалить агент SQL Server IaaS, используйте следующий синтаксис:

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension –Uninstall | Update-AzureVM

Кроме того, удалить расширение можно с помощью команды **Remove-AzureVMSqlServerExtension**:

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Remove-AzureVMSqlServerExtension | Update-AzureVM

## Совместимость

Следующие продукты совместимы с функциями автоматической установки исправлений агента SQL Server IaaS:

- Windows Server 2012

- Windows Server 2012 R2

- SQL Server 2012

- SQL Server 2014

## Дальнейшие действия

Для виртуальных машин SQL Server Azure существует родственная функция — [автоматическая архивация для SQL Server на виртуальных машинах Azure](virtual-machines-sql-server-automated-backup.md).

Просмотрите и другие [ресурсы по запуску SQL Server на виртуальных машинах Azure](virtual-machines-sql-server-infrastructure-services.md).

<!---HONumber=Sept15_HO4-->