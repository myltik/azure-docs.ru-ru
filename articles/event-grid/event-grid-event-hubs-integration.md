---
title: Интеграция службы "Сетка событий Azure" и концентраторов событий
description: Описывается, как перенести данные в хранилище данных SQL с помощью службы "Сетка событий Azure" и концентраторов событий
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 05/04/2018
ms.author: tomfitz
ms.openlocfilehash: 60857327685fca9a5f97588ab51909ce2537d68f
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2018
---
# <a name="stream-big-data-into-a-data-warehouse"></a>Потоковая передача больших данных в хранилище данных

[Сетка событий](overview.md) Azure — интеллектуальная служба маршрутизации событий, позволяющая реагировать на уведомления приложений и служб. В [примере записи концентраторов событий и службы "Сетка событий"](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) показано, как легко перенести данные из концентратора событий в хранилище данных SQL с помощью записи концентраторов событий Azure и службы "Сетка событий Azure".

![Обзор приложений](media/event-grid-event-hubs-integration/overview.png)

При передаче данных в концентратор событий в процессе записи они извлекаются из потока, а также создаются большие двоичные объекты хранилища с данными в формате Avro. Во время создания большого двоичного объекта инициируется событие. Служба "Сетка событий" передает данные о событии подписчикам. В этом случае данные события отправляются в конечную точку службы "Функции Azure". Данные события включают путь созданного большого двоичного объекта. Эта функция использует URL-адрес, чтобы получить файл и отправить его в хранилище данных.

Работая с этой статьей, вы выполните следующие задачи:

* Развертывание следующей инфраструктуры:
  * Концентратор событий с активированной записью.
  * Учетная запись хранения для файлов записи.
  * План службы приложений Azure для размещения приложения-функции.
  * Приложение-функция для обработки события.
  * SQL Server для размещения хранилища данных.
  * Хранилище данных SQL для хранения перенесенных данных.
* Создание таблицы в хранилище данных.
* Добавление кода в приложение-функцию.
* Оформление подписки на событие.
* Запуск приложения, отправляющего данные в концентратор событий.
* Просмотр перенесенных данных в хранилище данных.

## <a name="about-the-event-data"></a>Сведения о данных события

Служба "Сетка событий" распределяет данные события между подписчиками. Далее представлен пример данных события для создания файла записи. Обратите внимание, на свойство `fileUrl` объекта `data`. Приложение-функция получает это значение и использует его для получения файла записи.

```json
[
    {
        "topic": "/subscriptions/<guid>/resourcegroups/rgDataMigrationSample/providers/Microsoft.EventHub/namespaces/tfdatamigratens",
        "subject": "eventhubs/hubdatamigration",
        "eventType": "Microsoft.EventHub.CaptureFileCreated",
        "eventTime": "2017-08-31T19:12:46.0498024Z",
        "id": "14e87d03-6fbf-4bb2-9a21-92bd1281f247",
        "data": {
            "fileUrl": "https://tf0831datamigrate.blob.core.windows.net/windturbinecapture/tfdatamigratens/hubdatamigration/1/2017/08/31/19/11/45.avro",
            "fileType": "AzureBlockBlob",
            "partitionId": "1",
            "sizeInBytes": 249168,
            "eventCount": 1500,
            "firstSequenceNumber": 2400,
            "lastSequenceNumber": 3899,
            "firstEnqueueTime": "2017-08-31T19:12:14.674Z",
            "lastEnqueueTime": "2017-08-31T19:12:44.309Z"
        }
    }
]
```

## <a name="prerequisites"></a>предварительным требованиям

Для работы с этим руководством требуется:

