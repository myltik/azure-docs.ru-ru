---
title: "Управление Azure DB Cosmos в Обозревателе службы хранилища Azure"
description: "Сведения об управлении Azure DB Cosmos в Обозревателе службы хранилища Azure."
Keywords: Azure Cosmos DB, Azure Storage Explorer, MongoDB
services: cosmos-db
documentationcenter: 
author: jejiang
manager: omafnan
editor: 
tags: Azure Cosmos DB
ms.assetid: 
ms.service: cosmos-db
ms.custom: Azure Cosmos DB active
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/19/2017
ms.author: Jejiang
ms.openlocfilehash: fa91630674151ac434c7f97fa2795e47bb38f16f
ms.sourcegitcommit: 901a3ad293669093e3964ed3e717227946f0af96
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/21/2017
---
# <a name="manage-azure-cosmos-db-in-azure-storage-explorer-preview"></a>Управление Azure DB Cosmos в Обозревателе службы хранилища Azure (предварительная версия)

В Обозревателе службы хранилища Azure можно управлять объектами Azure Cosmos DB и работать с данными, хранимыми процедурами и триггерами, а также другими объектами Azure, такими как хранилища BLOB-объектов и очереди. Теперь это средство можно использовать для управления всеми объектами Azure в одном месте. В данный момент обозреватель хранилища Azure поддерживает SQL <!--and MongoDB--> учетные записи. Обозреватель хранилищ Azure не работает с локальный эмулятор Azure DB Cosmos. 

В этой статье описано, как управлять Azure Cosmos DB с помощью Обозревателя службы хранилища.


## <a name="prerequisites"></a>Технические условия

Учетная запись Azure Cosmos DB API-Интерфейсы SQL <!--or MongoDB API-->. Если у вас нет учетной записи, можно создать на портале Azure, как описано в [Azure Cosmos DB: сборки SQL API веб-приложения с помощью .NET и на портале Azure](create-sql-api-dotnet.md).

## <a name="installation"></a>Установка

