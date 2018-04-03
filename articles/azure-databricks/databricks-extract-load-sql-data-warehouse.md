---
title: Руководство. Выполнение операций извлечения, преобразования и загрузки с помощью Azure Databricks | Документация Майкрософт
description: Узнайте, как извлечь данные из Data Lake Store в Azure Databricks, преобразовывать их, а затем загрузить в хранилище данных SQL Azure.
services: azure-databricks
documentationcenter: ''
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: azure-databricks
ms.custom: mvc
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: Active
ms.date: 03/23/2018
ms.author: nitinme
ms.openlocfilehash: c3aa87f2c74175d1b61a8db6a9c7a0318a408658
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2018
---
# <a name="tutorial-extract-transform-and-load-data-using-azure-databricks"></a>Руководство. Извлечение, преобразование и загрузка данных с помощью Azure Databricks

В этом руководстве рассматривается выполнение операций извлечения, преобразования и загрузки данных с помощью Azure Databricks. Мы извлечем данные из Azure Data Lake Store в Azure Databricks, выполним преобразование данных в Azure Databricks, а затем загрузим преобразованные данные в хранилище данных SQL Azure. 

Для действий, описанных в этом руководстве, используется соединитель хранилища данных SQL для Azure Databricks, позволяющий передавать данные в Azure Databricks. Этот соединитель, в свою очередь, использует хранилище BLOB-объектов Azure как временное хранилище для данных, передаваемых между кластером Azure Databricks и хранилищем данных SQL Azure.

На следующем рисунке показан поток в приложении.

![Azure Databricks с Data Lake Store и хранилищем данных SQL](./media/databricks-extract-load-sql-data-warehouse/databricks-extract-transform-load-sql-datawarehouse.png "Azure Databricks с Data Lake Store и хранилищем данных SQL")

В рамках этого руководства рассматриваются следующие задачи: 

> [!div class="checklist"]
> * Создание рабочей области Azure Databricks
> * Создание кластера Spark в Azure Databricks.
> * Создание учетной записи хранения озера данных Azure
> * Отправка данных в Azure Data Lake Store.
> * Создание записной книжки в Azure Databricks.
> * Извлечение данных из Data Lake Store.
> * Преобразование данных в Azure Databricks.
> * Загрузка данных в хранилище данных Azure SQL

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>предварительным требованиям

