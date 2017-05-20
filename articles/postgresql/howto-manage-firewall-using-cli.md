---
title: "Создание правил брандмауэра базы данных Azure для PostgreSQL и управление ими с помощью Azure CLI | Документация Майкрософт"
description: "Описывается, как создать правила брандмауэра базы данных Azure для PostgreSQL и управлять ими с помощью Azure CLI."
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: postgresql-database
ms.tgt_pltfrm: portal
ms.devlang: azurecli
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 5cb9a3e1fe6e5477e399fd7148fc5366f76cea38
ms.contentlocale: ru-ru
ms.lasthandoff: 05/10/2017

---
# <a name="create-and-manage-azure-database-for-postgresql-firewall-rules-using-azure-cli"></a>Создание правил брандмауэра базы данных Azure для PostgreSQL и управление ими с помощью Azure CLI
Правила брандмауэра уровня сервера позволяют администраторам управлять доступом к серверу базы данных Azure для PostgreSQL с указанного IP-адреса или диапазона IP-адресов. С помощью удобных команд Azure CLI можно создавать, обновлять, удалять, выводить список и отображать правила брандмауэра для управления сервером. Обзор брандмауэров базы данных Azure для PostgreSQL приведен в разделе [Правила брандмауэра сервера базы данных Azure для PostgreSQL](concepts-firewall-rules.md).

## <a name="prerequisites"></a>Предварительные требования
Прежде чем приступить к выполнению этого руководства, необходимы следующие компоненты:
- [сервер и база данных Azure для PostgreSQL](quickstart-create-server-database-azure-cli.md);
- установленная программа командной строки [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="configure-firewall-rules-for-azure-database-for-postgresql"></a>Настройка правил брандмауэра сервера базы данных Azure для PostgreSQL
Команда Azure CLI **az postgres server firewall-rule** используется для создания, удаления, вывода списка, отображения и обновления правил брандмауэра. Этот 

## <a name="login-to-azure-and-list-servers"></a>Вход в Azure и вывод списка серверов
Безопасно подключитесь к Azure CLI к помощью своей учетной записи Azure. Для этого используйте команду **az login**.
1. Выполните следующую команду из командной строки.
```azurecli
az login
```
Эта команда выведет код, который понадобится на следующем шаге.

2. Откройте в веб-браузере страницу [https://aka.ms/devicelogin](https://aka.ms/devicelogin) и введите код.

3. Когда появится окно для входа, войдите с использованием учетных данных Azure.

4. После авторизации имени для входа в консоли будет отображен список подписок.
Скопируйте необходимый идентификатор подписки, чтобы выбрать текущую подписку для дальнейших действий.
```azurecli
az account set --subscription {your subscription id}
```
5. Выведите список серверов базы данных Azure для PostgreSQL для своей подписки и группы ресурсов, если вы не уверены, что знаете нужные имена.

```azurecli
az postgres server list --resource-group myresourcegroup
```
Обратите внимание на атрибут имени в списке, который будет использоваться, чтобы указать сервер PostgreSQL для дальнейших действий. При необходимости подтвердите данные этого сервера с помощью атрибута имени, чтобы подтвердить, что имя указано правильно.

```azurecli
az postgres server show --resource-group myresourcegroup --name mypgserver-20170401
```

## <a name="list-firewall-rules"></a>Вывод списка правил брандмауэра 
Используя имя сервера и имя группы ресурсов, выведите список существующих правил брандмауэра на сервере. Обратите внимание, что атрибут имени сервера указан в параметре **--server**, а не в параметре **--name**.
```azurecli
az postgres server firewall-rule list --resource-group myresourcegroup --server mypgserver-20170401
```
Выходные данные будут содержать правила, если они имеются, в используемом по умолчанию в формате JSON. Можно указать параметр **--output table**, чтобы получить выходные данные в виде более удобной таблицы.
```azurecli
az postgres server firewall-rule list --resource-group myresourcegroup --server mypgserver-20170401 --output table
```
## <a name="create-firewall-rule"></a>Создание правила брандмауэра
Используя имя сервера базы данных Azure для PostgreSQL и имя группы ресурсов, создайте правило брандмауэра на сервере. Введите имя правила, а также начальный IP-адрес и конечный IP-адрес, чтобы определить диапазон IP-адресов, доступ с которых будет разрешен.
```azurecli
az postgres server firewall-rule create --resource-group myresourcegroup  --server mypgserver-20170401 --name "Firewall Rule 1" --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```
Чтобы разрешить доступ с отдельного IP-адреса, укажите одинаковый начальный и конечный IP-адрес, как показано в следующем примере.
```azurecli
az postgres server firewall-rule create --resource-group myresourcegroup  
--server mypgserver-20170401 --name "Firewall Rule with a Single Address" --start-ip-address 1.1.1.1 --end-ip-address 1.1.1.1
```
При успешном выполнении команды ее выходные данные будут содержать сведения о созданном правиле брандмауэра в используемом по умолчанию формате JSON. Если возникнет сбой, выходные данные будут содержать сообщение об ошибке.

## <a name="update-firewall-rule"></a>Изменение правила брандмауэра 
Используя имя сервера базы данных Azure для PostgreSQL и имя группы ресурсов, измените правило брандмауэра на сервере. В качестве входных данных укажите имя существующего правила брандмауэра, которое нужно обновить, а также атрибуты начального и конечного IP-адресов.
```azurecli
az postgres server firewall-rule update --resource-group myresourcegroup --server mypgserver-20170401 --name "Firewall Rule 1" --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```
При успешном выполнении команды ее выходные данные будут содержать сведения об обновленном правиле брандмауэра в используемом по умолчанию формате JSON. Если возникнет сбой, выходные данные будут содержать сообщение об ошибке.
> [!NOTE]
> Если правило брандмауэра не существует, оно будет создано командой update.

## <a name="show-firewall-rule-details"></a>Отображение сведений о правиле брандмауэра
Используя имя сервера базы данных Azure для PostgreSQL и имя группы ресурсов, отобразите сведения о правиле брандмауэра на сервере. Укажите имя существующего правила брандмауэра в качестве входных данных.
```azurecli
az postgres server firewall-rule show --resource-group myresourcegroup --server mypgserver-20170401 --name "Firewall Rule 1"
```
При успешном выполнении команды ее выходные данные будут содержать сведения об указанном правиле брандмауэра в используемом по умолчанию формате JSON. Если возникнет сбой, выходные данные будут содержать сообщение об ошибке.

## <a name="delete-firewall-rule"></a>Удаление правила брандмауэра
Используя имя сервера базы данных Azure для PostgreSQL и имя группы ресурсов, удалите правило брандмауэра с сервера. Укажите имя существующего правила брандмауэра.
```azurecli
az postgres server firewall-rule delete --resource-group myresourcegroup --server mypgserver-20170401 --name "Firewall Rule 1"
```
При успешном выполнении выходные данные отсутствуют. В случае сбоя возвращается сообщение об ошибке.

## <a name="next-steps"></a>Дальнейшие действия
- Аналогичным образом можно использовать веб-браузер, чтобы [создать правила брандмауэра базы данных Azure для PostgreSQL и управлять ими с помощью портала Azure](howto-manage-firewall-using-portal.md).
- Узнайте больше о [правилах брандмауэра сервера базы данных Azure для PostgreSQL](concepts-firewall-rules.md).
- Справка по подключению к серверу базы данных Azure для PostgreSQL доступна в разделе [Библиотеки подключений для базы данных Azure для PostgreSQL](concepts-connection-libraries.md).

