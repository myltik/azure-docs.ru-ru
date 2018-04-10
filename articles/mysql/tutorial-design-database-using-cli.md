---
title: Руководство по проектированию службы "База данных Azure для MySQL" с помощью Azure CLI
description: Это руководство содержит сведения о создании базы данных и сервера базы данных Azure для MySQL и управлении ими с помощью Azure CLI 2.0 из командной строки.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.topic: tutorial
ms.date: 04/01/2018
ms.custom: mvc
ms.openlocfilehash: 0c1c9364d6d7071e0aa454889417eeec3807f406
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2018
---
# <a name="tutorial-design-an-azure-database-for-mysql-using-azure-cli"></a>Руководство по проектированию службы "База данных Azure для MySQL" с помощью Azure CLI

База данных Azure для MySQL — это служба реляционной базы данных в Microsoft Cloud на основе ядра СУБД MySQL Community Edition. Из этого руководства вы узнаете, как с помощью Azure CLI (интерфейса командной строки) и других служебных программ выполнять следующие операции:

> [!div class="checklist"]
> * создание базы данных Azure для MySQL;
> * настройка брандмауэра сервера;
> * использование [программы командной строки MySQL](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) для создания базы данных.
> * Загрузка примера данных
> * Запрос данных
> * Обновление данных
> * восстановление данных.

Вы можете использовать Azure Cloud Shell в браузере или [установить Azure CLI 2.0]( /cli/azure/install-azure-cli) на компьютере, чтобы запустить блоки кода в этом руководстве.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать интерфейс командной строки локально, для работы с этой статьей вам понадобится Azure CLI 2.0 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli). 

