---
title: "Создание базы данных SQL и отправка к ней запросов с помощью Azure CLI | Документация Майкрософт"
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
ms.devlang: cli
ms.topic: hero-article
ms.date: 03/13/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 8322e46f462b6c940f9808411d99aa1cee0beea5
ms.lasthandoff: 03/15/2017

---

# <a name="create-and-query-a-single-azure-sql-database-with-the-azure-cli"></a>Создание базы данных SQL Azure и отправка к ней запросов с помощью Azure CLI

Azure CLI используется для создания ресурсов Azure и управления ими из командной строки или с помощью скриптов. В данном руководстве подробно описывается использование Azure CLI для развертывания базы данных SQL Azure.

Перед началом работы убедитесь, что вы установили Azure CLI. Дополнительные сведения см. в статье [Install Azure CLI&2;.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (Установка Azure CLI 2.0). 

## <a name="log-in-to-azure"></a>Вход в Azure

Войдите в подписку Azure с помощью команды [az login](/cli/azure/#login) и следуйте инструкциям на экране.

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте группу ресурсов с помощью команды [az group create](/cli/azure/group#create). Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими. В следующем примере создается группа ресурсов с именем `myResourceGroup` в расположении `westeurope`.

```azurecli
az group create --name myResourceGroup --location westeurope
```
## <a name="create-a-logical-server"></a>Создание логического сервера

Создайте логический сервер с помощью команды [az sql server create](/cli/azure/sql/server#create). В примере ниже показано создание сервера со случайным именем в группе ресурсов с именем администратора `ServerAdmin` и паролем `ChangeYourAdminPassword1`. Замените эти предопределенные значения по своему усмотрению.

```azurecli
servername=server-$RANDOM
az sql server create --name $servername --resource-group myResourceGroup --location westeurope \
    --admin-user ServerAdmin --admin-password ChangeYourAdminPassword1
```

## <a name="configure-a-server-firewall-rule"></a>Настройка правил брандмауэра сервера

Создайте правило брандмауэра на уровне сервера с помощью команды [az sql server firewall create](/cli/azure/sql/server/firewall-rule#create). Правило брандмауэра на уровне сервера позволяет внешним приложениям, таким как SQL Server Management Studio или программе sqlcmd, подключаться к базе данных SQL через брандмауэр службы базы данных SQL. В примере ниже показано создание правила брандмауэра для предопределенного диапазона адресов, который в этом примере представляет наиболее полный диапазон IP-адресов. Замените эти предопределенные значения значением внешнего IP-адреса или диапазона IP-адресов. 

```azurecli
az sql server firewall-rule create --resource-group myResourceGroup --server $servername \
    -n AllowYourIp --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

## <a name="create-a-database-in-the-server"></a>Создание базы данных на сервере

Создайте базу данных на сервере с помощью команды [az sql db create](/cli/azure/sql/db#create). В примере ниже показано создание пустой базы данных с именем `mySampleDatabase`. Замените это предопределенное значение по своему усмотрению.

```azurecli
az sql db create --resource-group myResourceGroup --server $servername \
    --name mySampleDatabase --service-objective S0
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Чтобы удалить все ресурсы, созданные в этом кратком руководстве, выполните следующую команду:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения о подключении к базе данных SQL с помощью SQL Server Management Studio и выполнении запроса к ней см. [здесь](sql-database-connect-query-ssms.md).
- Дополнительные сведения о подключении с помощью Visual Studio см. в статье [Azure SQL Database: Use Visual Studio Code to connect and query data](sql-database-connect-query.md) (База данных SQL Azure. Использование кода Visual Studio для подключения к базе данных и выполнения запроса).
- Технический обзор базы данных SQL см. в статье [Что такое база данных SQL? Введение в базы данных SQL](sql-database-technical-overview.md).

