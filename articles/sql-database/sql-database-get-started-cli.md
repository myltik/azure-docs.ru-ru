---
title: "Azure CLI: создание базы данных SQL | Документация Майкрософт"
description: "Узнайте, как создать логический сервер базы данных SQL, правила брандмауэра на уровне сервера и базы данных с помощью Azure CLI."
keywords: "руководство по базам данных SQL, создание базы данных SQL"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: quick start create
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: hero-article
ms.date: 04/17/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: fb0cd3898703b9acf54d7ff70774bd090d39f0e5
ms.lasthandoff: 04/22/2017

---

# <a name="create-a-single-azure-sql-database-using-the-azure-cli"></a>Создание отдельной базы данных SQL Azure с помощью Azure CLI

Azure CLI используется для создания ресурсов Azure и управления ими из командной строки или с помощью скриптов. В этом руководстве подробно объясняется, как с помощью Azure CLI развернуть базу данных SQL Azure в [группе ресурсов Azure](../azure-resource-manager/resource-group-overview.md) на [логическом сервере базы данных Azure SQL](sql-database-features.md).

Для работы с этим кратким руководством нужно установить последнюю версию [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli). 

Если у вас еще нет подписки Azure, создайте [бесплатную](https://azure.microsoft.com/free/) учетную запись Azure, прежде чем начинать работу.

## <a name="log-in-to-azure"></a>Вход в Azure

Войдите в подписку Azure с помощью команды [az login](/cli/azure/#login) и следуйте инструкциям на экране.

```azurecli
az login
```

## <a name="define-variables"></a>Определение переменных

Определите переменные для использования в скриптах этого руководства.

```azurecli
# The data center and resource name for your resources
export resourcegroupname = myResourceGroup
export location = westeurope
# The logical server name: Use a random value or replace with your own value (do not capitalize)
export servername = server-$RANDOM
# Set an admin login and password for your database
export adminlogin = ServerAdmin
export password = ChangeYourAdminPassword1
# The ip address range that you want to allow to access your DB
export startip = "0.0.0.0"
export endip = "0.0.0.1"
# The database name
export databasename = mySampleDatabase
```

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте [группу ресурсов Azure](../azure-resource-manager/resource-group-overview.md) с помощью команды [az group create](/cli/azure/group#create). Группа ресурсов — это логический контейнер, в котором ресурсы Azure развертываются и администрируются как группа. В следующем примере создается группа ресурсов с именем `myResourceGroup` в расположении `westeurope`.

```azurecli
az group create --name $resourcegroupname --location $location
```
## <a name="create-a-logical-server"></a>Создание логического сервера

Создайте [логический сервер базы данных SQL Azure ](sql-database-features.md) с помощью команды [az sql server create](/cli/azure/sql/server#create). Логический сервер содержит группу баз данных, которыми можно управлять как группой. В примере ниже показано создание сервера со случайным именем в группе ресурсов с именем администратора `ServerAdmin` и паролем `ChangeYourAdminPassword1`. Замените эти предопределенные значения по своему усмотрению.

```azurecli
az sql server create --name $servername --resource-group $resourcegroupname --location $location \
    --admin-user $adminlogin --admin-password $password
```

## <a name="configure-a-server-firewall-rule"></a>Настройка правил брандмауэра сервера

Создайте [правило брандмауэра на уровне сервера базы данных Azure SQL](sql-database-firewall-configure.md) с помощью команды [az sql server firewall create](/cli/azure/sql/server/firewall-rule#create). Правило брандмауэра на уровне сервера позволяет внешним приложениям, таким как SQL Server Management Studio или программе sqlcmd, подключаться к базе данных SQL через брандмауэр службы базы данных SQL. В следующем примере брандмауэр открыт только для других ресурсов Azure. Чтобы включить возможность внешнего подключения, измените IP-адрес на соответствующий адрес своей среды. Чтобы открыть все IP-адреса, используйте 0.0.0.0 как начальный IP-адрес, а 255.255.255.255 — как конечный.  

```azurecli
az sql server firewall-rule create --resource-group $resourcegroupname --server $servername \
    -n AllowYourIp --start-ip-address $startip --end-ip-address $endip
```

> [!NOTE]
> База данных SQL обменивается данными через порт 1433. Если вы пытаетесь подключиться из корпоративной сети, исходящий трафик через порт 1433 может быть запрещен сетевым брандмауэром. В таком случае вы не сможете подключиться к серверу базы данных SQL Azure. Для этого ваш ИТ-отдел должен открыть порт 1433.
>

## <a name="create-a-database-in-the-server-with-sample-data"></a>Создание базы данных на сервере с образцами данных

Создайте на сервере базу данных с [уровнем производительности S0](sql-database-service-tiers.md) с помощью команды [az sql db create](/cli/azure/sql/db#create). В следующем примере создается база данных с именем `mySampleDatabase`, в которую загружается образец данных AdventureWorksLT. При необходимости замените эти предопределенные значения (другие краткие руководства в этой коллекции созданы на основе этого документа).

```azurecli
az sql db create --resource-group $resourcegroupname --server $servername \
    --name $databasename --sample-name AdventureWorksLT --service-objective S0
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Другие краткие руководства в этой коллекции созданы на основе этого документа. 

> [!TIP]
> Если вы планируете продолжать работу с этими краткими руководствами, не удаляйте созданные ресурсы. Если вы не планируете продолжать работу, удалите все созданные ресурсы, выполнив на портале Azure следующие действия.
>

```azurecli
az group delete --name $resourcegroupname
```

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда у вас есть база данных, вы можете подключиться и создать запрос, используя привычные средства. См. дополнительные сведения о доступных средствах:

- [SQL Server Management Studio](sql-database-connect-query-ssms.md)
- [Visual Studio Code](sql-database-connect-query-vscode.md)
- [.NET](sql-database-connect-query-dotnet.md)
- [PHP](sql-database-connect-query-php.md)
- [Node.js](sql-database-connect-query-nodejs.md)
- [Java](sql-database-connect-query-java.md)
- [Python](sql-database-connect-query-python.md)
- [Ruby](sql-database-connect-query-ruby.md)