* Подписка Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.
* [Visual Studio 2017 версии 15.3.2 или более поздней](https://www.visualstudio.com/vs/) с рабочими нагрузками для разработки классических приложений .NET, разработки Azure, разработки ASP.NET и веб-разработки, разработки Node.js и Python.
* [Пример проекта EventHubsCaptureEventGridDemo](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo), загруженный на компьютер.

## <a name="deploy-the-infrastructure"></a>Развертывание инфраструктуры

Чтобы упростить работу с этой статьей, разверните необходимую инфраструктуру с помощью шаблона Resource Manager. Чтобы просмотреть развернутые ресурсы, просмотрите [шаблон](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/EventHubsDataMigration.json). Используйте один из [поддерживаемых регионов](overview.md) в качестве расположения группы ресурсов.

Для интерфейса командной строки Azure:

```azurecli-interactive
az group create -l westcentralus -n rgDataMigrationSample

az group deployment create \
  --resource-group rgDataMigrationSample \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json \
  --parameters eventHubNamespaceName=<event-hub-namespace> eventHubName=hubdatamigration sqlServerName=<sql-server-name> sqlServerUserName=<user-name> sqlServerPassword=<password> sqlServerDatabaseName=<database-name> storageName=<unique-storage-name> functionAppName=<app-name>
```

Для PowerShell используйте команду:

```powershell
New-AzureRmResourceGroup -Name rgDataMigration -Location westcentralus

New-AzureRmResourceGroupDeployment -ResourceGroupName rgDataMigration -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json -eventHubNamespaceName <event-hub-namespace> -eventHubName hubdatamigration -sqlServerName <sql-server-name> -sqlServerUserName <user-name> -sqlServerDatabaseName <database-name> -storageName <unique-storage-name> -functionAppName <app-name>
```

При появлении запроса укажите пароль.

## <a name="create-a-table-in-sql-data-warehouse"></a>Создание таблицы в хранилище данных SQL

Добавьте таблицу в хранилище данных, выполнив сценарий [CreateDataWarehouseTable.sql](https://github.com/Azure/azure-event-hubs/blob/master/samples/e2e/EventHubsCaptureEventGridDemo/scripts/CreateDataWarehouseTable.sql). Для этого воспользуйтесь Visual Studio или редактором запросов на портале.

Далее представлен выполняемый сценарий.

```sql
CREATE TABLE [dbo].[Fact_WindTurbineMetrics] (
    [DeviceId] nvarchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL, 
    [MeasureTime] datetime NULL, 
    [GeneratedPower] float NULL, 
    [WindSpeed] float NULL, 
    [TurbineSpeed] float NULL
)
WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
```

## <a name="publish-the-azure-functions-app"></a>Публикация приложения службы "Функции Azure"

1. Откройте [пример проекта EventHubsCaptureEventGridDemo](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) в Visual Studio 2017 (версии 15.3.2 или более поздней).

1. В обозревателе решений щелкните правой кнопкой мыши **FunctionEGDWDumper** и выберите **Публиковать**.

   ![Публикация приложения-функции](media/event-grid-event-hubs-integration/publish-function-app.png)

1. Выберите **Приложение функции Azure** и **Выбрать существующее**. Нажмите кнопку **Опубликовать**.

   ![Целевое приложение-функция](media/event-grid-event-hubs-integration/pick-target.png)

1. Выберите приложение-функцию, развернутое с помощью шаблона. Нажмите кнопку **ОК**.

   ![Выбор приложения-функции](media/event-grid-event-hubs-integration/select-function-app.png)

1. После настройки профиля в Visual Studio выберите **Публиковать**.

   ![Выбор публикации](media/event-grid-event-hubs-integration/select-publish.png)

После публикации функции вы можете подписаться на событие.

## <a name="subscribe-to-the-event"></a>Оформление подписки на событие

1. Перейдите на [портал Azure](https://portal.azure.com/). Выберите группу ресурсов и приложение-функцию.

   ![Просмотр приложения-функции](media/event-grid-event-hubs-integration/view-function-app.png)

1. Выберите функцию.

   ![Выбор функции](media/event-grid-event-hubs-integration/select-function.png)

1. Выберите **Добавление подписки для службы "Сетка событий"**.

   ![Добавить подписку](media/event-grid-event-hubs-integration/add-event-grid-subscription.png)

9. Присвойте имя подписке для службы "Сетка событий". Используйте тип события **Пространства имен концентраторов событий**. Укажите значения для экземпляра пространства имен в службе "Концентраторы событий". Оставьте для конечной точки подписчика указанное значение. Нажмите кнопку **Создать**.

   ![Создание подписки](media/event-grid-event-hubs-integration/set-subscription-values.png)

## <a name="run-the-app-to-generate-data"></a>Запуск приложения для создания данных

Вы завершили настройку концентратора событий, хранилища данных SQL, приложения-функции Azure и подписки на события. Решение готово к миграции данных из концентратора событий в хранилище данных. Прежде чем запустить приложение, создающее данные для концентратора событий, настройте несколько значений.

1. На портале выберите пространство имен концентратора событий. Выберите **Строки подключения**.

   ![Выбор элемента "Строки подключения"](media/event-grid-event-hubs-integration/event-hub-connection.png)

2. Выберите **RootManageSharedAccessKey**.

   ![Выбор ключа](media/event-grid-event-hubs-integration/show-root-key.png)

3. Скопируйте значение в поле **Connection string — Primary Key** (Строка подключения — первичный ключ).

   ![Копирование ключа](media/event-grid-event-hubs-integration/copy-key.png)

4. Вернитесь к проекту Visual Studio. В проекте WindTurbineDataGenerator откройте файл **program.cs**.

5. Замените две константы. Использовать скопированное значение **EventHubConnectionString**. Используйте имя концентратора событий **hubdatamigration**.

   ```cs
   private const string EventHubConnectionString = "Endpoint=sb://demomigrationnamespace.servicebus.windows.net/...";
   private const string EventHubName = "hubdatamigration";
   ```

6. Выполните сборку решения. Запустите приложение WindTurbineGenerator.exe. Через несколько минут запросите перенесенные данные в таблице хранилища данных.

## <a name="next-steps"></a>Дополнительная информация

* Общие сведения о службе "Сетка событий" см. в разделе [Общие сведения о службе "Сетка событий Azure"](overview.md).
* Общие сведения о записи концентраторов событий см. в статье [Включение записи концентраторов событий с помощью портала Azure](../event-hubs/event-hubs-capture-enable-through-portal.md).
* Дополнительные сведения о настройке и запуске примера см. в [примере записи концентраторов событий и службы "Сетка событий"](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo).