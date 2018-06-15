---
title: Создание экземпляра службы Azure Database Migration Service с помощью портала Azure | Документация Майкрософт
description: Использование портала Azure для создания экземпляра службы Azure Database Migration Service
services: database-migration
author: edmacauley
ms.author: edmaca
manager: craigg
ms.reviewer: ''
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: quickstart
ms.date: 05/01/2018
ms.openlocfilehash: 7669ee678f4049c938c6e249c26997d993fd7c6a
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/03/2018
ms.locfileid: "32772200"
---
# <a name="create-an-instance-of-the-azure-database-migration-service-by-using-the-azure-portal"></a>Создание экземпляра службы Azure Database Migration Service с помощью портала Azure
В этом кратком руководстве вы создадите экземпляр службы Azure Database Migration Service с помощью портала Azure.  После создания службы вы сможете использовать ее для переноса данных с локального сервера SQL Server в базу данных SQL Azure.

Если у вас еще нет подписки Azure, создайте [бесплатную](https://azure.microsoft.com/free/) учетную запись Azure, прежде чем начинать работу.

## <a name="log-in-to-the-azure-portal"></a>Войдите на портал Azure.
Откройте веб-браузер, перейдите к [порталу Microsoft Azure](https://portal.azure.com/) и введите учетные данные для входа на портал.

Панель мониторинга службы является представлением по умолчанию.

## <a name="register-the-resource-provider"></a>Регистрация поставщика ресурсов
Прежде чем создать свой первый экземпляр Database Migration Service, зарегистрируйте поставщик ресурсов Microsoft.DataMigration.

1. На портале Azure щелкните **Все службы** и выберите **Подписки**.

2. Выберите подписку, в которой нужно создать экземпляр Azure Database Migration Service, а затем щелкните **Поставщики ресурсов**.

3. В поле поиска введите migration, а затем справа от Microsoft.DataMigration щелкните **Зарегистрировать**.

![Регистрация поставщика ресурсов](media/quickstart-create-data-migration-service-portal/dms-register-provider.png)

## <a name="create-an-instance-of-the-service"></a>Создание экземпляра службы
1. Щелкните **+ Create a resource** (+ Создать ресурс), чтобы создать экземпляр службы Azure Database Migration Service.

2. Выполните в Marketplace поиск по слову migration, выберите службу **Azure Database Migration Service**, а затем на экране **Azure Database Migration Service** (Azure Database Migration Service) нажмите кнопку **Create** (Create).

3. На экране **Database Migration Service** сделайте следующее. 

    - Выберите **имя службы**, которое хорошо запоминается и будет уникальным для идентификации экземпляра службы Azure Database Migration Service.
    - Выберите **подписку** Azure, в которой нужно создать экземпляр.
    - Создайте новую **сеть** с уникальным именем.
    - Выберите **расположение**, наиболее близкое к исходному или целевому серверу.
    - Дл параметра **Ценовая категория** выберите значение Basic: 1 vCore (Базовый: 1 виртуальное ядро).

    ![Создание службы миграции](media/quickstart-create-data-migration-service-portal/dms-create-service.png)
4. Нажмите кнопку **Создать**.

Через несколько секунд экземпляр службы Azure Database Migration Service будет создан и готов к использованию. Экземпляр Database Migration Service отобразится, как показано на рисунке:

![Созданная служба Migration Service](media/quickstart-create-data-migration-service-portal/dms-service-created.png)

## <a name="clean-up-resources"></a>Очистка ресурсов
Чтобы очистить ресурсы, созданные при работе с этим кратким руководством, удалите [группу ресурсов Azure](../azure-resource-manager/resource-group-overview.md).  Чтобы удалить группу ресурсов, перейдите к созданному экземпляру Azure Database Migration Service. Выберите имя **группы ресурсов** и щелкните **Удалить группу ресурсов**.  Это действие удаляет все ресурсы в группе ресурсов, а также саму группу.

## <a name="next-steps"></a>Дополнительная информация
> [!div class="nextstepaction"]
> [Миграция с SQL Server в базу данных SQL Azure](tutorial-sql-server-to-azure-sql.md)