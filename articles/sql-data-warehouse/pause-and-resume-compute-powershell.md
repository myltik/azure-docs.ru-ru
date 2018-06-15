---
title: Краткое руководство. Приостановка и возобновление вычислений в хранилище данных SQL Azure (PowerShell) | Документация Майкрософт
description: Используйте PowerShell, чтобы приостановить вычисления в хранилище данных SQL Azure для снижения расходов. Возобновите вычисления, когда вы готовы к использованию хранилища данных.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: ef341a1528bf759461abfb7cfc6d878fd8a44cb4
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/19/2018
ms.locfileid: "31598903"
---
# <a name="quickstart-pause-and-resume-compute-in-azure-sql-data-warehouse-with-powershell"></a>Краткое руководство. Приостановка и возобновление вычислений в хранилище данных SQL Azure с помощью PowerShell
Используйте PowerShell, чтобы приостановить вычисления в хранилище данных SQL Azure для снижения расходов. [Возобновите работу вычислительных ресурсов](sql-data-warehouse-manage-compute-overview.md), когда будете готовы к использованию хранилища данных.

Если у вас еще нет подписки Azure, создайте [бесплатную](https://azure.microsoft.com/free/) учетную запись Azure, прежде чем начинать работу.

Для работы с этим руководством требуется модуль Azure PowerShell версии не ниже 5.1.1. Чтобы узнать текущую версию, выполните команду ` Get-Module -ListAvailable AzureRM`. Если вам необходимо выполнить установку или обновление, см. статью [об установке модуля Azure PowerShell](/powershell/azure/install-azurerm-ps.md).

## <a name="before-you-begin"></a>Перед началом работы

В этом кратком руководстве предполагается, что уже имеется хранилище данных SQL, работу которого можно приостановить и возобновить. Если его требуется создать, используйте инструкции из раздела [Создание хранилища данных SQL Azure на портале Azure и отправка запросов к этому хранилищу данных](create-data-warehouse-portal.md) для создания хранилища данных **mySampleDataWarehouse**.

## <a name="log-in-to-azure"></a>Вход в Azure

С помощью команды [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) войдите в подписку Azure и следуйте инструкциям на экране.

```powershell
Connect-AzureRmAccount
```

Чтобы узнать, какие подписки вы используете, выполните командлет [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription).

```powershell
Get-AzureRmSubscription
```

Если необходимо использовать не подписку по умолчанию, выполните командлет [Select-AzureRmSubscription](/powershell/module/azurerm.profile/select-azurermsubscription).

```powershell
Select-AzureRmSubscription -SubscriptionName "MySubscription"
```

## <a name="look-up-data-warehouse-information"></a>Поиск сведений о хранилище данных

Найдите имя базы данных, имя сервера и группу ресурсов для хранилища данных, работу которого вы собираетесь приостановить и возобновить.

Выполните следующие действия, чтобы найти сведения о расположении хранилища данных.

1. Войдите на [портале Azure](https://portal.azure.com/).
2. На портале Azure на странице слева щелкните **Базы данных SQL**.
3. На странице **Базы данных SQL** выберите **mySampleDataWarehouse**. Откроется хранилище данных.

    ![Имя сервера и группа ресурсов](media/pause-and-resume-compute-powershell/locate-data-warehouse-information.png)

4. Запишите имя хранилища данных, которое является именем базы данных. Также запишите имя сервера и группу ресурсов. Вы
5.  Они будут использоваться в командах приостановки и возобновления работы.
6. Если вашим сервером является foo.database.windows.net, то в командлетах PowerShell в качестве -ServerName используйте только первую часть имени сервера. На предыдущем рисунке полное имя сервера — newserver-20171113.database.windows.net. Не указывая суффикс, укажите **newserver-20171113** в качестве имени сервера в командлете PowerShell.

## <a name="pause-compute"></a>Приостановка работы вычислительных ресурсов
Для сокращения затрат можно приостанавливать и возобновлять работу вычислительных ресурсов по требованию. Например, если база данных не будет использоваться ночью и по выходным, ее работу можно приостанавливать на это время и возобновлять днем. Когда база данных приостановлена, оплата за вычислительные ресурсы не взимается. Тем не менее плата за хранение по-прежнему будет взиматься.

Чтобы приостановить базу данных, используйте командлет [Suspend-AzureRmSqlDatabase](/powershell/module/azurerm.sql/suspend-azurermsqldatabase.md). В следующем примере приостанавливается работа хранилища данных **mySampleDataWarehouse**, размещенного на сервере **newserver-20171113**. Этот сервер находится в группе ресурсов Azure **myResourceGroup**.


```Powershell
Suspend-AzureRmSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "newserver-20171113" –DatabaseName "mySampleDataWarehouse"
```

В следующем примере, являющемся вариантом предыдущего, база данных извлекается в объект $database. Затем объект передается по конвейеру в командлет [Suspend-AzureRmSqlDatabase](/powershell/module/azurerm.sql/suspend-azurermsqldatabase). Результаты сохраняются в объекте resultDatabase. Последняя команда отображает результаты.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "newserver-20171113" –DatabaseName "mySampleDataWarehouse"
$resultDatabase = $database | Suspend-AzureRmSqlDatabase
$resultDatabase
```


## <a name="resume-compute"></a>Возобновление работы вычислительных ресурсов
Чтобы запустить базу данных, используйте командлет [Resume-AzureRmSqlDatabase](/powershell/module/azurerm.sql/resume-azurermsqldatabase). В приведенном ниже примере запускается база данных mySampleDataWarehouse, размещенная на сервере newserver-20171113. Этот сервер находится в группе ресурсов Azure myResourceGroup.

```Powershell
Resume-AzureRmSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "newserver-20171113" -DatabaseName "mySampleDataWarehouse"
```

В следующем примере, являющемся вариантом предыдущего, база данных извлекается в объект $database. Затем объект передается в командлет [Resume-AzureRmSqlDatabase](/powershell/module/azurerm.sql/resume-azurermsqldatabase.md), и результаты сохраняются в объекте $resultDatabase. Последняя команда отображает результаты.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzureRmSqlDatabase
$resultDatabase
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Плата взимается за единицы хранилища данных и данные, хранящиеся в хранилище данных. Плата за вычислительные ресурсы и ресурсы хранилища взимается отдельно.

- Если вы хотите сохранить данные в хранилище, приостановите вычисления.
- Если вы хотите исключить будущие расходы, то можете удалить хранилище данных.

Выполните следующие действия, чтобы очистить ресурсы по необходимости.

1. Войдите на [портал Azure](https://portal.azure.com) и щелкните хранилище данных.

    ![Очистка ресурсов](media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

1. Чтобы приостановить работу вычислительных ресурсов, нажмите кнопку **Приостановить**. Если работа хранилища данных приостановлена, вы увидите кнопку **Запуск**.  Чтобы возобновить работу вычислительных ресурсов, нажмите кнопку **Запуск**.

2. Чтобы удалить хранилище данных во избежание дальнейших платежей за вычисления или хранение, нажмите кнопку **Удалить**.

3. Чтобы удалить созданный вами сервер SQL Server, щелкните **mynewserver-20171113.database.windows.net**, а затем щелкните **Удалить**.  Будьте внимательны, так как удаление сервера приведет к удалению всех баз данных, назначенных этому серверу.

4. Чтобы удалить группу ресурсов, щелкните **myResourceGroup**, а затем нажмите кнопку **Удалить группу ресурсов**.


## <a name="next-steps"></a>Дополнительная информация
Вы приостановили и возобновили вычисления для хранилища данных. Чтобы узнать больше о хранилище данных SQL Azure, перейдите к руководству по загрузке данных.

> [!div class="nextstepaction"]
>[Загрузка данных в хранилище данных SQL](load-data-from-azure-blob-storage-using-polybase.md)
