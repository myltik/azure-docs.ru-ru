---
title: "PowerShell. Изменение ценовой категории базы данных SQL Azure | Документация Майкрософт"
description: "Узнайте, как изменить уровень производительности и обслуживания базы данных SQL Azure, а также увеличить и уменьшить масштаб ее ресурсов и цен с помощью PowerShell."
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 6f016c17-b048-4968-b82b-d2dcec954e54
ms.service: sql-database
ms.custom: monitor and tune
ms.devlang: NA
ms.date: 10/12/2016
ms.author: sstein
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 5b1a96149924c6170ea4561b9639f6b85b15ddd3
ms.openlocfilehash: c45d9200dd25d18d45602c377c682875d04c64cb


---
# <a name="change-the-service-tier-and-performance-level-pricing-tier-of-a-sql-database-with-powershell"></a>Изменение уровня обслуживания и уровня производительности (ценовой категории) базы данных SQL с помощью PowerShell
> [!div class="op_single_selector"]
> * [портале Azure](sql-database-scale-up.md)
> * [**PowerShell**](sql-database-scale-up-powershell.md)> 
> 

Уровни службы и уровни производительности описывают функции и ресурсы, доступные для базы данных SQL. Их можно обновлять в соответствии с потребностями приложения. Дополнительные сведения см. в статье [Уровни служб](sql-database-service-tiers.md).

Обратите внимание, что при изменении уровня обслуживания или уровня производительности базы данных создается реплика исходной базы данных с новым уровнем производительности, после чего устанавливаются подключения к реплике. Во время этого процесса данные не теряются, но в течение короткого периода, когда производится переподключение к реплике, соединения с базой данных становятся неактивны, поэтому некоторые текущие транзакции могут откатиться. Этот период может быть разным, но обычно он не превышает 4 секунд, а в более чем 99 % случаев его длительность составляет менее 30 секунд. В очень редких случаях, особенно если в момент отключения соединений выполняется большое количество транзакций, этот период может быть длиннее.  

Длительность всего процесса вертикального масштабирования зависит от размера и уровня службы базы данных до и после изменения. Например, если база данных размером 250 ГБ переводится с уровня службы "Стандартный", на этот уровень или в его пределах, процесс должен завершиться в течение 6 часов. Если уровень производительности базы данных того же размера меняется в пределах уровня службы "Премиум", процесс должен завершиться в течение 3 часов.

* При снижении уровня базы данных ее размер не должен превышать максимально допустимый размер целевого уровня служб. 
* При обновлении базы данных с включенной [георепликацией](sql-database-geo-replication-portal.md) перед обновлением базы данных-источника необходимо сначала обновить базы данных-получатели до требуемого уровня производительности.
* При понижении от уровня обслуживания Premium необходимо сначала завершить все активные отношения георепликации. Чтобы остановить процесс репликации между базой данных-источником и активными базами данных-получателями, выполните действия, описанные в разделе [Восстановление после сбоя](sql-database-disaster-recovery.md) .
* Предложения службы восстановления отличаются для разных уровней служб. При переходе на более низкий уровень можно потерять возможность восстановления на момент времени или получить меньший срок хранения резервных копий. Дополнительные сведения см. в статье [Резервное копирование и восстановление Базы данных SQL Azure](sql-database-business-continuity.md).
* Новые свойства базы данных не применяются до тех пор, пока изменение не завершится.

**Для работы с этой статьей необходимо следующее:**

* Подписка Azure. Если вам требуется подписка Azure, нажмите в верхней части этой страницы кнопку **Бесплатная учетная запись**. Оформив подписку, вернитесь к этой статье.
* База данных SQL Azure. Если у вас нет базы данных SQL, создайте ее в соответствии с инструкциями в следующей статье: [Создание первой базы данных SQL Azure](sql-database-get-started.md).
* Azure PowerShell.

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="change-the-service-tier-and-performance-level-of-your-sql-database"></a>Изменение уровня обслуживания и уровня производительности базы данных SQL
Выполните командлет [Set-AzureRMSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433\(v=azure.300\).aspx) и задайте в параметре **-RequestedServiceObjectiveName** уровень производительности требуемой ценовой категории, например *S0*, *S1*, *S2*, *S3*, *P1*, *P2* и т. д.

```
$ResourceGroupName = "resourceGroupName"

$ServerName = "serverName"
$DatabaseName = "databaseName"

$NewEdition = "Standard"
$NewPricingTier = "S2"

Set-AzureRmSqlDatabase -DatabaseName $DatabaseName -ServerName $ServerName -ResourceGroupName $ResourceGroupName -Edition $NewEdition -RequestedServiceObjectiveName $NewPricingTier
```






## <a name="sample-powershell-script-to-change-the-service-tier-and-performance-level-of-your-sql-database"></a>Пример скрипта PowerShell для изменения уровня обслуживания и уровня производительности базы данных SQL
Замените ```{variables}``` собственными значениями (не включая фигурные скобки).

```
$SubscriptionId = "{4cac86b0-1e56-bbbb-aaaa-000000000000}"

$ResourceGroupName = "{resourceGroup}"
$Location = "{AzureRegion}"

$ServerName = "{server}"
$DatabaseName = "{database}"

$NewEdition = "{Standard}"
$NewPricingTier = "{S2}"

Add-AzureRmAccount
Set-AzureRmContext -SubscriptionId $SubscriptionId

Set-AzureRmSqlDatabase -DatabaseName $DatabaseName -ServerName $ServerName -ResourceGroupName $ResourceGroupName -Edition $NewEdition -RequestedServiceObjectiveName $NewPricingTier
```



## <a name="next-steps"></a>Дальнейшие действия
* [Масштабирование](sql-database-elastic-scale-get-started.md)
* [Подключение к базе данных SQL и выполнение запросов с помощью SSMS](sql-database-connect-query-ssms.md)
* [Экспорт базы данных SQL Azure](sql-database-export-powershell.md)

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Общие сведения о непрерывности бизнес-процессов](sql-database-business-continuity.md)
* [База данных SQL — документация](http://azure.microsoft.com/documentation/services/sql-database/)
* [Командлеты Базы данных SQL Azure](http://msdn.microsoft.com/library/azure/mt574084https://msdn.microsoft.com/library/azure/mt619433\(v=azure.300\).aspx.aspx)




<!--HONumber=Jan17_HO1-->


