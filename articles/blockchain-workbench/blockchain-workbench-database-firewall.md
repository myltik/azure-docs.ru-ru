---
title: Настройка брандмауэра базы данных SQL Azure Blockchain Workbench
description: Дополнительные сведения о настройке брандмауэра базы данных SQL Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/4/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: afeea143f73fa4f7d3e373535007846a668616ab
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2018
ms.locfileid: "34074955"
---
# <a name="configure-the-azure-blockchain-workbench-database-firewall"></a>Настройка брандмауэра базы данных Azure Blockchain Workbench

В этой статье показано, как настроить правило брандмауэра с помощью портала Azure. Правила брандмауэра позволяют внешним клиентам или приложениям подключаться к базе данных Azure Blockchain Workbench.

## <a name="connect-to-the-blockchain-workbench-database"></a>Подключение к базе данных Blockchain Workbench

Чтобы подключиться к базе данных, в которой вы хотите настроить правило, сделайте следующее:

1. Войдите на портал Azure с учетной записью с разрешениями **владельца** для ресурсов Azure Blockchain Workbench.
2. В левой области навигации выберите **Группа ресурсов**.
3. Выберите имя группы ресурсов для развертывания Blockchain Workbench.
4. Выберите **Тип** для сортировки списка ресурсов, а затем выберите **SQL Server**.
5. В примере списка ресурсов на следующем снимке экрана показано две базы данных: *master* и *lsgn-sdk*. Настройте правило брандмауэра на *lsgn-sdk*.

![Список ресурсов Blockchain Workbench](media/blockchain-workbench-database-firewall/list-database-resources.png)

## <a name="create-a-database-firewall-rule"></a>Создание правила брандмауэра базы данных

Чтобы создать правило брандмауэра, сделайте следующее:

1. Выберите ссылку на базу данных lsgn-sdk.
2. В строке меню выберите **Настройка брандмауэра для сервера**.

   ![Настройка брандмауэра для сервера](media/blockchain-workbench-database-firewall/configure-server-firewall.png)

3. Чтобы создать правило для организации, сделайте следующее:

   * Введите **имя правила**.
   * Введите IP-адрес в поле **НАЧАЛЬНЫЙ IP-АДРЕС** диапазона адресов.
   * Введите IP-адрес в поле **КОНЕЧНЫЙ IP-АДРЕС** диапазона адресов.

   ![Создание правила брандмауэра](media/blockchain-workbench-database-firewall/create-firewall-rule.png)

    > [!NOTE]
    > Если требуется только добавить IP-адрес компьютера, выберите **+ Add client IP** (+ Добавить IP-адрес клиента).
        
1. Чтобы сохранить конфигурацию брандмауэра, щелкните **Сохранить**.
2. Проверьте диапазон IP-адресов, настроенных для базы данных, подключившись с помощью приложения или средства. Например, SQL Server Management Studio.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Database views in Azure Blockchain Workbench](blockchain-workbench-database-views.md) (Представления базы данных в Azure Blockchain Workbench)