---
title: Подключенная служба Visual Studio для Azure Cosmos DB
description: Разработчики могут легко подключать учетную запись Azure Cosmos DB и управлять ресурсами через подключенные службы Visual Studio
services: cosmos-db
documentationcenter: ''
author: Jejiang
manager: kfile
ms.assetid: ''
ms.service: cosmos-db
ms.custom: Azure Cosmos DB
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 09/19/2017
ms.author: jejiang
ms.openlocfilehash: d496d80f1d87b234e0c94333b01ad2c23cc037ab
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/11/2018
---
# <a name="azure-cosmos-db-visual-studio-connected-service-preview"></a>Azure Cosmos DB: подключенная служба Visual Studio (предварительная версия)

Подключенные службы Visual Studio позволяют разработчикам легко подключить учетную запись Azure Cosmos DB и управлять своими ресурсами.

Также можно использовать обозреватель данных в подключенной службе для создания хранимых процедур, определяемых пользователем функций и триггеров, чтобы реализовать бизнес-логику на стороне сервера. Обозреватель данных отображает все встроенные возможности программного доступа к данным, доступные в API, а также обеспечивает быстрый доступ к данным.

## <a name="prerequisites"></a>предварительным требованиям

Убедитесь, что у вас есть указанные ниже компоненты.

