---
title: "Создание правил брандмауэра базы данных Azure для MySQL и управление ими с помощью Azure CLI | Документация Майкрософт"
description: "В этой статье описывается, как создать базу данных Azure для правил брандмауэра MySQL и управлять ею с помощью интерфейса командной строки Azure."
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.topic: article
ms.date: 06/13/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: 9a03722e9f71be307bdbf0b846a4cbf7b34cd7ff
ms.contentlocale: ru-ru
ms.lasthandoff: 06/17/2017

---

# <a name="create-and-manage-azure-database-for-mysql-firewall-rules-using-azure-cli"></a>Создание правил брандмауэра базы данных Azure для MySQL и управление ими с помощью Azure CLI
Правила брандмауэра уровня сервера позволяют администраторам управлять доступом к серверу базы данных Azure для MySQL с указанного IP-адреса или диапазона IP-адресов. С помощью удобных команд Azure CLI можно создавать, обновлять, удалять, выводить список и отображать правила брандмауэра для управления сервером. Обзор брандмауэров базы данных Azure для MySQL приведен в разделе [Правила брандмауэра сервера базы данных Azure для MySQL](./concepts-firewall-rules.md).

## <a name="prerequisites"></a>Предварительные требования
* [Установите Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).
* Установите пакет SDK Azure для Python для служб MySQL и PostgreSQL.
* Установите компонент Azure CLI для служб PostgreSQL и MySQL.
* Создайте сервер базы данных Azure для MySQL.

## <a name="firewall-rule-commands"></a>Команды для правила брандмауэра
Команда Azure CLI **az mysql server firewall-rule** используется для создания, удаления, вывода списка, отображения и обновления правил брандмауэра.

Команды:
- **create**: создание правила брандмауэра сервера Azure MySQL.
- **delete**: удаление правила брандмауэра сервера Azure MySQL.
- **list**: вывод списка правил брандмауэра сервера Azure MySQL.
- **show**: отображение сведений о правиле брандмауэра сервере Azure MySQL.
- **update**: обновление правила брандмауэра сервера Azure MySQL.

## <a name="login-to-azure-and-list-your-azure-database-for-mysql-servers"></a>Вход в Azure и вывод списка серверов базы данных Azure для MySQL
Безопасно подключитесь к Azure CLI к помощью своей учетной записи Azure. Для этого используйте команду **az login**.

1. Выполните следующую команду из командной строки.
```azurecli
az login
```
Эта команда выведет код, который понадобится на следующем шаге.

