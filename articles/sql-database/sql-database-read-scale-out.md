---
title: База данных SQL Azure. Чтение запросов в репликах | Документация Майкрософт
description: База данных SQL Azure позволяет распределять рабочие нагрузки только для чтения, используя емкость реплик только для чтения (горизонтальное масштабирование для чтения).
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: article
ms.date: 04/23/2018
ms.author: sashan
ms.openlocfilehash: d2472867c71aedf35e537a29d3912b9e423de2e2
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32185432"
---
# <a name="use-read-only-replicas-to-load-balance-read-only-query-workloads-preview"></a>Использование реплик только для чтения для распределения рабочих нагрузок запросов только для чтения (предварительная версия)

Функция **Горизонтальное масштабирование для чтения** позволяет распределять рабочие нагрузки службы "База данных SQL Azure" только для чтения, используя емкость реплик только для чтения. 

## <a name="overview-of-read-scale-out"></a>Общие сведения о горизонтальном масштабировании для чтения

Все базы данных уровня "Премиум" ([модель приобретения на основе единиц DTU](sql-database-service-tiers-dtu.md)) или "Критически важный для бизнеса" ([модель приобретения на основе виртуальных ядер (предварительная версия)](sql-database-service-tiers-vcore.md)) автоматически подготавливаются с несколькими репликами AlwaysOn для поддержки соглашения об уровне обслуживания для доступности. Эти реплики имеют тот же уровень производительности, что и реплика для чтения и записи, используемая при обычном подключении к базе данных. Благодаря функции **Горизонтальное масштабирование для чтения** можно распределять рабочие нагрузки базы данных SQL только для чтения за счет емкости реплик только для чтения вместо совместного использования реплики для чтения и записи. Таким образом рабочая нагрузка только для чтения изолируется от главных рабочих нагрузок чтения и записи и не влияет на их производительность. Эта функция предназначена для приложений, в которые включены такие логически разделенные рабочие нагрузки только для чтения, как аналитика, и поэтому она может обеспечить повышение производительности с помощью этой дополнительной емкости без дополнительных затрат.

Чтобы использовать горизонтальное масштабирование для чтения вместе с определенной базой данных, его следует явно включить в момент создания базы данных. Эту функцию также можно включить позже путем изменения ее конфигурации с помощью PowerShell, вызвав командлет [Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) или [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase), или путем использования метода [создания или обновления базы данных](/rest/api/sql/databases/createorupdate) в REST API Azure Resource Manager. 

Когда для базы данных будет включена функция горизонтального масштабирования для чтения, приложения, которые подключаются к ней, будут перенаправлены к реплике для чтения и записи или к реплике только для чтения текущей базы данных в соответствии со свойством `ApplicationIntent`, настроенным в строке подключения приложения. Дополнительные сведения о свойстве `ApplicationIntent` см. в разделе об [указании назначения приложения](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent).

Если масштабирование для чтения отключено или установлено свойство ReadScale на уровне служб, который не поддерживается, то все подключения направляются к реплике для чтения и записи независимо от свойства `ApplicationIntent`.

> [!NOTE]
> В предварительной версии в репликах только для чтения не поддерживаются хранилище запросов и расширенные события.

## <a name="data-consistency"></a>Согласованность данных

Одним из преимуществ AlwaysOn является то, что реплики всегда находятся в состоянии транзакционной согласованности, но в разные моменты времени может возникать небольшая задержка между различными репликами. Горизонтальное масштабирование для чтения поддерживает согласованность на уровне сеанса. Это значит, что если сеанс только для чтения будет повторно подключаться после ошибки подключения, которая была вызвана недоступностью реплики, он может быть перенаправлен к реплике, которая не полностью синхронизирована с текущей репликой для чтения и записи. Аналогичным образом, если приложение записывает данные с помощью сеанса чтения и записи и сразу же считывает их с помощью сеанса только для чтения, возможно, что последние обновления не будут отображены немедленно. Это происходит, так как стадия повтора журнала транзакций реплик является асинхронной.

> [!NOTE]
> Задержки репликации в пределах региона случаются крайне редко.


