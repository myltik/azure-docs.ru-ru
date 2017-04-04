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
ms.custom: quick start
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: hero-article
ms.date: 03/13/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 3d642447526c5562ae3bfad5e4a4592e33e766aa
ms.lasthandoff: 03/25/2017

---

# <a name="create-a-single-azure-sql-database-using-the-azure-cli"></a>Создание отдельной базы данных SQL Azure с помощью Azure CLI

Azure CLI используется для создания ресурсов Azure и управления ими из командной строки или с помощью скриптов. В этом руководстве подробно объясняется, как с помощью Azure CLI развернуть базу данных SQL Azure в [группе ресурсов Azure](../azure-resource-manager/resource-group-overview.md) на [логическом сервере базы данных Azure SQL](sql-database-features.md).

Перед началом работы убедитесь, что вы установили Azure CLI. Дополнительные сведения см. в [руководстве по установке Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli). 

## <a name="log-in-to-azure"></a>Вход в Azure

Войдите в подписку Azure с помощью команды [az login](/cli/azure/#login) и следуйте инструкциям на экране.

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте [группу ресурсов Azure](../azure-resource-manager/resource-group-overview.md) с помощью команды [az group create](/cli/azure/group#create). Группа ресурсов — это логический контейнер, в котором ресурсы Azure развертываются и администрируются как группа. В следующем примере создается группа ресурсов с именем `myResourceGroup` в расположении `westeurope`.

```azurecli
az group create --name myResourceGroup --location westeurope
```
## <a name="create-a-logical-server"></a>Создание логического сервера

Создайте [логический сервер базы данных Azure SQL](sql-database-features.md) с помощью команды [az sql server create](/cli/azure/sql/server#create). Логический сервер содержит группу баз данных, которыми можно управлять как группой. В примере ниже показано создание сервера со случайным именем в группе ресурсов с именем администратора `ServerAdmin` и паролем `ChangeYourAdminPassword1`. Замените эти предопределенные значения по своему усмотрению.

```azurecli
servername=server-$RANDOM
az sql server create --name $servername --resource-group myResourceGroup --location westeurope \
    --admin-user ServerAdmin --admin-password ChangeYourAdminPassword1
```

## <a name="configure-a-server-firewall-rule"></a>Настройка правил брандмауэра сервера

Создайте [правило брандмауэра на уровне сервера базы данных Azure SQL](sql-database-firewall-configure.md) с помощью команды [az sql server firewall create](/cli/azure/sql/server/firewall#create). Правило брандмауэра на уровне сервера позволяет внешним приложениям, таким как SQL Server Management Studio или программе sqlcmd, подключаться к базе данных SQL через брандмауэр службы базы данных SQL. В примере ниже показано создание правила брандмауэра для предопределенного диапазона адресов, который в этом примере представляет наиболее полный диапазон IP-адресов. Замените эти предопределенные значения значением внешнего IP-адреса или диапазона IP-адресов. 

```azurecli
az sql server firewall-rule create --resource-group myResourceGroup --server $servername \
    -n AllowYourIp --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

## <a name="create-a-database-in-the-server-with-sample-data"></a>Создание базы данных на сервере с образцами данных

Создайте на сервере базу данных с [уровнем производительности S0](sql-database-service-tiers.md) с помощью команды [az sql db create](/cli/azure/sql/db#create). В следующем примере создается база данных с именем `mySampleDatabase`, в которую загружается образец данных AdventureWorksLT. При необходимости замените эти предопределенные значения (другие краткие руководства в этой коллекции созданы на основе этого документа).

```azurecli
az sql db create --resource-group myResourceGroup --server $servername \
    --name mySampleDatabase --sample-name AdventureWorksLT --service-objective S0
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Другие краткие руководства в этой коллекции созданы на основе этого документа. Если вы планируете продолжать работу с этими руководствами по быстрому запуску или обычными руководствами, не удаляйте созданные ресурсы. Если вы не планируете продолжать работу, удалите все созданные ресурсы с помощью следующей команды.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения о подключении к базе данных SQL с помощью SQL Server Management Studio и выполнении запроса к ней см. [здесь](sql-database-connect-query-ssms.md).
- Дополнительные сведения о подключении с помощью Visual Studio см. в статье [Azure SQL Database: Use Visual Studio Code to connect and query data](sql-database-connect-query.md) (База данных SQL Azure. Использование кода Visual Studio для подключения к базе данных и выполнения запроса).
- Технический обзор базы данных SQL см. в статье [Что такое база данных SQL? Введение в базы данных SQL](sql-database-technical-overview.md).

