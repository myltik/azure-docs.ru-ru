<properties 
   pageTitle="Расписания в службе автоматизации Azure"
   description="Расписания службы автоматизации используются для планирования автоматического выполнения модулей Runbook в службе автоматизации Azure. В этой статье рассматривается создание расписаний."
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/21/2015"
   ms.author="bwren" />

# Расписания в службе автоматизации Azure

Расписания службы автоматизации используются для планирования автоматического выполнения модулей Runbook. Это может быть одна дата и время для однократного запуска модуля Runbook. Или это может быть повторяющееся расписание для запуска Runbook несколько раз. Расписания обычно недоступны из модулей Runbook.

## Командлеты Windows PowerShell

Командлеты, представленные в следующей таблице, используются для создания переменных и управления ими с помощью Windows PowerShell в службе автоматизации Azure. Они входят в состав [модуля Azure PowerShell](../powershell-install-configure.md).

|Командлеты|Описание|
|:---|:---|
|[Get-AzureAutomationSchedule](http://msdn.microsoft.com/library/dn690274.aspx)|Получает расписание.|
|[New-AzureAutomationSchedule](http://msdn.microsoft.com/library/dn690271.aspx)|Создает новое расписание.|
|[Remove-AzureAutomationSchedule](http://msdn.microsoft.com/library/dn690279.aspx)|Удаляет расписание.|
|[Set-AzureAutomationSchedule](http://msdn.microsoft.com/library/dn690270.aspx)|Задает свойства для существующего расписания.|
|[Get-AzureAutomationScheduledRunbook](http://msdn.microsoft.com/library/dn913778.aspx)|Получает расписание модулей Runbook.|
|[Register-AzureAutomationScheduledRunbook](http://msdn.microsoft.com/library/dn690265.aspx)|Связывает Runbook с расписанием.|
|[Unregister-AzureAutomationScheduledRunbook](http://msdn.microsoft.com/library/dn690273.aspx)|Отменяет связь Runbook с расписанием.|

## Создание нового расписания

### Создание нового расписания на портале Azure


1. Из учетной записи автоматизации щелкните **Средства** в верхней части окна.
1. Нажмите кнопку **Добавить параметр** в нижней части окна.
1. Щелкните **Добавить расписание**.
1. Завершите работу мастера и установите флаг, чтобы сохранить новую переменную.

### Создание нового расписания на портале предварительной версии Azure

1. Из учетной записи автоматизации щелкните **Средства**, чтобы открыть колонку **Средства**.
1. Щелкните **Расписания**, чтобы открыть колонку **Расписания**.
1. Щелкните **Добавить расписание** в верхней части области.
1. Заполните форму и нажмите кнопку **Создать** для сохранения нового расписания.

### Создание расписания с помощью Windows PowerShell

Командлет [New-AzureAutomationSchedule](http://msdn.microsoft.com/library/dn690271.aspx) создает новое расписание и задает значение для существующего расписания. Приведенные ниже примеры команд Windows PowerShell позволяют создать новое расписание с именем "Мое ежедневное расписание", которое запускается на следующий день в полдень и работает каждый день в течение года.

	$automationAccountName = "MyAutomationAccount"
	$scheduleName = "My Daily Schedule"
	$startTime = (Get-Date).Date.AddDays(1).AddHours(12)
	$expiryTime = $startTime.AddYears(1)
	
	New-AzureAutomationSchedule –AutomationAccountName $automationAccountName –Name $scheduleName –StartTime $startTime –ExpiryTime $expiryTime –DayInterval 1


## См. также
- [Создание расписания для Runbook в службе автоматизации Azure](automation-scheduling-a-runbook.md)
 

<!---HONumber=62-->