## <a name="connecting-to-a-read-only-replica"></a>Подключение к реплике только для чтения

При включении для базы данных горизонтального масштабирования для чтения предоставляемый клиентом параметр `ApplicationIntent` в строке подключения определяет, куда направляется подключение: к реплике для записи или к реплике только для чтения. В частности, если значение `ApplicationIntent` — `ReadWrite` (по умолчанию), подключение направляется к реплике базы данных для чтения и записи. Также происходит и без этой функции. Если значение `ApplicationIntent` — `ReadOnly`, подключение направляется к реплике, доступной для чтения.

Например, следующая строка подключения позволяет подключить клиента к реплике только для чтения (замените элементы в угловых скобках правильными значениями для среды и удалите угловые скобки):

```SQL
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

Обе приведенные ниже строки подключения подключают подключить клиента к реплике для чтения и записи (замените элементы в угловых скобках правильными значениями для среды и удалите угловые скобки):

```SQL
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadWrite;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;

Server=tcp:<server>.database.windows.net;Database=<mydatabase>;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

Можно проверить, подключены ли вы к реплике только для чтения, выполнив приведенный ниже запрос. Он возвращает READ_ONLY при подключении к реплике только для чтения.

```SQL
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Updateability')
```

## <a name="enable-and-disable-read-scale-out-using-azure-powershell"></a>Включение и отключение горизонтального масштабирования для чтения с помощью Azure PowerShell

Для управления горизонтальным масштабированием для чтения в Azure PowerShell требуется выпуск Azure PowerShell за декабрь 2016 года или более поздней версии. Последнюю версию Azure PowerShell см. [здесь](https://docs.microsoft.com/powershell/azure/install-azurerm-ps).

Чтобы включить или отключить горизонтальное масштабирование для чтения в Azure PowerShell, вызовите командлет [Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) и задайте нужное значение (`Enabled` или `Disabled`) для параметра `-ReadScale`. Кроме того, чтобы создать базу данных с поддержкой функции горизонтального масштабирования для чтения, можно использовать командлет [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase).

Например, чтобы включить для имеющейся базы данных функцию горизонтального масштабирования для чтения (замените элементы в угловых скобках правильными значениями для среды и удалите угловые скобки):

```powershell
Set-AzureRmSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Enabled
```

Чтобы отключить для имеющейся базы данных функцию горизонтального масштабирования для чтения (замените элементы в угловых скобках правильными значениями для среды и удалите угловые скобки):

```powershell
Set-AzureRmSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Disabled
```

Чтобы создать базу данных с поддержкой функции горизонтального масштабирования для чтения (замените элементы в угловых скобках правильными значениями для среды и удалите угловые скобки):

```powershell
New-AzureRmSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Enabled -Edition Premium
```

## <a name="enabling-and-disabling-read-scale-out-using-the-azure-sql-database-rest-api"></a>Включение и отключение функции горизонтального масштабирования для чтения с помощью REST API Базы данных SQL Azure

Чтобы создать базу данных с поддержкой функции горизонтального масштабирования для чтения, включить или отключить эту функцию для имеющейся базы данных, создайте или обновите соответствующую сущность базы данных, задав для свойства `readScale` значение `Enabled` или `Disabled`, как в приведенном ниже примере запроса.

```rest
Method: PUT
URL: https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{GroupName}/providers/Microsoft.Sql/servers/{ServerName}/databases/{DatabaseName}?api-version= 2014-04-01-preview
Body:
{
   "properties":
   {
      "readScale":"Enabled"
   }
} 
```

Дополнительные сведения см. в статье о [создании или обновлении базы данных](/rest/api/sql/databases/createorupdate).

## <a name="next-steps"></a>Дополнительная информация

- Дополнительные сведения об использовании PowerShell для установки функции горизонтального масштабирования для чтения см. в разделах о командлетах [Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) или [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase).
- Дополнительные сведения об использовании интерфейса REST API для включения функции горизонтального масштабирования для чтения см. в [этой статье](/rest/api/sql/databases/createorupdate).
