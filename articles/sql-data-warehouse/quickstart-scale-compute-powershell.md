---
title: Краткое руководство. Горизонтальное масштабирование вычислительных ресурсов в хранилище данных SQL Azure с помощью PowerShell | Документация Майкрософт
description: Масштабируйте вычислительные ресурсы в хранилище данных SQL Azure в PowerShell. Масштабируйте вычислительные ресурсы, чтобы повысить производительность, или выполняйте обратное масштабирование, чтобы сократить расходы.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.component: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 0718365153390f525b22ef07559a822c777c2ff4
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32187918"
---
# <a name="quickstart-scale-compute-in-azure-sql-data-warehouse-in-powershell"></a>Краткое руководство. Масштабирование вычислительных ресурсов в хранилище данных SQL Azure в PowerShell

Масштабируйте вычислительные ресурсы в хранилище данных SQL Azure в PowerShell. [Горизонтально масштабируйте вычислительные ресурсы](sql-data-warehouse-manage-compute-overview.md), чтобы повысить производительность, или уменьшайте их масштаб, чтобы сократить затраты.

Если у вас еще нет подписки Azure, создайте [бесплатную](https://azure.microsoft.com/free/) учетную запись Azure, прежде чем начинать работу.

Для работы с этим руководством требуется модуль Azure PowerShell версии не ниже 5.1.1. Чтобы узнать текущую версию, выполните команду `Get-Module -ListAvailable AzureRM`. Если вам необходимо выполнить установку или обновление, см. статью [об установке модуля Azure PowerShell](/powershell/azure/install-azurerm-ps.md).

## <a name="before-you-begin"></a>Перед началом работы

В этом кратком руководстве предполагается, что вы уже создали хранилище данных SQL, которое можно масштабировать. Если его требуется создать, используйте инструкции из раздела [Создание хранилища данных SQL Azure на портале Azure и отправка запросов к этому хранилищу данных](create-data-warehouse-portal.md), чтобы создать хранилище данных **mySampleDataWarehouse**.

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
2. На портале Azure на странице слева щелкните **Хранилища данных SQL**.
3. На странице **Хранилища данных SQL** выберите **mySampleDataWarehouse**. Откроется хранилище данных.

    ![Имя сервера и группа ресурсов](media/pause-and-resume-compute-powershell/locate-data-warehouse-information.png)

4. Запишите имя хранилища данных, которое будет использоваться в качестве имени базы данных. Помните, что хранилище данных — это один из типов базы данных. Также запишите имя сервера и группу ресурсов. Они будут использоваться в командах приостановки и возобновления работы.
5. Если вашим сервером является foo.database.windows.net, то в командлетах PowerShell в качестве -ServerName используйте только первую часть имени сервера. На предыдущем рисунке полное имя сервера — newserver-20171113.database.windows.net. Мы используем **newserver-20180430** в качестве имени сервера в командлете PowerShell.

## <a name="scale-compute"></a>Масштабирование вычислительных ресурсов

В хранилище данных SQL вы можете увеличивать и уменьшать объем вычислительных ресурсов, изменяя число единиц использования хранилища данных (DWU). В статье [Создание хранилища данных SQL Azure на портале Azure и отправка запросов к этому хранилищу данных](create-data-warehouse-portal.md) мы создали хранилище **mySampleDataWarehouse** и инициализировали его со значением 400 DWU. Ниже описаны шаги по изменению числа единиц DWU для **mySampleDataWarehouse**.

Чтобы изменить число единиц DWU, используйте командлет PowerShell [Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase). В следующем примере для параметра числа единиц задано значение DW300 для базы данных **mySampleDataWarehouse**, размещенной в группе ресурсов **myResourceGroup** на сервере **mynewserver-20180430**.

```Powershell
Set-AzureRmSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300"
```

## <a name="check-data-warehouse-state"></a>Проверка состояния хранилища данных

Чтобы просмотреть текущее состояние хранилища данных, используйте командлет PowerShell [Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase). Он позволяет вернуть состояние базы данных **mySampleDataWarehouse**, размещенной в группе ресурсов **myResourceGroup** на сервере **mynewserver-20180430.database.windows.net**.

```powershell
$database = Get-AzureRmSqlDatabase -ResourceGroupName myResourceGroup -ServerName mynewserver-20171113 -DatabaseName mySampleDataWarehouse
$database
```

Результат будет примерно таким:

```powershell
ResourceGroupName             : myResourceGroup
ServerName                    : mynewserver-20171113
DatabaseName                  : mySampleDataWarehouse
Location                      : North Europe
DatabaseId                    : 34d2ffb8-b70a-40b2-b4f9-b0a39833c974
Edition                       : DataWarehouse
CollationName                 : SQL_Latin1_General_CP1_CI_AS
CatalogCollation              :
MaxSizeBytes                  : 263882790666240
Status                        : Online
CreationDate                  : 11/20/2017 9:18:12 PM
CurrentServiceObjectiveId     : 284f1aff-fee7-4d3b-a211-5b8ebdd28fea
CurrentServiceObjectiveName   : DW300
RequestedServiceObjectiveId   : 284f1aff-fee7-4d3b-a211-5b8ebdd28fea
RequestedServiceObjectiveName :
ElasticPoolName               :
EarliestRestoreDate           :
Tags                          :
ResourceId                    : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/
                                resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/mynewserver-20171113/databases/mySampleDataWarehouse
CreateMode                    :
ReadScale                     : Disabled
ZoneRedundant                 : False
```

Вы увидите **состояние** базы данных в выходных данных. Как видите, в этом случае база данных находится в сети (Online).  При выполнении этой команды должно отобразиться одно из следующих значений состояния: "Online" (В сети), "Pausing" (Приостановка), "Resuming" (Возобновление), "Scaling" (Масштабирование) или "Paused" (Приостановлено).

Чтобы просмотреть само состояние службы, используйте следующую команду:

```powershell
$database | Select-Object DatabaseName,Status
```

## <a name="next-steps"></a>Дополнительная информация
Вы узнали, как масштабировать вычислительные ресурсы для хранилища данных. Чтобы узнать больше о хранилище данных SQL Azure, перейдите к руководству по загрузке данных.

> [!div class="nextstepaction"]
>[Загрузка данных в хранилище данных SQL](load-data-from-azure-blob-storage-using-polybase.md)
