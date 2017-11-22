---
title: "Создание экземпляра службы Azure Database Migration Service с помощью портала Azure | Документация Майкрософт"
description: "Использование портала Azure для создания экземпляра службы Azure Database Migration Service"
services: database-migration
author: edmacauley
ms.author: edmaca
manager: craigg
ms.reviewer: 
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: quickstart
ms.date: 11/08/2017
ms.openlocfilehash: 4213a0ae73c38dcad403140e3d45e28f2d338020
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/15/2017
---
# <a name="create-a-database-migration-service-instance-using-the-azure-portal"></a>Создание экземпляра службы Database Migration Service с помощью портала Azure
В этом кратком руководстве вы воспользуетесь порталом Azure, чтобы создать экземпляр службы Azure Database Migration Service.  После создания службы вы сможете использовать ее для переноса данных с локального сервера SQL Server в базу данных SQL Azure.

Если у вас еще нет подписки Azure, создайте [бесплатную](https://azure.microsoft.com/free/) учетную запись Azure, прежде чем начинать работу.

## <a name="log-in-to-the-azure-portal"></a>Войдите на портал Azure.
Откройте свой веб-браузер и перейдите на [портал Microsoft Azure](https://portal.azure.com/). Введите свои учетные данные для входа на портал. Панель мониторинга службы является представлением по умолчанию.

## <a name="create-azure-database-migration-service"></a>Создание службы Azure Database Migration Service
1. Щелкните **+**, чтобы создать новую службу.  Пока что доступна только предварительная версия службы Database Migration Service.  

1. Выполните поиск в Marketplace по слову "migration", выберите "Database Migration Service (предварительная версия)", а затем щелкните **Создать**.

    ![Создание службы миграции](media/quickstart-create-data-migration-service-portal/dms-create-service.png)

    - Выберите **имя службы**, которое хорошо запоминается и будет уникальным для идентификации экземпляра службы Azure Database Migration Service.
    - Выберите **подписку** Azure, в рамках которой нужно создать службу Database Migration Service.
    - Создайте новую **сеть** с уникальным именем.
    - Выберите **расположение**, наиболее близкое к исходному или целевому серверу.
    - Дл параметра **Ценовая категория** выберите значение Basic: 1 vCore (Базовый: 1 виртуальное ядро).

1. Щелкните **Создать**.

Через несколько секунд служба Azure Database Migration Service будет создана и готова к использованию.  Служба Database Migration Service отобразится, как показано на рисунке.

![Созданная служба Migration Service](media/quickstart-create-data-migration-service-portal/dms-service-created.png)

## <a name="clean-up-resources"></a>Очистка ресурсов
Чтобы очистить ресурсы, созданные при работе с этим кратким руководством, удалите [группу ресурсов Azure](../azure-resource-manager/resource-group-overview.md).  Чтобы удалить группу ресурсов, перейдите к созданной вами службе Database Migration Service, щелкните имя **группы ресурсов**, а затем выберите **Удалить группу ресурсов**.  Это действие удаляет все ресурсы в группе ресурсов, а также саму группу.

## <a name="next-steps"></a>Дальнейшие действия
> [!div class="nextstepaction"]
> [Перенос локального сервера SQL Server в базу данных SQL Azure](tutorial-sql-server-to-azure-sql.md)