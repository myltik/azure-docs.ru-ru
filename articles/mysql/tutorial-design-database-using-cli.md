---
title: "Проектирование первой базы данных Azure для MySQL с помощью Azure CLI | Документация Майкрософт"
description: "Это руководство содержит сведения о создании базы данных и сервера базы данных Azure для MySQL и управлении ими с помощью Azure CLI 2.0 из командной строки."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
ms.service: mysql
ms.devlang: azure-cli
ms.topic: tutorial
ms.date: 06/13/2017
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 190ca4b228434a7d1b30348011c39a979c22edbd
ms.openlocfilehash: 0e6a92eeb9711b086359ab2cd1aea87a57f1fc36
ms.contentlocale: ru-ru
ms.lasthandoff: 09/09/2017

---

# <a name="design-your-first-azure-database-for-mysql-database"></a>Проектирование первой базы данных Azure для MySQL

База данных Azure для MySQL — это служба реляционной базы данных в Microsoft Cloud на основе ядра СУБД MySQL Community Edition. Из этого руководства вы узнаете, как с помощью Azure CLI (интерфейса командной строки) и других служебных программ выполнять следующие операции:

> [!div class="checklist"]
> * создание базы данных Azure для MySQL;
> * настройка брандмауэра сервера;
> * использование [средства командной строки MySQL](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) для создания базы данных;
> * Загрузка примера данных
> * Запрос данных
> * Обновление данных
> * восстановление данных.

Вы можете использовать Azure Cloud Shell в браузере или [установить Azure CLI 2.0]( /cli/azure/install-azure-cli) на компьютере, чтобы запустить блоки кода в этом руководстве.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать интерфейс командной строки локально, для работы с этим руководством вам понадобится Azure CLI 2.0 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli). 

Если вы используете несколько подписок, выберите соответствующую подписку, в которой находится ресурс либо в которой за него взимается плата. Выберите конкретный идентификатор подписки вашей учетной записи, выполнив команду [az account set](/cli/azure/account#set).
```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Создание группы ресурсов
Создайте [группу ресурсов Azure](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview), выполнив команду [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create). Группа ресурсов — это логический контейнер, в котором ресурсы Azure развертываются и администрируются как группа.

В следующем примере создается группа ресурсов с именем `mycliresource` в расположении `westus`.

```azurecli-interactive
az group create --name mycliresource --location westus
```

## <a name="create-an-azure-database-for-mysql-server"></a>Создание сервера базы данных Azure для MySQL
Создайте сервер базы данных Azure для MySQL, выполнив команду az mysql server create. Сервер может управлять несколькими базами данных. Как правило, для каждого проекта и для каждого пользователя используется отдельная база данных.

В следующем примере в группе ресурсов `mycliresource` создается сервер базы данных Azure для MySQL с именем `mycliserver`, который расположен в `westus`. Для сервера указано имя администратора для входа `myadmin` и пароль `Password01!`. Он создается с уровнем производительности **Базовый** и **50** единицами вычислений, которые совместно используются всеми базами данных на сервере. В зависимости от потребностей приложения можно увеличить или уменьшить масштаб вычислительных ресурсов и ресурсов хранилища.

```azurecli-interactive
az mysql server create --resource-group mycliresource --name mycliserver
--location westus --user myadmin --password Password01!
--performance-tier Basic --compute-units 50
```

## <a name="configure-firewall-rule"></a>Настройка правила брандмауэра
Создайте правило брандмауэра на уровне сервера базы данных Azure для MySQL, выполнив команду az mysql server firewall-rule create. Правило брандмауэра на уровне сервера позволяет внешним приложениям, таким как средство командной строки **MySQL** или MySQL Workbench, подключаться к серверу через брандмауэр службы Azure MySQL. 

В примере ниже показано создание правила брандмауэра для предопределенного диапазона адресов, который в этом примере представляет наиболее полный диапазон IP-адресов.

```azurecli-interactive
az mysql server firewall-rule create --resource-group mycliresource --server mycliserver --name AllowYourIP --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

## <a name="get-the-connection-information"></a>Получение сведений о подключении

Чтобы подключиться к серверу, необходимо указать сведения об узле и учетные данные для доступа.
```azurecli-interactive
az mysql server show --resource-group mycliresource --name mycliserver
```

Результаты выводятся в формате JSON. Запишите значения **fullyQualifiedDomainName** и **administratorLogin**.
```json
{
  "administratorLogin": "myadmin",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "mycliserver.database.windows.net",
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

## <a name="connect-to-the-server-using-mysql"></a>Подключение к серверу с помощью MySQL
Используйте [средство командной строки MySQL](https://dev.mysql.com/doc/refman/5.6/en/mysql.html), чтобы установить соединение с сервером базы данных Azure для MySQL. В этом примере используется следующая команда:
```cmd
mysql -h mycliserver.database.windows.net -u myadmin@mycliserver -p
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
Теперь, когда вы знаете, как подключиться к базе данных Azure для MySQL, рассмотрим, как выполнить некоторые основные задачи.

Сначала можно создать таблицу и заполнить ее некоторыми данными. Давайте создадим таблицу, в которой хранятся данные инвентаризации.
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
az mysql server restore --resource-group mycliresource --name mycliserver-restored --restore-point-in-time "2017-05-4 03:10" --source-server-name mycliserver
```

Выполните предыдущую команду и восстановите сервер до [точки во времени](./howto-restore-server-portal.md) перед удалением таблицы. Восстановление сервера до точки во времени создает копию сервера, где расположен исходный сервер, с состоянием на момент указанной точки во времени (в пределах срока хранения, установленного для вашего [уровня служб](./concepts-service-tiers.md)).

## <a name="next-steps"></a>Дальнейшие действия
Из этого руководства вы узнали, как выполнять следующие операции:
> [!div class="checklist"]
> * создание базы данных Azure для MySQL;
> * настройка брандмауэра сервера;
> * использование [средства командной строки MySQL](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) для создания базы данных;
> * Загрузка примера данных
> * Запрос данных
> * Обновление данных
> * восстановление данных.

> [!div class="nextstepaction"]
> [Примеры Azure CLI для базы данных Azure для MySQL](./sample-scripts-azure-cli.md)