Если вы используете несколько подписок, выберите соответствующую подписку, в которой находится ресурс либо в которой за него взимается плата. Выберите конкретный идентификатор подписки вашей учетной записи, выполнив команду [az account set](/cli/azure/account#az_account_set).
```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Создание группы ресурсов
Создайте [группу ресурсов Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview), выполнив команду [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create). Группа ресурсов — это логический контейнер, в котором ресурсы Azure развертываются и администрируются как группа.

В следующем примере создается группа ресурсов с именем `myresourcegroup` в расположении `westus`.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```
## <a name="add-the-extension"></a>Добавление расширения
Добавьте обновленное расширение управления Базы данных Azure для MySQL, используя следующую команду.
```azurecli-interactive
az extension add --name rdbms
``` 

Убедитесь, что установлена правильная версия расширения. 
```azurecli-interactive
az extension list
```

В возвращенных данных JSON должно быть следующее: 
```json
{
    "extensionType": "whl",
    "name": "rdbms",
    "version": "0.0.5"
}
```

Если возвращается версия, отличная от 0.0.5, выполните следующую команду, чтобы обновить расширение: 
```azurecli-interactive
az extension update --name rdbms
```

## <a name="create-an-azure-database-for-mysql-server"></a>Создайте сервер базы данных Azure для MySQL.
Создайте сервер базы данных Azure для MySQL, выполнив команду az mysql server create. Сервер может управлять несколькими базами данных. Как правило, для каждого проекта и для каждого пользователя используется отдельная база данных.

В следующем примере в группе ресурсов `myresourcegroup` создается сервер базы данных Azure для MySQL с именем `mydemoserver`, который расположен в `westus`. Для сервера указано имя администратора для входа `myadmin`. Это сервер общего назначения четвертого поколения с 2 виртуальными ядрами. Замените `<server_admin_password>` собственным значением.

```azurecli-interactive
az mysql server create --resource-group myresourcegroup --name mydemoserver --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen4_2 --version 5.7
```
> [!IMPORTANT]
> Указанные здесь учетные данные и пароль администратора сервера понадобятся позже в этом руководстве, чтобы войти на сервер и в его базу данных. Запомните или запишите эту информацию для последующего использования.


## <a name="configure-firewall-rule"></a>Настройка правила брандмауэра
Создайте правило брандмауэра на уровне сервера базы данных Azure для MySQL, выполнив команду az mysql server firewall-rule create. Правило брандмауэра на уровне сервера позволяет внешним приложениям, таким как средство командной строки **MySQL** или MySQL Workbench, подключаться к серверу через брандмауэр службы Azure MySQL. 

В примере ниже показано создание правила брандмауэра для предопределенного диапазона адресов, который в этом примере представляет наиболее полный диапазон IP-адресов.

```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowAllIPs --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

## <a name="get-the-connection-information"></a>Получение сведений о подключении

Чтобы подключиться к серверу, необходимо указать сведения об узле и учетные данные для доступа.
```azurecli-interactive
az mysql server show --resource-group myresourcegroup --name mydemoserver
```

Результаты выводятся в формате JSON. Запишите значения **fullyQualifiedDomainName** и **administratorLogin**.
```json
{
  "administratorLogin": "myadmin",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "mydemoserver.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMySQL/servers/mydemoserver",
  "location": "westus",
  "name": "mydemoserver",
  "resourceGroup": "myresourcegroup",
 "sku": {
    "capacity": 2,
    "family": "Gen4",
    "name": "GP_Gen4_2",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "sslEnforcement": "Enabled",
  "storageProfile": {
    "backupRetentionDays": 7,
    "geoRedundantBackup": "Disabled",
    "storageMb": 5120
  },
  "tags": null,
  "type": "Microsoft.DBforMySQL/servers",
  "userVisibleState": "Ready",
  "version": "5.7"
}
```

## <a name="connect-to-the-server-using-mysql"></a>Подключение к серверу с помощью MySQL
Используйте [программу командной строки MySQL](https://dev.mysql.com/doc/refman/5.6/en/mysql.html), чтобы подключиться к серверу базы данных Azure для MySQL. В этом примере используется следующая команда:
```cmd
mysql -h mydemoserver.database.windows.net -u myadmin@mydemoserver -p
```

## <a name="create-a-blank-database"></a>Создание пустой базы данных
Подключившись к серверу, создайте пустую базу данных.
```sql
mysql> CREATE DATABASE mysampledb;
```

Выполните следующую команду в командной строке, чтобы подключиться к созданной базе данных:
```sql
mysql> USE mysampledb;
```

## <a name="create-tables-in-the-database"></a>Создание таблиц в базе данных
Теперь, когда вы знаете, как подключиться к базе данных Azure для MySQL, выполните некоторые основные задачи.

Сначала создайте таблицу и заполните ее некоторыми данными. Давайте создадим таблицу, в которой хранятся данные инвентаризации.
```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

## <a name="load-data-into-the-tables"></a>Загрузка данных в таблицу
Теперь, когда таблица создана, мы можем вставить в нее данные. Чтобы вставить некоторые строки данных, в открытом окне командной строки выполните следующий запрос:
```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

Итак, в созданной ранее таблице содержится две строки данных.

## <a name="query-and-update-the-data-in-the-tables"></a>Запрос и обновление данных в таблицах
Чтобы извлечь сведения из таблицы базы данных, выполните приведенный ниже запрос.
```sql
SELECT * FROM inventory;
```

Вы можете также обновить данные в таблицах, выполнив следующую команду:
```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

При извлечении данных строка будет обновляться соответствующим образом.
```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>Восстановление базы данных до предыдущей точки во времени
Представьте, что вы случайно удалили таблицу. Восстановить ее будет не просто. База данных Azure для MySQL позволяет вернуться в любой момент времени в течение последних 35 дней и восстановить данные на определенный момент времени на новом сервере. Вы можете восстановить удаленные данные с помощью нового сервера. Указанные ниже шаги позволяют восстановить сервер до точки во времени, когда была создана таблица.

Чтобы восстановить данные, вам потребуются следующие сведения:

- Точка восстановления. Выберите время до того момента, когда был изменен сервер. Значение точки восстановления должно быть не меньше значения самой старой резервной копии исходной базы данных.
- Целевой сервер. Укажите новое имя сервера, который нужно восстановить.
- Исходный сервер. Укажите имя сервера, из которого требуется выполнить восстановление.
- Расположение. Вы не сможете выбрать регион, по умолчанию он совпадает с исходным сервером.

```azurecli-interactive
az mysql server restore --resource-group myresourcegroup --name mydemoserver-restored --restore-point-in-time "2017-05-4 03:10" --source-server-name mydemoserver
```

Для команды `az mysql server restore` необходимо настроить следующие параметры:
| Параметр | Рекомендуемое значение | ОПИСАНИЕ  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Группа ресурсов, в которой находится исходный сервер.  |
| name | mydemoserver-restored | Имя нового сервера, созданного командой restore. |
| restore-point-in-time | 2017-04-13T13:59:00Z | Выберите точку во времени, до которой необходимо выполнить восстановление. Значения даты и времени должны находиться в пределах срока хранения резервной копии исходного сервера. Используйте формат даты и времени ISO8601. Например, вы можете использовать свой местный часовой пояс, например `2017-04-13T05:59:00-08:00`, или использовать формат UTC Zulu `2017-04-13T13:59:00Z`. |
| source-server | mydemoserver | Имя или идентификатор исходного сервера, с которого необходимо выполнить восстановление. |

При восстановлении сервера до определенной точки во времени создается новый сервер путем копирования той точки во времени исходного сервера, которую вы задали. Значения расположения и ценовой категории для восстановленного сервера совпадают со значениями исходного сервера.

Команда выполняется в синхронном режиме и будет возвращена после восстановления сервера. После завершения восстановления найдите созданный сервер. Убедитесь, что данные восстановлены надлежащим образом.

## <a name="next-steps"></a>Дополнительная информация
Из этого руководства вы узнали, как выполнять следующие операции:
> [!div class="checklist"]
> * Создайте сервер базы данных Azure для MySQL.
> * настройка брандмауэра сервера;
> * использование [программы командной строки MySQL](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) для создания базы данных.
> * Загрузка примера данных
> * Запрос данных
> * Обновление данных
> * восстановление данных.

> [!div class="nextstepaction"]
> [Примеры Azure CLI для базы данных Azure для MySQL](./sample-scripts-azure-cli.md)