* Активная учетная запись Azure. Если у вас ее нет, зарегистрируйте [бесплатную учетную запись](https://azure.microsoft.com/free/). 
* Учетная запись Azure Cosmos DB. Если у вас еще нет этой учетной записи, см. инструкции по [ созданию учетной записи Azure Cosmos DB с помощью портала Azure](create-sql-api-dotnet.md). Или см. инструкции по [созданию учетной записи Azure Cosmos DB в средстве подключенной службы](#Create-an-Azure-Cosmo-DB-account-in-Connected-Service-tool). 
* Если вы хотите использовать локальную среду для разработки, можно использовать [эмулятор Azure Cosmos DB](local-emulator.md). Среда эмулирует службу Azure Cosmos DB.
* [Visual Studio](http://www.visualstudio.com/).
* Последняя версия подключенной службы Azure Cosmos DB. Скачайте подключенную службу Azure Cosmos DB из Visual Studio Marketplace, как показано на следующем снимке экрана. Откройте **Visual Studio** на компьютере. В меню **Средства** щелкните **Расширения и обновление...**, а затем последовательно выберите **В сети** / **Visual Studio Marketplace**. Для поиска службы введите запрос **cosmosdb**.

    Можно также установить подключенную службу Azure Cosmos DB из [Visual Studio Marketplace](https://go.microsoft.com/fwlink/?linkid=858709).

    ![Снимок экрана: скачивание подключенной службы](./media/connected-service/connected-service-downloadbits.png) 

Когда вы скачаете расширение подключенной службы Azure Cosmos DB, закройте Visual Studio, чтобы установить расширение.

## <a id="SetupVS"></a>Настройка решения Visual Studio
1. Откройте **Visual Studio** у себя на компьютере.
2. В меню **Файл** выберите пункт **Создать**, а затем — **Проект**.
3. В диалоговом окне **Новый проект** выберите **Visual C#** / **Консольное приложение (.NET Framework)** или **WPF App (.NET Framework)**, а затем укажите имя проекта и нажмите кнопку **ОК**.

    ![Снимок экрана: диалоговое окно «Новый проект»](./media/connected-service/connected-service-new-project.png)
    
## <a name="add-connected-service-and-add-account"></a>добавлять подключенную службу и учетную запись;
1. В обозревателе решений щелкните правой кнопкой мыши узел проекта, а затем выберите **Добавить** / **Подключенная служба**. Или щелкните правой кнопкой мыши меню **Проект** и выберите **Добавить подключенные службы**.

    ![Снимок экрана: окно "Добавление подключенной службы"](./media/connected-service/connected-service-add-connectedservice-rightclick.png)
2. На странице подключенных службы выберите **Подключенные службы** / **Azure Cosmos DB**, чтобы открыть страницу **Azure Cosmos DB**.

    ![Снимок экрана: окно "Azure Cosmos DB"](./media/connected-service/connected-service-choose-azure-cosmosdb.png)
3. Щелкните стрелку вниз, чтобы войти в Azure Cosmos DB или добавить учетную запись. Когда вы войдете, все учетные записи Azure Cosmos DB отобразятся в пустой области. Выберите одну учетную запись Azure Cosmos DB для добавления в проект.

    ![Снимок экрана: окна входа и списка учетных записей базы данных](./media/connected-service/connected-service-add-db-account.png)
4. Когда вы добавите учетную запись Azure Cosmos DB, папка подключенной службы учетной записи Azure Cosmos DB будет добавлена в проект. Можно добавить несколько учетных записей Azure Cosmos DB, повторив шаги 1–3.

    ![Снимок экрана: окно с папкой подключенной службы](./media/connected-service/connected-service-add-connectedservice-folder.png)

5. Добавив подключенную службу Azure DB Cosmos, измените проект, чтобы предоставить доступ к базе данных Azure Cosmos DB одним из следующих способов:

    * Установите пакеты NuGet, необходимые для клиента Azure Cosmos DB. Вы увидите их в файле конфигурации пакетов. 

        ![Новый файл конфигурации пакетов Azure Cosmos DB](./media/connected-service/connected-service-packages-config.png)   
    
    * Добавьте универсальный код ресурса (URI) и ключ подключения к Azure Cosmos DB в файл конфигурации проекта. В нашем примере это файл App.config. 

        ![Новый файл конфигурации приложения Azure Cosmos DB](./media/connected-service/connected-service-app-config.png) 

## <a name="open-azure-cosmos-db-explorer"></a>Открытие Azure Cosmos DB Explorer
1. Щелкните узел проекта правой кнопкой мыши и выберите **Открыть Cosmos DB Explorer...**

    ![Снимок экрана: окно, в котором открывается обозреватель данных](./media/connected-service/connected-service-right-click-open-data-exporer.png)
2. На странице **Выбор учетной записи Cosmos DB** щелкните раскрывающийся список, чтобы выбрать учетную запись Azure Cosmos DB.

    ![Снимок экрана: окно "Добавленная подключенная служба"](./media/connected-service/connected-service-open-explorer.png)
3. Нажмите кнопку **Открыть**, чтобы открыть окно обозревателя данных.

## <a id="Create-an-Azure-Cosmo-DB-account-in-Connected-Service-tool"></a>Создание учетной записи Azure Cosmos DB в средстве подключенной службы
1. На странице подключенной службы в левом нижнем углу панели нажмите кнопку **Создать учетную запись Cosmos DB**, чтобы открыть страницу **Создание учетной записи Cosmos DB**.

    ![Снимок экрана: окно, в котором открывается страница "Создание учетной записи Cosmos DB"](./media/connected-service/connected-service-click-new-db-account.png)
2. На странице **Создание учетной записи** укажите необходимую конфигурацию учетной записи Azure Cosmos DB.

    Заполните поля страницы **Создание учетной записи**, используя для справки сведения на следующем снимке экрана. 
 
    ![Страница "Создание учетной записи Azure Cosmos DB"](./media/connected-service/connected-service-create-new-account.png)        
3. Щелкните **Создать** , чтобы создать учетную запись.

## <a name="use-data-explorer"></a>использовать обозреватель данных.

В обозревателе данных можно выполнять следующие задачи:
* создавать и удалять базы данных;
* создавать и удалять коллекции;
* создавать, удалять и фильтровать документы;
* создавать и удалять хранимые процедуры;
* создавать и удалять триггеры и определяемые пользователем функции для реализации бизнес-логики на стороне сервера. 

![Страница "Создание учетной записи Azure Cosmos DB"](./media/connected-service/connected-service-dataexplorerui.png)

## <a name="demo"></a>Демонстрация

Просмотрите это видео, чтобы узнать, как использовать подключенную службу Azure Cosmos DB в Visual Studio: [Cosmos DB Connected Service in Visual Studio](https://go.microsoft.com/fwlink/?linkid=858711) (Подключенная служба Cosmos DB в Visual Studio)

## <a name="next-steps"></a>Дополнительная информация
Изучив эту статью, вы научились выполнять следующие задачи:

> [!div class="checklist"]
> * создание учетной записи Azure Cosmos DB;
> * добавлять подключенную службу и учетную запись;
> * открывать Azure Cosmos DB Explorer;
> * использовать обозреватель данных.

Теперь, когда подключенные службы работают в учетной записи Azure Cosmos DB, ознакомьтесь с одним из руководств, чтобы начать разработку решения:

* [Разработка с помощью API SQL на .NET](tutorial-develop-sql-api-dotnet.md).
* [Azure Cosmos DB. Приступая к работе с API SQL](sql-api-get-started.md).
* Хотите выполнять проверку масштабирования и производительности с помощью Azure Cosmos DB? См. инструкции по [проверке производительности и масштабирования с помощью Azure Cosmos DB](performance-testing.md).
* Узнайте, как выполнять [мониторинг учетной записи Azure Cosmos DB](monitor-accounts.md).

