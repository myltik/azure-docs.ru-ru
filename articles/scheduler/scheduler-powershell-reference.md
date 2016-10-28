<properties
 pageTitle="Справочник по командлетам PowerShell планировщика"
 description="Справочник по командлетам PowerShell планировщика"
 services="scheduler"
 documentationCenter=".NET"
 authors="krisragh"
 manager="dwrede"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.date="08/18/2016"
 ms.author="krisragh"/>

# Справочник по командлетам PowerShell планировщика

В следующей таблице приведено описание и ссылки на справочную страницу для каждого из основных командлетов в планировщике Azure.

Чтобы установить решение Azure PowerShell и связать его с подпиской Azure, см. статью [Установка и настройка Azure PowerShell](../powershell-install-configure.md).

Дополнительные сведения о [командлетах Azure Resource Manager](https://msdn.microsoft.com/library/mt125356(v=azure.200).aspx) см. в статье [Использование Azure PowerShell с диспетчером ресурсов Azure](../powershell-azure-resource-manager.md).

|Командлет|Описание командлета|
|---|---|
[Disable-AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490133(v=azure.200).aspx) |Отключает коллекцию заданий. 
[Enable-AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490135(v=azure.200).aspx) |Включает коллекцию заданий.
[Get-AzureRmSchedulerJob](https://msdn.microsoft.com/library/mt490125(v=azure.200).aspx) |Получает задания планировщика.
[Get-AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490132(v=azure.200).aspx) |Получает коллекции заданий.
[Get-AzureRmSchedulerJobHistory](https://msdn.microsoft.com/library/mt490126(v=azure.200).aspx) |Получает журнал заданий.
[New-AzureRmSchedulerHttpJob](https://msdn.microsoft.com/library/mt490136(v=azure.200).aspx) |Создает задание HTTP.
[New-AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490141(v=azure.200).aspx) |Создает коллекцию заданий.
[New-AzureRmSchedulerServiceBusQueueJob](https://msdn.microsoft.com/library/mt490134(v=azure.200).aspx) |Создает задание очереди служебной шины.
[New-AzureRmSchedulerServiceBusTopicJob](https://msdn.microsoft.com/library/mt490142(v=azure.200).aspx) |Создает задание раздела служебной шины.
[New-AzureRmSchedulerStorageQueueJob](https://msdn.microsoft.com/library/mt490127(v=azure.200).aspx) |Создает задание очереди хранилища. 
[Remove-AzureRmSchedulerJob](https://msdn.microsoft.com/library/mt490140(v=azure.200).aspx) |Удаляет задание планировщика.  
[Remove-AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490131(v=azure.200).aspx) |Удаляет коллекцию заданий. 
[Set-AzureRmSchedulerHttpJob](https://msdn.microsoft.com/library/mt490130(v=azure.200).aspx) |Изменяет задание HTTP планировщика.
[Set-AzureRmSchedulerJobCollection](https://msdn.microsoft.com/library/mt490129(v=azure.200).aspx) |Изменяет коллекцию заданий. 
[Set-AzureRmSchedulerServiceBusQueueJob](https://msdn.microsoft.com/library/mt490143(v=azure.200).aspx) |Изменяет задание очереди служебной шины.  
[Set-AzureRmSchedulerServiceBusTopicJob](https://msdn.microsoft.com/library/mt490137(v=azure.200).aspx) |Изменяет задание раздела служебной шины. 
[Set-AzureRmSchedulerStorageQueueJob](https://msdn.microsoft.com/library/mt490128(v=azure.200).aspx) |Изменяет задание очереди хранилища.   

Для получения более подробных сведений можно выполнить следующие командлеты:

```
Get-Help <cmdlet name> -Detailed
```
```
Get-Help <cmdlet name> -Examples
```
```
Get-Help <cmdlet name> -Full
```

## См. также


 [Что такое планировщик?](scheduler-intro.md)

 [Основные понятия, терминология и иерархия сущностей планировщика Azure](scheduler-concepts-terms.md)

 [Приступая к работе с планировщиком Azure на портале Azure](scheduler-get-started-portal.md)

 [Планы и выставление счетов в планировщике Azure](scheduler-plans-billing.md)

 [Справочник по API REST планировщика Azure](https://msdn.microsoft.com/library/mt629143)

 [Высокая доступность и надежность планировщика Azure](scheduler-high-availability-reliability.md)

 [Ограничения, значения по умолчанию и коды ошибок планировщика Azure](scheduler-limits-defaults-errors.md)

 [Исходящая аутентификация планировщика Azure](scheduler-outbound-authentication.md)

<!---HONumber=AcomDC_0914_2016-->