Скачать новейшие версии обозревателя службы хранилища Azure можно отсюда: [обозреватель хранилищ Azure](https://azure.microsoft.com/features/storage-explorer/), теперь мы поддерживаем версии для Windows, MAC и Linux.

## <a name="connect-to-an-azure-subscription"></a>Подключение к подписке Azure

1. После установки **Обозревателя службы хранилища Azure** щелкните значок **подключаемого модуля** слева, как показано на следующем рисунке.
       
   ![Значок подключаемого модуля](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/plug-in-icon.png)
 
2. Выберите **Добавить учетную запись Azure**, а затем щелкните **Войти**.

   ![Подключение к подписке Azure](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/connect-to-azure-subscription.png)

2. В диалоговом окне **Вход в Azure** нажмите кнопку **Войти**, а затем введите свои учетные данные Azure.

    ![Вход](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/sign-in.png)

3. Выберите свою подписку и нажмите кнопку **Применить**.

    ![Применить](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/apply-subscription.png)

    На панели Обозревателя будут показаны учетные записи в выбранной подписке.

    ![Список учетных записей](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/account-list.png)

    Вы успешно подключились к **учетной записи Azure Cosmos DB** в подписке Azure.

## <a name="connect-to-azure-cosmos-db-by-using-a-connection-string"></a>Подключение к Azure Cosmos DB с помощью строки подключения

Другой способ подключения к Azure Cosmos DB — с помощью строки подключения. Для подключения с помощью строки подключения выполните следующие действия.

1. Найдите элемент **Локальные и присоединенные** в дереве слева, щелкните правой кнопкой мыши **Учетные записи Azure Cosmos DB** и выберите **Подключиться к Azure Cosmos DB...**

    ![Подключение к Azure Cosmos DB с помощью строки подключения](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/connect-to-db-by-connection-string.png)

2. Выберите соответствующий **по умолчанию взаимодействие** для типа учетной записи, <!--either--> **DocumentDB** <!--or **MongoDB**-->, вставьте в вашей **строка подключения**, а затем Нажмите кнопку **ОК** для подключения учетной записи Azure Cosmos DB. Сведения о том, как получить строку подключения, см. в разделе [Получение строки подключения](https://docs.microsoft.com/azure/cosmos-db/manage-account#get-the--connection-string).

    ![Строка подключения](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/connection-string.png)

## <a name="azure-cosmos-db-resource-management"></a>Управление ресурсами Azure Cosmos DB

Для управления учетной записью Azure Cosmos DB вы можете выполнять следующие операции:
* Открытие учетной записи на портале Azure
* Добавление ресурса в список быстрого доступа
* Поиск и обновление ресурсов
* Создание и удаление баз данных
* Создание и удаление коллекций
* Создание, изменение, удаление и фильтрации документов
* Управление хранимыми процедурами, триггерами и определяемыми пользователем функциями

### <a name="quick-access-tasks"></a>Задачи для быстрого доступа

Щелкнув подписку на панели Обозревателя правой кнопкой мыши, вы сможете выполнить множество быстрых действий:

* Щелкнув правой кнопкой мыши учетную запись Azure Cosmos DB или базу данных и выбрав **Открыть на портале**, вы сможете управлять этим ресурсом в браузере на портале Azure.

     ![Открытие ресурса на портале](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/open-in-portal.png)

* Вы также можете добавить на панель **быстрого доступа** учетную запись Azure Cosmos DB, базу данных или коллекцию.
* С помощью функции **Найти здесь** вы сможете выполнять поиск по ключевым словам по выбранному пути.

    ![Найти здесь](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/search-from-here.png) 

### <a name="database-and-collection-management"></a>Управление базами данных и коллекциями
#### <a name="create-a-database"></a>Создание базы данных 
Щелкните правой кнопкой мыши учетную запись Azure Cosmos DB, выберите **Создать базу данных**, введите имя базы данных и нажмите клавишу **ВВОД**.

![Создание базы данных](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/create-database.png) 

#### <a name="delete-a-database"></a>удаление базы данных.
Щелкните правой кнопкой мыши базу данных, выберите **Удалить базу данных**и нажмите кнопку **Да** во всплывающем окне. После удаления узла базы данных учетная запись Azure Cosmos DB будет обновлена автоматически.

![Удаление базы данных 1](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/delete-database1.png)  

![Удаление базы данных 2](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/delete-database2.png) 

#### <a name="create-a-collection"></a>Создание коллекции
Щелкните правой кнопкой мыши базу данных, выберите **Создать коллекцию** и укажите параметры коллекции, такие как **Идентификатор коллекции**, **Емкость хранилища** и т. д. Нажмите кнопку **ОК** для завершения. Сведения о параметрах ключа секции см. в разделе [Схема секционирования](partition-data.md#designing-for-partitioning).

Если при создании коллекции использовался ключ секции, то после создания коллекции изменить значение этого ключа невозможно.

![Создание коллекции 1](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/create-collection.png)

![Создание коллекции 2](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/create-collection2.png) 

#### <a name="delete-a-collection"></a>Удаление коллекции
Щелкните коллекцию правой кнопкой мыши, выберите **Удалить коллекцию**, а затем нажмите кнопку **Да** во всплывающем окне. 

После удаления узла коллекции база данных будет обновлена автоматически.  

![Удаление коллекции](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/delete-collection.png) 

### <a name="document-management"></a>Управление документами

#### <a name="create-and-modify-documents"></a>Создание и изменение документов
Чтобы создать новый документ, откройте раздел **Документы** в левом окне, щелкните **Новый документ**, измените содержимое документа на правой панели и нажмите кнопку **Сохранить**. Вы также можете изменить существующий документ, а затем нажмите кнопку **Сохранить**. Чтобы отменить изменения, нажмите кнопку **Отменить**.

![Документ](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/document.png)

#### <a name="delete-a-document"></a>Удаление документа
Нажмите кнопку **Удалить**, чтобы удалить выбранный документ.
#### <a name="query-for-documents"></a>Запросы для документов
Измените фильтр документов, указав [SQL-запрос](sql-api-sql-query.md), и нажмите кнопку **Применить**.

![Фильтр](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/filter.png)

### <a name="manage-stored-procedures-triggers-and-udfs"></a>Управление хранимыми процедурами, триггерами и определяемыми пользователем функциями
* Чтобы создать хранимую процедуру, щелкните раздел **Хранимая процедура** в дереве слева правой кнопкой мыши, выберите **Создать хранимую процедуру**, введите имя процедуры слева, скрипты для этой хранимой процедуры в окне справа и нажмите кнопку **Создать**. 
* Вы также можете изменять существующие хранимые процедуры. Для этого дважды щелкните хранимую процедуру, внесите изменения и нажмите кнопку **Обновить**, чтобы сохранить изменения, или кнопку **Отменить**, чтобы отменить изменения.

![Хранимая процедура](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/stored-procedure.png)

* Операции для **триггеров** и **определяемых пользователем функций** аналогичны соответствующим операциям для **хранимых процедур**.

## <a name="next-steps"></a>Дальнейшие действия

* Чтобы узнать, как использовать Azure Cosmos DB в Обозревателе службы хранилища Azure, просмотрите это видео: [Использование Azure Cosmos DB в Обозревателе службы хранилища Azure](https://www.youtube.com/watch?v=iNIbg1DLgWo&feature=youtu.be).
* Дополнительные сведения об обозревателе службы хранилища и подключении дополнительных служб см. в статье [Приступая к работе с обозревателем службы хранилища (предварительная версия)](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