2. Откройте в веб-браузере страницу [https://aka.ms/devicelogin](https://aka.ms/devicelogin) и введите код.

3. Когда появится окно для входа, войдите с использованием учетных данных Azure.

4. После авторизации имени для входа в консоли будет отображен список подписок. Скопируйте необходимый идентификатор подписки, чтобы выбрать текущую подписку для дальнейших действий.
   ```azurecli-interactive
   az account set --subscription {your subscription id}
   ```

5. Выведите список серверов базы данных Azure для MySQL для своей подписки и группы ресурсов, если вы не уверены, что знаете нужные имена.

   ```azurecli-interactive
   az mysql server list --resource-group myResourceGroup
   ```

   Обратите внимание на атрибут имени в списке, который будет использоваться, чтобы указать сервер MySQL для дальнейших действий. При необходимости подтвердите данные этого сервера с помощью атрибута имени, чтобы подтвердить, что имя указано правильно.

   ```azurecli-interactive
   az mysql server show --resource-group myResourceGroup --name mysqlserver4demo
   ```

## <a name="list-firewall-rules-on-azure-database-for-mysql-server"></a>Вывод списка правил брандмауэра для сервера базы данных Azure для MySQL 
Используя имя сервера и имя группы ресурсов, выведите список существующих правил брандмауэра на сервере. Обратите внимание, что атрибут имени сервера указан в параметре **--server**, а не в параметре **--name**.
```azurecli-interactive
az mysql server firewall-rule list --resource-group myResourceGroup --server mysqlserver4demo
```
Выходные данные будут содержать правила, если они имеются, в используемом по умолчанию в формате JSON. Можно указать параметр **--output table**, чтобы получить выходные данные в виде более удобной таблицы.
```azurecli-interactive
az mysql server firewall-rule list --resource-group myResourceGroup --server mysqlserver4demo --output table
```
## <a name="create-firewall-rule-on-azure-database-for-mysql-server"></a>Создание правила брандмауэра для сервера базы данных Azure для MySQL
Используя имя сервера Azure MySQL и имя группы ресурсов, создайте правило брандмауэра на сервере. Введите имя правила, а также начальный IP-адрес и конечный IP-адрес, чтобы определить диапазон IP-адресов, доступ с которых будет разрешен.
```azurecli-interactive
az mysql server firewall-rule create --resource-group myResourceGroup  --server mysqlserver4demo --name "Firewall Rule 1" --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```
Чтобы разрешить доступ с отдельного IP-адреса, укажите одинаковый начальный и конечный IP-адрес, как показано в следующем примере.
```azurecli-interactive
az mysql server firewall-rule create --resource-group myResourceGroup  
--server mysql --name "Firewall Rule with a Single Address" --start-ip-address 1.1.1.1 --end-ip-address 1.1.1.1
```
При успешном выполнении команды ее выходные данные будут содержать сведения о созданном правиле брандмауэра в используемом по умолчанию формате JSON. Если возникнет сбой, выходные данные будут содержать сообщение об ошибке.

## <a name="update-firewall-rule-on-azure-database-for-mysql-server"></a>Обновление правила брандмауэра для сервера базы данных Azure для MySQL 
Используя имя сервера Azure MySQL и имя группы ресурсов, измените правило брандмауэра на сервере. В качестве входных данных укажите имя существующего правила брандмауэра, которое нужно обновить, а также атрибуты начального и конечного IP-адресов.
```azurecli-interactive
az mysql server firewall-rule update --resource-group myResourceGroup --server mysqlserver4demo --name "Firewall Rule 1" --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```
При успешном выполнении команды ее выходные данные будут содержать сведения об обновленном правиле брандмауэра в используемом по умолчанию формате JSON. Если возникнет сбой, выходные данные будут содержать сообщение об ошибке.

> [!NOTE]
> Если правило брандмауэра не существует, оно будет создано командой update.

## <a name="show-firewall-rule-details-on-azure-database-for-mysql-server"></a>Отображение сведения о правиле брандмауэра для сервера базы данных Azure для MySQL
Используя имя сервера Azure MySQL и имя группы ресурсов, отобразите сведения о правиле брандмауэра на сервере. Укажите имя существующего правила брандмауэра в качестве входных данных.
```azurecli-interactive
az mysql server firewall-rule show --resource-group myResourceGroup --server mysqlserver4demo --name "Firewall Rule 1"
```
При успешном выполнении команды ее выходные данные будут содержать сведения об указанном правиле брандмауэра в используемом по умолчанию формате JSON. Если возникнет сбой, выходные данные будут содержать сообщение об ошибке.

## <a name="delete-firewall-rule-on-azure-database-for-mysql-server"></a>Удаление правила брандмауэра для сервера базы данных Azure для MySQL
Используя имя сервера Azure MySQL и имя группы ресурсов, удалите правило брандмауэра с сервера. Укажите имя существующего правила брандмауэра.
```azurecli-interactive
az mysql server firewall-rule delete --resource-group myResourceGroup --server mysqlserver4demo --name "Firewall Rule 1"
```
При успешном выполнении выходные данные отсутствуют. В случае сбоя возвращается сообщение об ошибке.

## <a name="next-steps"></a>Дальнейшие действия
- Узнайте больше о [правилах брандмауэра сервера базы данных Azure для MySQL](./concepts-firewall-rules.md).
- [Создание правил брандмауэра базы данных Azure для MySQL и управление ими с помощью портала Azure](./howto-manage-firewall-using-portal.md)

