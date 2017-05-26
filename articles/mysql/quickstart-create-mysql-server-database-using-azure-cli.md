---
title: "Краткое руководство. Создание базы данных Azure для сервера MySQL с помощью Azure CLI | Документация Майкрософт"
description: "В этом кратком руководстве описывается создание сервера базы данных Azure для MySQL в группе ресурсов Azure с помощью Azure CLI."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: mysql-database
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: portal
ms.workload: 
ms.date: 05/24/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: 9f78163e4ff1166a2abd94150d686256ee338286
ms.contentlocale: ru-ru
ms.lasthandoff: 05/25/2017

---

# <a name="create-an-azure-database-for-mysql-server-using-azure-cli"></a>Создание сервера базы данных Azure для MySQL с помощью Azure CLI
В этом кратком руководстве описывается создание сервера базы данных Azure для MySQL в группе ресурсов Azure с помощью Azure CLI за 5 минут. Azure CLI используется для создания ресурсов Azure и управления ими из командной строки или с помощью скриптов.

Для работы с этим кратким руководством нужно установить последнюю версию [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli). 

Если у вас еще нет подписки Azure, создайте [бесплатную](https://azure.microsoft.com/free/) учетную запись Azure, прежде чем начинать работу.

## <a name="log-in-to-azure"></a>Вход в Azure
Войдите в подписку Azure с помощью команды [az login](/cli/azure/#login) и следуйте инструкциям на экране.

```azurecli
az login
```
Следуйте указаниям командной строки, чтобы открыть https://aka.ms/devicelog в браузере, а затем введите код, созданный в **командной строке**.

## <a name="create-a-resource-group"></a>Создание группы ресурсов
Создайте [группу ресурсов Azure](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview) с помощью команды [az group create](https://docs.microsoft.com/cli/azure/group#create). Группа ресурсов — это логический контейнер, в котором ресурсы Azure развертываются и администрируются как группа.

В следующем примере создается группа ресурсов с именем `mycliresource` в расположении `westus`.

```azurecli
az group create --name mycliresource --location westus
```

## <a name="create-an-azure-database-for-mysql-server"></a>Создание сервера базы данных Azure для MySQL
Создайте сервер базы данных Azure для MySQL, выполнив команду **az mysql server create**. Сервер может управлять несколькими базами данных. Как правило, для каждого проекта и для каждого пользователя используется отдельная база данных.

В следующем примере в группе ресурсов `mycliresource` создается сервер базы данных Azure для MySQL с именем `mycliserver`, который расположен в `westus`. Для сервера указано имя администратора для входа `myadmin` и пароль `Password01!`. Он создается с уровнем производительности **Базовый** и **50** единицами вычислений, которые совместно используются всеми базами данных на сервере. В зависимости от потребностей приложения можно увеличить или уменьшить масштаб вычислительных ресурсов и ресурсов хранилища.

```azurecli
az mysql server create --resource-group mycliresource --name mycliserver--location westus --user myadmin --password Password01! --performance-tier Basic --compute-units 50
```

![Создание сервера базы данных Azure для MySQL с помощью Azure CLI](./media/quickstart-create-mysql-server-database-using-azure-cli/3_az-mysq-server-create.png)

## <a name="configure-firewall-rule"></a>Настройка правила брандмауэра
Создайте правило брандмауэра на уровне сервера базы данных Azure для MySQL, выполнив команду **az mysql server firewall-rule create**. Правило брандмауэра на уровне сервера позволяет внешним приложениям, таким как программа командной строки **mysql.exe** или MySQL Workbench, подключаться к серверу через брандмауэр службы Azure MySQL. 

В примере ниже показано создание правила брандмауэра для предопределенного диапазона адресов, который в этом примере представляет наиболее полный диапазон IP-адресов.

```azurecli
az mysql server firewall-rule create --resource-group mycliresource --server mycliserver --name AllowYourIP --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```
## <a name="configure-ssl-settings"></a>Настройка параметров SSL
По умолчанию между сервером и клиентскими приложениями применяется SSL-соединение.  Это гарантирует безопасное перемещение данных за счет шифрования потока данных через Интернет.  Чтобы упростить работу с этим руководством, необходимо отключить SSL-соединение для вашего сервера.  Мы не рекомендуем так делать для рабочих серверов.  Дополнительные сведения см. в статье [Настройка SSL-соединений в приложении для безопасного подключения к базе данных Azure для MySQL](./howto-configure-ssl.md).

В следующем примере показано, как отключить SSL-соединение на сервере MySQL.
 
 ```azurecli
 az mysql server update --resource-group mycliresource --name mycliserver -g -n --ssl-enforcement Disabled
 ```

## <a name="get-the-connection-information"></a>Получение сведений о подключении

Чтобы подключиться к серверу, необходимо указать сведения об узле и учетные данные для доступа.

```azurecli
az mysql server show --resource-group mycliresource --name mycliserver
```

Результаты выводятся в формате JSON. Запишите значения **fullyQualifiedDomainName** и **administratorLogin**.
```json
{
  "administratorLogin": "myadmin",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "mycliserver.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mycliresource/providers/Microsoft.DBforMySQL/servers/mycliserver",
  "location": "westus",
  "name": "mycliserver",
  "resourceGroup": "mycliresource",
  "sku": {
    "capacity": 50,
    "family": null,
    "name": "MYSQLS2M50",
    "size": null,
    "tier": "Basic"
  },
  "storageMb": 2048,
  "tags": null,
  "type": "Microsoft.DBforMySQL/servers",
  "userVisibleState": "Ready",
  "version": null
}
```

## <a name="connect-to-the-server-using-the-mysqlexe-command-line-tool"></a>Подключение к серверу с помощью программы командной строки mysql.exe
Чтобы подключиться к серверу с помощью программы командной строки **mysql.exe**, убедитесь, что на компьютере установлен экземпляр MySQL.  Скачать MySQL можно [здесь](https://dev.mysql.com/downloads/).

Введите приведенные ниже команды в окне командной строки. 

1. Подключитесь к серверу с помощью программы командной строки **mysql**:
```dos
 mysql -h mycliserver.mysql.database.azure.com -u myadmin@mycliserver -p
```

2. Просмотрите состояние сервера:
```dos
 mysql> status
```
Если все работает правильно, в программе командной строки должен отобразиться следующий результат:

```dos
C:\Users\v-chenyh>mysql -h mycliserver.mysql.database.azure.com -u myadmin@mycliserver -p
Enter password: ***********
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 65512
Server version: 5.6.26.0 MySQL Community Server (GPL)

Copyright (c) 2000, 2016, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> status
--------------
mysql  Ver 14.14 Distrib 5.6.35, for Win64 (x86_64)

Connection id:          65512
Current database:
Current user:           myadmin@116.230.243.143
SSL:                    Not in use
Using delimiter:        ;
Server version:         5.6.26.0 MySQL Community Server (GPL)
Protocol version:       10
Connection:             mycliserver.mysql.database.azure.com via TCP/IP
Server characterset:    latin1
Db     characterset:    latin1
Client characterset:    gbk
Conn.  characterset:    gbk
TCP port:               3306
Uptime:                 2 days 9 hours 47 min 20 sec

Threads: 4  Questions: 34833  Slow queries: 2  Opens: 84  Flush tables: 4  Open tables: 1  Queries per second avg: 0.167
--------------

mysql>
```

> [!TIP]
> Дополнительные сведения см. в [разделе 4.5.1 справочного руководства по MySQL 5.6](https://dev.mysql.com/doc/refman/5.6/en/mysql.html).

## <a name="connect-to-the-server-using-the-mysql-workbench-gui-tool"></a>Подключение к серверу с помощью инструмента графического пользовательского интерфейса MySQL Workbench
1.    Запустите приложение MySQL Workbench на клиентском компьютере. Скачать и установить MySQL Workbench вы можете [здесь](https://dev.mysql.com/downloads/workbench/).

2.    В диалоговом окне **настройки нового подключения** на вкладке **Параметры** введите следующие сведения:

| **Параметры** | **Описание** |
|----------------|-----------------|
|    *Имя подключения* | Укажите любое имя для этого подключения. |
| *Способ подключения* | Выберите стандартный способ (по протоколу TCP/IP). |
| *Имя узла* | mycliserver.MySQL.Database.Azure.com (имя сервера, которое вы записали ранее) |
| *Порт* | 3306 |
| *Имя пользователя* | myadmin@mycliserver (учетные данные администратора сервера для входа, которые вы записали ранее) |
| *Пароль* | Сохраните пароль учетной записи администратора. |

   ![Настройка нового подключения](./media/quickstart-create-mysql-server-database-using-azure-cli/setup-new-connection.png)

Щелкните **Проверить подключение**, чтобы проверить, все ли параметры верно настроены.
Теперь вы можете щелкнуть только что созданное подключение, чтобы успешно подключиться к серверу.

## <a name="clean-up-resources"></a>Очистка ресурсов

Если эти ресурсы не требуются для изучения другого руководства, вы можете их удалить. Для этого сделайте следующее: 

```azurecli
az group delete --name mycliresource
```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Проектирование первой базы данных Azure для MySQL](./tutorial-design-database-using-cli.md)

