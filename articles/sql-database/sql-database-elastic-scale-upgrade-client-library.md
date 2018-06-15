---
title: Обновление до последней версии клиентской библиотеки эластичной базы данных | Документация Майкрософт
description: Используйте Nuget для обновления клиентской библиотеки эластичной базы данных.
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: 56500a21822f7c7122a20cb676b9ad307de49c87
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34647016"
---
# <a name="upgrade-an-app-to-use-the-latest-elastic-database-client-library"></a>Обновление приложения для использования новой версии клиентской библиотеки эластичной базы данных
Новые версии [клиентской библиотеки эластичной базы данных](sql-database-elastic-database-client-library.md) доступны через интерфейс диспетчера пакетов NuGet в Visual Studio. В обновленных версиях исправлены ошибки и добавлена поддержка новых возможностей клиентской библиотеки.

**Новая версия** : [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

Выполните повторную сборку приложения с использованием новой библиотеки, а также измените существующие метаданные диспетчера сопоставления сегментов, сохраненные в базах данных SQL Azure, чтобы обеспечить поддержку новых функций.

В результате выполнения этих действий в указанном порядке предыдущие версии клиентской библиотеки будут удалены из текущей среды, а объекты метаданных обновлены. Это гарантирует, что данные объекты предыдущей версии не будут повторно созданы после обновления.   

## <a name="upgrade-steps"></a>Действия по обновлению
**1. Обновите свои приложения.** В Visual Studio скачайте последнюю версию клиентской библиотеки, создайте на нее ссылку во всех проектах разработки, использующих библиотеку, а затем выполните повторную сборку и развертывание. 

* В Visual Studio выберите **Инструменты** --> **Диспетчер пакетов NuGet** -->  **Управление пакетами NuGet для решения...**. 
* (Visual Studio 2013.) В левой области выберите **Обновления** и нажмите кнопку **Обновить** возле отобразившегося в окне пакета **Azure SQL Database Elastic Scale Client Library** (База данных SQL Azure — клиентская библиотека эластичного масштабирования).
* (Visual Studio 2015.) В поле фильтра выберите значение **Доступно обновление**. Выберите пакет для обновления и нажмите кнопку **Обновить** .
* (Visual Studio 2017) В верхней части диалогового окна выберите **Обновления**. Выберите пакет для обновления и нажмите кнопку **Обновить** .
* Выполните сборку и развертывание. 

**2. Обновите свои сценарии.** Если для управления сегментами используются сценарии **PowerShell**, [скачайте новую версию библиотеки](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) и скопируйте ее в каталог, из которого выполняются сценарии. 

**3. Обновите свои службы разбиения и объединения.** Если для реорганизации сегментированных данных используется инструмент разбиения и объединения эластичной базы данных, [скачайте и разверните последнюю версию инструмента](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/). Подробные указания по обновлению данной службы можно найти [здесь](sql-database-elastic-scale-overview-split-and-merge.md). 

**4. Обновите базы данных диспетчера карты сегментов**. Обновите метаданные, обслуживающие функцию сопоставления сегментов в базе данных SQL Azure.  Это можно сделать двумя способами — с помощью PowerShell или C#. Ниже описаны оба этих способа.

***Вариант 1. Обновление метаданных с помощью PowerShell***

1. Скачайте последнюю версию служебной программы командной строки для NuGet [здесь](http://nuget.org/nuget.exe) и сохраните ее в папке. 
2. Откройте командную строку, перейдите к указанной папке и выполните следующую команду: `nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Client`
3. Перейдите к вложенной папке, в которой сохранена скачанная новая версия клиентской библиотеки DLL, например: `cd .\Microsoft.Azure.SqlDatabase.ElasticScale.Client.1.0.0\lib\net45`
4. Скачайте сценарий обновления клиента эластичного масштабирования из [Центра сценариев](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-Elastic-6442e6a9)и сохраните его в папке, содержащей библиотеку DLL.
5. Находясь в этой папке, выполните в командной строке команду PowerShell .\upgrade.ps1 и следуйте указаниям на экране.

***Вариант 2. Обновление метаданных с помощью C#***

Создайте приложение Visual Studio, которое открывает диспетчер сопоставления сегментов. Это приложение выполняет итерацию сегментов и обновляет метаданные с помощью методов [UpgradeLocalStore](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradelocalstore.aspx) и [UpgradeGlobalStore](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradeglobalstore.aspx), как в следующем примере. 

    ShardMapManager smm =
       ShardMapManagerFactory.GetSqlShardMapManager
       (connStr, ShardMapManagerLoadPolicy.Lazy); 
    smm.UpgradeGlobalStore(); 

    foreach (ShardLocation loc in
     smm.GetDistinctShardLocations()) 
    {   
       smm.UpgradeLocalStore(loc); 
    } 

Эти методы обновления метаданных могут многократно применяться без каких-либо последствий. Например, если клиент предыдущей версии после обновления случайно создаст какой-либо сегмент, вы сможете повторно обновить все сегменты, чтобы в инфраструктуре использовалась последняя версия метаданных. 

**Примечание.** Имеющиеся на сегодняшний день новые версии клиентской библиотеки поддерживают предыдущие версии метаданных диспетчера сопоставления сегментов, используемые в базе данных SQL Azure, и наоборот.   Однако, чтобы воспользоваться преимуществами некоторых новых функций, реализованных в последней версии клиента, необходимо обновить метаданные.   Обратите внимание, что обновление метаданных не повлияет на пользовательские данные или данные приложения. Оно затронет исключительно объекты, созданные и используемые диспетчером сопоставления сегментов.  Во время выполнения всех этапов обновления, описанных выше, приложения будут работать, как и раньше. 

## <a name="elastic-database-client-version-history"></a>Журнал версий клиента эластичной базы данных
Журнал версий можно просмотреть здесь: [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]:./media/sql-database-elastic-scale-upgrade-client-library/nuget-upgrade.png