Прежде чем приступить к изучению этого руководства, убедитесь, что выполнены следующие требования.
- Создайте хранилище данных SQL Azure, создайте правило брандмауэра на уровне сервера и подключитесь к серверу с правами администратора. Инструкции см. в статье [Краткое руководство. Создание хранилища данных SQL Azure на портале Azure и отправка запросов к этому хранилищу данных](../sql-data-warehouse/create-data-warehouse-portal.md).
- Создайте главный ключ базы данных для хранилища данных SQL Azure. Инструкции см. в статье [Создание главного ключа базы данных](https://docs.microsoft.com/sql/relational-databases/security/encryption/create-a-database-master-key).
- Создайте учетную запись хранилища BLOB-объектов Azure, содержащую контейнер. Также получите ключ для доступа к учетной записи хранения. Инструкции см. в статье [Краткое руководство по передаче, скачиванию и составлению списка больших двоичных объектов с помощью портала Azure](../storage/blobs/storage-quickstart-blobs-portal.md).

## <a name="log-in-to-the-azure-portal"></a>Войдите на портал Azure.

Войдите на [портал Azure](https://portal.azure.com/).

## <a name="create-an-azure-databricks-workspace"></a>Создание рабочей области Azure Databricks

В этом разделе вы создадите рабочую область Azure Databricks с помощью портала Azure. 

1. На портале Azure выберите **Создать ресурс** > **Data + Analytics** (Данные и аналитика) > **Azure Databricks**. 

    ![Databricks на портале Azure](./media/databricks-extract-load-sql-data-warehouse/azure-databricks-on-portal.png "Databricks on Azure portal")

3. В разделе **службы Azure Databricks** укажите значения для создания рабочей области Databricks.

    ![Создание рабочей области Azure Databricks](./media/databricks-extract-load-sql-data-warehouse/create-databricks-workspace.png "Create an Azure Databricks workspace")

    Укажите следующие значения. 
     
    |Свойство  |ОПИСАНИЕ  |
    |---------|---------|
    |**Имя рабочей области**     | Укажите имя рабочей области Databricks.        |
    |**Подписка**     | Выберите подписку Azure в раскрывающемся списке.        |
    |**Группа ресурсов**     | Укажите, следует ли создать новую группу ресурсов или использовать имеющуюся. Группа ресурсов — это контейнер, содержащий связанные ресурсы для решения Azure. Дополнительные сведения см. в [обзоре группы ресурсов Azure](../azure-resource-manager/resource-group-overview.md). |
    |**Местоположение.**     | Выберите регион **Восточная часть США 2**. Другие доступные регионы см. в статье о [доступности служб Azure по регионам](https://azure.microsoft.com/regions/services/).        |
    |**Ценовая категория**     |  Вы можете выбрать уровень **Стандартный** или **Премиум**. Дополнительные сведения об этих ценовых категориях см. на [странице цен на Databricks](https://azure.microsoft.com/pricing/details/databricks/).       |

    Установите флажок **Закрепить на панели мониторинга** и щелкните **Создать**.

4. Создание учетной записи займет несколько минут. Во время создания учетной записи на портале с правой стороны отображается плитка **Submitting deployment for Azure Databricks** (Идет отправка развертывания для Databricks). Возможно, вам потребуется прокрутить панель мониторинга, чтобы увидеть эту плитку. В верхней части экрана также будет отображаться индикатор хода выполнения. Следить за выполнением можно с помощью любого из этих элементов.

    ![Плитка развертывания Databricks](./media/databricks-extract-load-sql-data-warehouse/databricks-deployment-tile.png "Databricks deployment tile")

## <a name="create-a-spark-cluster-in-databricks"></a>Создание кластера Spark в Databricks

1. На портале Azure перейдите к созданной рабочей области Databricks, а затем выберите **Launch Workspace** (Запуск рабочей области).

2. Вы будете перенаправлены на портал Azure Databricks. На портале выберите **Кластер**.

    ![Databricks в Azure](./media/databricks-extract-load-sql-data-warehouse/databricks-on-azure.png "Databricks on Azure")

3. На странице **создания кластера** укажите значения для создания кластера.

    ![Создание кластера Databricks Spark в Azure](./media/databricks-extract-load-sql-data-warehouse/create-databricks-spark-cluster.png "Create Databricks Spark cluster on Azure")

    Для всех остальных параметров, кроме следующих, примите значения по умолчанию:

    * Введите имя кластера.
    * В рамках этой статьи создайте кластер со средой выполнения **4.0**. 
    * Убедитесь, что установлен флажок **Terminate after ___ minutes of activity** (Завершить работу после ___ минут отсутствия активности). Укажите длительность (в минутах) для завершения работы кластера, если тот не используется.
    
    Выберите **Create cluster** (Создать кластер). После запуска кластера можно вложить записные книжки в кластер и запустить задания Spark.

## <a name="create-an-azure-data-lake-store-account"></a>Создание учетной записи хранения озера данных Azure

В этом разделе мы создадим учетную запись Azure Data Lake Store и свяжем ее с субъектом-службой Azure Active Directory. Далее в этом руководстве этот субъект-служба будет использоваться в Azure Databricks для получения доступа к Azure Data Lake Store. 

1. На [портале Azure](https://portal.azure.com) последовательно выберите **Создать ресурс** > **Хранилище** > **Data Lake Store**.
3. В колонке **Создать Data Lake Store** задайте значения, как показано на следующем снимке экрана:
   
    ![Создание учетной записи Azure Data Lake Store](./media/databricks-extract-load-sql-data-warehouse/create-new-datalake-store.png "Создание учетной записи Azure Data Lake")

    Укажите следующие значения. 
     
    |Свойство  |ОПИСАНИЕ  |
    |---------|---------|
    |**Имя**     | Введите уникальное имя учетной записи Data Lake Store.        |
    |**Подписка**     | Выберите подписку Azure в раскрывающемся списке.        |
    |**Группа ресурсов**     | В этом руководстве выберите ту же группу ресурсов, которая использовалась для создания рабочей области Azure Databricks.  |
    |**Местоположение.**     | Выберите регион **Восточная часть США 2**.  |
    |**Тарифный пакет**     |  Выберите **Оплата по мере использования**. |
    | **Параметры шифрования** | Оставьте параметры по умолчанию. |

    Установите флажок **Закрепить на панели мониторинга** и щелкните **Создать**.

Теперь создадим субъект-службу Azure Active Directory и свяжем его с созданной учетной записью Data Lake Store.

### <a name="create-an-azure-active-directory-service-principal"></a>Создание субъекта-службы Azure Active Directory
   
1. На [портале Azure](https://portal.azure.com) выберите **Все службы**, а потом найдите **Azure Active Directory**.

2. Щелкните **Регистрация приложений**.

   ![Пункт "Регистрация приложений"](./media/databricks-extract-load-sql-data-warehouse/select-app-registrations.png)

3. Выберите **Регистрация нового приложения**.

   ![Действие "Добавить приложение"](./media/databricks-extract-load-sql-data-warehouse/select-add-app.png)

4. Укажите имя и URL-адрес для приложения. В качестве типа создаваемого приложения выберите **Веб-приложение или API**. Предоставьте URL-адрес для входа, а затем нажмите кнопку **Создать**.

   ![указание имени приложения](./media/databricks-extract-load-sql-data-warehouse/create-app.png)

Чтобы получить доступ к учетной записи Data Lake Store из Azure Databricks, необходимо иметь следующие значения созданного субъекта-службы Azure Active Directory:
- Идентификатор приложения
- ключ проверки подлинности;
- Tenant ID

В следующих разделах мы получим значения для созданного ранее субъекта-службы Azure Active Directory.

### <a name="get-application-id-and-authentication-key-for-the-service-principal"></a>Получение идентификатора приложения и ключа проверки подлинности для субъекта-службы

При программном входе необходимо указывать идентификатор приложения и ключ проверки подлинности. Получить эти значения можно следующим образом.

1. В Azure Active Directory в разделе **Регистрация приложений** выберите нужное приложение.

   ![Выбор приложения](./media/databricks-extract-load-sql-data-warehouse/select-app.png)

2. Скопируйте **идентификатор приложения** и сохраните его в коде приложения. Некоторые [примеры приложений](#log-in-as-the-application) ссылаются на это значение как на идентификатор клиента.

   ![Идентификатор клиента](./media/databricks-extract-load-sql-data-warehouse/copy-app-id.png)

3. Чтобы создать ключ проверки подлинности, щелкните **Параметры**.

   ![Выбор элемента "Параметры"](./media/databricks-extract-load-sql-data-warehouse/select-settings.png)

4. Чтобы создать ключ проверки подлинности, щелкните **Ключи**.

   ![Пункт "Ключи"](./media/databricks-extract-load-sql-data-warehouse/select-keys.png)

5. Введите описание и срок действия ключа. Затем нажмите кнопку **Сохранить**.

   ![Сохранение ключа](./media/databricks-extract-load-sql-data-warehouse/save-key.png)

   После этого отобразится значение ключа. Это значение нельзя будет получить позже, поэтому скопируйте его сразу. Это значение необходимо предоставить вместе с идентификатором приложения для входа от имени приложения. Сохраните значение ключа, чтобы приложение могло получить к нему доступ.

   ![сохраненный ключ](./media/databricks-extract-load-sql-data-warehouse/copy-key.png)

### <a name="get-tenant-id"></a>Получение идентификатора клиента

При программном входе необходимо передать идентификатор клиента в запросе на проверку подлинности.

1. Выберите **Azure Active Directory**.

   ![Выбор Azure Active Directory](./media/databricks-extract-load-sql-data-warehouse/select-active-directory.png)

1. Чтобы получить идентификатор клиента, щелкните **Свойства** для клиента Azure AD.

   ![Выбор свойств Azure AD](./media/databricks-extract-load-sql-data-warehouse/select-ad-properties.png)

1. Скопируйте **идентификатор каталога**. Это и есть ваш идентификатор клиента.

   ![tenant ID](./media/databricks-extract-load-sql-data-warehouse/copy-directory-id.png) 

### <a name="associate-service-principal-with-azure-data-lake-store"></a>Связывание субъекта-службы с Azure Data Lake Store

В этом разделе учетная запись Azure Data Lake Store связывается с созданным субъектом-службой Azure Active Directory. Это гарантирует, что вы сможете получить доступ к учетной записи Data Lake Store из Azure Databricks.

1. На [портале Azure](https://portal.azure.com) выберите созданную учетную запись Data Lake Store.

2. В левой области выберите **Управление доступом** > **Добавить**.

    ![Добавление доступа к Data Lake Store](./media/databricks-extract-load-sql-data-warehouse/add-adls-access.png "Добавление доступа к Data Lake Store")

3. В разделе **Добавление разрешений** выберите роль, которая будет присвоена субъекту-службе. Для работы с этим руководством выберите **Владелец**. В поле **Назначение доступа** выберите **Пользователь, группа или приложение Azure AD**. В поле **Выбор** введите имя созданного субъекта-службы, чтобы сократить число субъектов-служб, из которых можно выбирать.

    ![Выбор субъекта-службы](./media/databricks-extract-load-sql-data-warehouse/select-service-principal.png "Выбор субъекта-службы")

    Выберите созданный ранее субъект-службу, а затем нажмите кнопку **Сохранить**. Теперь субъект-служба связан с учетной записью Azure Data Lake Store.

## <a name="upload-data-to-data-lake-store"></a>Передача данных в хранилище озера данных

В этом разделе мы отправим пример файла данных в Data Lake Store. Этот файл позже будет использоваться в Azure Databricks для выполнения некоторых преобразований. Пример данных (**small_radio_json.json**), используемый в этом руководстве, доступен в репозитории [GitHub](https://github.com/Azure/usql/blob/master/Examples/Samples/Data/json/radiowebsite/small_radio_json.json).

1. На [портале Azure](https://portal.azure.com) выберите созданную учетную запись Data Lake Store.

2. На вкладке **Обзор** щелкните **Обозреватель данных**.

    ![Открытие обозревателя данных](./media/databricks-extract-load-sql-data-warehouse/open-data-explorer.png "Открытие обозревателя данных")

3. В обозревателе данных щелкните **Отправить**.

    ![Параметр отправки](./media/databricks-extract-load-sql-data-warehouse/upload-to-data-lake-store.png "Параметр отправки")

4. В окне **Отправка файлов** перейдите к расположению примера файла данных, а затем нажмите кнопку **Добавить выбранные файлы**.

    ![Параметр отправки](./media/databricks-extract-load-sql-data-warehouse/upload-data.png "Параметр отправки")

5. В этом руководстве файл данных был отправлен в корневой каталог Data Lake Store. Таким образом, теперь файл доступен в каталоге `adl://<YOUR_DATA_LAKE_STORE_ACCOUNT_NAME>.azuredatalakestore.net/small_radio_json.json`.

## <a name="extract-data-from-data-lake-store"></a>Извлечение данных из Data Lake Store

В этом разделе мы создадим записную книжку в рабочей области Azure Databricks, а затем выполним фрагменты кода для извлечения данных из Data Lake Store в Azure Databricks.

1. На [портале Azure](https://portal.azure.com) перейдите к созданной рабочей области Azure Databricks, а затем выберите **Launch Workspace** (Запуск рабочей области).

2. В левой области выберите **Рабочая область**. В раскрывающемся списке **Рабочая область** выберите **Создать** > **Notebook** (Записная книжка).

    ![Создание записной книжки в Databricks](./media/databricks-extract-load-sql-data-warehouse/databricks-create-notebook.png "Create notebook in Databricks")

2. В диалоговом окне **создания записной книжки** введите имя записной книжки. Выберите **Scala** в качестве языка, а затем выберите созданный ранее кластер Spark.

    ![Создание записной книжки в Databricks](./media/databricks-extract-load-sql-data-warehouse/databricks-notebook-details.png "Create notebook in Databricks")

    Нажмите кнопку **Создать**.

3. Добавьте следующий фрагмент кода в пустую ячейку кода и замените значения заполнителей значениями, сохраненными ранее для субъекта-службы Azure Active Directory.

        spark.conf.set("dfs.adls.oauth2.access.token.provider.type", "ClientCredential")
        spark.conf.set("dfs.adls.oauth2.client.id", "<APPLICATION-ID>")
        spark.conf.set("dfs.adls.oauth2.credential", "<AUTHENTICATION-KEY>")
        spark.conf.set("dfs.adls.oauth2.refresh.url", "https://login.microsoftonline.com/<TENANT-ID>/oauth2/token")

    Чтобы выполнить ячейку кода, нажмите клавиши **SHIFT+ВВОД**.

4. Теперь можно загрузить пример JSON-файла в Data Lake Store в виде кадра данных в Azure Databricks. Вставьте следующий фрагмент кода в новую ячейку кода, замените значение заполнителя и нажмите клавиши **SHIFT+ВВОД**.

        val df = spark.read.json("adl://<DATA LAKE STORE NAME>.azuredatalakestore.net/small_radio_json.json")

5. Запустите следующий фрагмент кода, чтобы просмотреть содержимое кадра данных.

        df.show()

    Должен отобразиться результат, аналогичный приведенному ниже фрагменту кода.

        +---------------------+---------+---------+------+-------------+----------+---------+-------+--------------------+------+--------+-------------+---------+--------------------+------+-------------+------+
        |               artist|     auth|firstName|gender|itemInSession|  lastName|   length|  level|            location|method|    page| registration|sessionId|                song|status|           ts|userId|
        +---------------------+---------+---------+------+-------------+----------+---------+-------+--------------------+------+--------+-------------+---------+--------------------+------+-------------+------+
        | El Arrebato         |Logged In| Annalyse|     F|            2|Montgomery|234.57914| free  |  Killeen-Temple, TX|   PUT|NextSong|1384448062332|     1879|Quiero Quererte Q...|   200|1409318650332|   309|
        | Creedence Clearwa...|Logged In|   Dylann|     M|            9|    Thomas|340.87138| paid  |       Anchorage, AK|   PUT|NextSong|1400723739332|       10|        Born To Move|   200|1409318653332|    11|
        | Gorillaz            |Logged In|     Liam|     M|           11|     Watts|246.17751| paid  |New York-Newark-J...|   PUT|NextSong|1406279422332|     2047|                DARE|   200|1409318685332|   201|
        ...
        ...

Вы извлекли данные из Azure Data Lake Store в Azure Databricks.

## <a name="transform-data-in-azure-databricks"></a>Преобразование данных в Azure Databricks

Пример необработанных данных **small_radio_json.json** содержит сведения о слушателях радиостанции и имеет множество столбцов. В этом разделе мы преобразуем данные, чтобы извлечь только определенные столбцы из набора данных. 

1. Сначала извлеките только столбцы *firstName*, *lastName*, *gender*, *location* и *level* из кадра данных, который был создан ранее.

        val specificColumnsDf = df.select("firstname", "lastname", "gender", "location", "level")

    Вывод должен выглядеть также, как показано в следующем фрагменте кода:

        +---------+----------+------+--------------------+-----+
        |firstname|  lastname|gender|            location|level|
        +---------+----------+------+--------------------+-----+
        | Annalyse|Montgomery|     F|  Killeen-Temple, TX| free|
        |   Dylann|    Thomas|     M|       Anchorage, AK| paid|
        |     Liam|     Watts|     M|New York-Newark-J...| paid|
        |     Tess|  Townsend|     F|Nashville-Davidso...| free|
        |  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
        |     Alan|     Morse|     M|Chicago-Napervill...| paid|
        |Gabriella|   Shelton|     F|San Jose-Sunnyval...| free|
        |   Elijah|  Williams|     M|Detroit-Warren-De...| paid|
        |  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
        |     Tess|  Townsend|     F|Nashville-Davidso...| free|
        |     Alan|     Morse|     M|Chicago-Napervill...| paid|
        |     Liam|     Watts|     M|New York-Newark-J...| paid|
        |     Liam|     Watts|     M|New York-Newark-J...| paid|
        |   Dylann|    Thomas|     M|       Anchorage, AK| paid|
        |     Alan|     Morse|     M|Chicago-Napervill...| paid|
        |   Elijah|  Williams|     M|Detroit-Warren-De...| paid|
        |  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
        |     Alan|     Morse|     M|Chicago-Napervill...| paid|
        |   Dylann|    Thomas|     M|       Anchorage, AK| paid|
        |  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
        +---------+----------+------+--------------------+-----+

2.  Эти данные можно еще преобразовывать, переименовав столбец **level** на **subscription_type**.

        val renamedColumnsDF = specificColumnsDf.withColumnRenamed("level", "subscription_type")
        renamedColumnsDF.show()

    Вывод должен выглядеть также, как показано в следующем фрагменте кода.

        +---------+----------+------+--------------------+-----------------+
        |firstname|  lastname|gender|            location|subscription_type|
        +---------+----------+------+--------------------+-----------------+
        | Annalyse|Montgomery|     F|  Killeen-Temple, TX|             free|
        |   Dylann|    Thomas|     M|       Anchorage, AK|             paid|
        |     Liam|     Watts|     M|New York-Newark-J...|             paid|
        |     Tess|  Townsend|     F|Nashville-Davidso...|             free|
        |  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
        |     Alan|     Morse|     M|Chicago-Napervill...|             paid|
        |Gabriella|   Shelton|     F|San Jose-Sunnyval...|             free|
        |   Elijah|  Williams|     M|Detroit-Warren-De...|             paid|
        |  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
        |     Tess|  Townsend|     F|Nashville-Davidso...|             free|
        |     Alan|     Morse|     M|Chicago-Napervill...|             paid|
        |     Liam|     Watts|     M|New York-Newark-J...|             paid|
        |     Liam|     Watts|     M|New York-Newark-J...|             paid|
        |   Dylann|    Thomas|     M|       Anchorage, AK|             paid|
        |     Alan|     Morse|     M|Chicago-Napervill...|             paid|
        |   Elijah|  Williams|     M|Detroit-Warren-De...|             paid|
        |  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
        |     Alan|     Morse|     M|Chicago-Napervill...|             paid|
        |   Dylann|    Thomas|     M|       Anchorage, AK|             paid|
        |  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
        +---------+----------+------+--------------------+-----------------+

## <a name="load-data-into-azure-sql-data-warehouse"></a>Загрузка данных в хранилище данных Azure SQL

В этом разделе преобразованные данные отправляются в хранилище данных SQL Azure. С помощью соединителя хранилища данных SQL Azure для Azure Databricks можно напрямую отправить кадр данных в виде таблицы в хранилище данных SQL.

Как упоминалось ранее, соединитель хранилища данных SQL использует хранилище BLOB-объектов Azure в качестве временного хранилища для передачи данных между Azure Databricks и хранилищем данных SQL Azure. Таким образом сначала нужно предоставить конфигурацию для подключения к учетной записи хранения. Вы уже должны были создать учетную запись, выполняя предварительные требования для этой статьи.

1. Предоставьте конфигурацию для получения доступа к учетной записи хранения Azure из Azure Databricks.

        val blobStorage = "<STORAGE ACCOUNT NAME>.blob.core.windows.net"
        val blobContainer = "<CONTAINER NAME>"
        val blobAccessKey =  "<ACCESS KEY>"

2. Укажите временную папку, которая будет использоваться при перемещении данных между Azure Databricks и хранилищем данных SQL Azure.

        val tempDir = "wasbs://" + blobContainer + "@" + blobStorage +"/tempDirs"

3. Запустите следующий фрагмент кода, чтобы сохранить ключ доступа к хранилищу BLOB-объектов Azure в конфигурации. Благодаря этому вам не придется хранить ключ доступа в записной книжке в виде обычного текста.

        val acntInfo = "fs.azure.account.key."+ blobStorage
        sc.hadoopConfiguration.set(acntInfo, blobAccessKey)

4. Укажите значения для подключения к экземпляру хранилища данных SQL Azure. Вы должны были создать хранилище данных SQL, выполняя предварительные требования для этой статьи.

        //SQL Data Warehouse related settings
        val dwDatabase = "<DATABASE NAME>"
        val dwServer = "<DATABASE SERVER NAME>" 
        val dwUser = "<USER NAME>"
        val dwPass = "<PASSWORD>"
        val dwJdbcPort =  "1433"
        val dwJdbcExtraOptions = "encrypt=true;trustServerCertificate=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;"
        val sqlDwUrl = "jdbc:sqlserver://" + dwServer + ".database.windows.net:" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass + ";$dwJdbcExtraOptions"
        val sqlDwUrlSmall = "jdbc:sqlserver://" + dwServer + ".database.windows.net:" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass

5. Выполните следующий фрагмент кода, чтобы загрузить преобразованный кадр данных **renamedColumnsDF** в качестве таблицы в хранилище данных SQL. Этот фрагмент кода создает таблицу с именем **SampleTable** в базе данных SQL.

        spark.conf.set(
          "spark.sql.parquet.writeLegacyFormat",
          "true")
        
        renamedColumnsDF.write
            .format("com.databricks.spark.sqldw")
            .option("url", sqlDwUrlSmall) 
            .option("dbtable", "SampleTable")
            .option( "forward_spark_azure_storage_credentials","True")
            .option("tempdir", tempDir)
            .mode("overwrite")
            .save()

6. Подключитесь к базе данных SQL и убедитесь, что вы видите таблицу **SampleTable**.

    ![Проверка примера таблицы](./media/databricks-extract-load-sql-data-warehouse/verify-sample-table.png "Проверка примера таблицы")

7. Выполните запрос SELECT, чтобы проверить содержимое таблицы. В ней должны быть те же данные, что и в кадре данных **renamedColumnsDF**.

    ![Проверка содержимого примера таблицы](./media/databricks-extract-load-sql-data-warehouse/verify-sample-table-content.png "Проверка содержимого примера таблицы")

## <a name="clean-up-resources"></a>Очистка ресурсов

После выполнения заданий из этого руководства вы можете завершить работу кластера. Для этого в рабочей области Azure Databricks на левой панели выберите **Кластеры**. Для кластера, работу которого необходимо завершить, переместите указатель мыши на многоточие в столбце **Actions** (Действия) и выберите значок **Завершить**.

![Завершение работы кластера Databricks](./media/databricks-extract-load-sql-data-warehouse/terminate-databricks-cluster.png "Stop a Databricks cluster")

Если не завершить работу кластера вручную, это можно сделать автоматически, выбрав флажок **Terminate after __ minutes of inactivity** (Завершить работу после __ минут бездействия) во время создания кластера. В этом случае работа кластера завершается автоматически, если кластер был неактивным в течение определенного времени.

## <a name="next-steps"></a>Дополнительная информация 
Из этого руководства вы узнали, как выполнить следующие задачи:

> [!div class="checklist"]
> * Создание рабочей области Azure Databricks
> * Создание кластера Spark в Azure Databricks.
> * Создание учетной записи хранения озера данных Azure
> * Отправка данных в Azure Data Lake Store.
> * Создание записной книжки в Azure Databricks.
> * Извлечение данных из Data Lake Store.
> * Преобразование данных в Azure Databricks.
> * Загрузка данных в хранилище данных Azure SQL

Перейдите к следующему руководству, чтобы узнать о потоковой передачи данных в реальном времени в Azure Databricks с помощью концентраторов событий Azure.

> [!div class="nextstepaction"]
>[Потоковая передача данных в Azure Databricks при помощи службы "Концентраторы событий"](databricks-stream-from-eventhubs.md)
