---
title: Руководство по Power BI для соединителя Azure Cosmos DB | Документация Майкрософт
description: Используйте это руководство по Power BI для импорта JSON, создания информативных отчетов и визуализации данных с помощью соединителя Azure Cosmos DB и Power BI.
keywords: учебник по Power BI, визуализация данных, соединитель Power BI
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 04/19/2018
ms.author: sngun
ms.openlocfilehash: 67ea7a9ea1a1be4fd0780f8b8ce22f1a133615e0
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34615875"
---
# <a name="power-bi-tutorial-for-azure-cosmos-db-visualize-data-using-the-power-bi-connector"></a>Руководство по Power BI для Azure Cosmos DB: визуализация данных с помощью соединителя Power BI
[PowerBI.com](https://powerbi.microsoft.com/) — это веб-служба, с помощью которой можно создавать и совместно использовать панели мониторинга и отчеты с важными данными.  Power BI Desktop — это специальное средство создания отчетов, позволяющее получать данные из различных источников, объединять и преобразовывать их, создавая подробные отчеты и визуализации, и публиковать их в Power BI.  Последняя версия Power BI Desktop теперь позволяет подключаться к вашей учетной записи Azure Cosmos DB через соединитель Azure Cosmos DB для Power BI.   

В этом руководстве по Power BI мы рассматриваем действия для подключения к учетной записи Azure Cosmos DB в Power BI Desktop, переходим к коллекции для извлечения данных с помощью навигатора, преобразуем данные JSON в табличный формат с помощью редактора запросов Power BI Desktop, а также создаем и публикуем отчет в PowerBI.com.

После изучения этого учебника по Power BI вы сможете ответить на следующие вопросы.  

* Как создавать отчеты с данными из Azure Cosmos DB с помощью Power BI Desktop?
* Как подключиться к учетной записи Azure Cosmos DB в Power BI Desktop?
* Как получить данные из коллекции в Power BI Desktop?
* Как преобразовывать вложенные данные JSON в Power BI Desktop?
* Как публиковать и совместно использовать отчеты в PowerBI.com?

> [!NOTE]
> Соединитель Power BI для Azure Cosmos DB подключается к Power BI Desktop для извлечения и преобразования данных. Затем отчеты, созданные в Power BI Desktop, можно опубликовать на сайте PowerBI.com. Невозможно напрямую извлекать и преобразовывать данные Azure Cosmos DB на сайте PowerBI.com. 

> [!NOTE]
> Чтобы подключить Azure Cosmos DB к Power BI с помощью API MongoDB, используйте [драйвер ODBC MongoDB Simba](http://www.simba.com/drivers/mongodb-odbc-jdbc/).

## <a name="prerequisites"></a>предварительным требованиям
Перед выполнением инструкций в этом руководстве по Power BI убедитесь в наличии доступа к следующим ресурсам:

* [Последняя версия Power BI Desktop](https://powerbi.microsoft.com/desktop).
* Доступ к демонстрационной учетной записи или данным в учетной записи Azure Cosmos DB.
  * В демонстрационной учетной записи используются данные о вулкане, показанные в этом руководстве. Использование этой демонстрационной учетной записи не регулируется соглашениями об уровне обслуживания. Она предназначена только для демонстрационных целей.  Мы оставляем за собой право вносить изменения в эту демонстрационную учетную запись, включая, кроме прочего, закрытие учетной записи, изменение ключа, ограничение доступа, изменение и удаление данных в любое время без уведомления или указания причины.
    * URL-адрес: https://analytics.documents.azure.com
    * Ключ только для чтения: MSr6kt7Gn0YRQbjd6RbTnTt7VHc5ohaAFu7osF0HdyQmfR+YhwCH2D2jcczVIR1LNK3nMPNBD31losN7lQ/fkw==
  * Сведения о создании учетной записи см. в статье [Azure Cosmos DB. Создание веб-приложения API DocumentDB с использованием языка .NET и портала Azure](https://azure.microsoft.com/documentation/articles/create-account/). Для получения примера данных о вулкане, схожих с теми, которые используются в этом руководстве (но не содержат блоки GeoJSON), посетите [сайт NOAA](https://www.ngdc.noaa.gov/nndc/struts/form?t=102557&s=5&d=5), а затем импортируйте данные с помощью [средства миграции данных Azure Cosmos DB](import-data.md).

Для совместного использования отчетов в службе PowerBI.com необходимо настроить учетную запись в PowerBI.com.  Дополнительные сведения о бесплатной версии Power BI и Power BI Pro см. по адресу [https://powerbi.microsoft.com/pricing](https://powerbi.microsoft.com/pricing).

## <a name="lets-get-started"></a>Начало работы
Для работы с этим руководством представим, что вы геолог, изучающий вулканы по всему миру.  Данные о вулканах хранятся в учетной записи Azure Cosmos DB, а документы JSON выглядят, как показано в примере документа ниже.

    {
        "Volcano Name": "Rainier",
           "Country": "United States",
          "Region": "US-Washington",
          "Location": {
            "type": "Point",
            "coordinates": [
              -121.758,
              46.87
            ]
          },
          "Elevation": 4392,
          "Type": "Stratovolcano",
          "Status": "Dendrochronology",
          "Last Known Eruption": "Last known eruption from 1800-1899, inclusive"
    }

Вам нужно получить данные о вулканах из учетной записи Azure Cosmos DB и представить их в виде интерактивного отчета Power BI, подобного приведенному ниже.

![После завершения этого учебника Power BI с соединителем Power BI вы сможете визуализировать данные в отчете о вулканах Power BI Desktop](./media/powerbi-visualize/power_bi_connector_pbireportfinal.png)

Готовы попробовать? Давайте приступим.

1. Запустите на своей рабочей станции Power BI Desktop.
2. После запуска Power BI Desktop появится экран *приветствия* .
   
    ![Экран приветствия в Power BI Desktop — соединитель Power BI](./media/powerbi-visualize/power_bi_connector_welcome.png)
3. С помощью экрана *приветствия* можно **получить данные**, просмотреть **последние источники** или **открыть другие отчеты**.  Щелкните значок X в правом верхнем углу, чтобы закрыть этот экран. Откроется представление **Отчет** средства Power BI Desktop.
   
    ![Представление отчета в Power BI Desktop — соединитель Power BI](./media/powerbi-visualize/power_bi_connector_pbireportview.png)
4. Откройте ленту **Главная** и щелкните **Получить данные**.  Откроется окно **Получение данных** .
5. Выберите **Azure**, затем — **Azure Cosmos DB (бета-версия)** и щелкните **Подключить**. 

    ![Получение данных в Power BI Desktop — соединитель Power BI](./media/powerbi-visualize/power_bi_connector_pbigetdata.png)   
6. На странице **Предварительная версия соединителя** нажмите кнопку **Продолжить**. Откроется окно **Azure Cosmos DB**.
7. Укажите URL-адрес конечной точки учетной записи Azure Cosmos DB, из которой необходимо получить данные, как показано ниже, и нажмите кнопку **ОК**. Чтобы использовать собственную учетную запись, можно найти URL-адрес в поле универсального кода ресурса (URI) в колонке **[Ключи](manage-account.md#keys)** на портале Azure. Чтобы использовать демонстрационную учетную запись, в качестве URL-адреса укажите `https://analytics.documents.azure.com`. 
   
    Не указывайте имя базы данных, имя коллекции и инструкцию SQL, т. к. эти поля являются необязательными.  Вместо этого мы будем использовать навигатор для выбора базы данных и коллекции для определения источника данных.
   
    ![Руководство по Power BI для соединителя Power BI Azure Cosmos DB — окно подключения рабочего стола](./media/powerbi-visualize/power_bi_connector_pbiconnectwindow.png)
8. При подключении к этой конечной точке в первый раз отображается запрос на введение ключа учетной записи. Для собственной учетной записи найдите ключ в поле **Первичный ключ** в колонке **[Ключи только для чтения](manage-account.md#keys)** на портале Azure. Ключ для демонстрационной учетной записи — `MSr6kt7Gn0YRQbjd6RbTnTt7VHc5ohaAFu7osF0HdyQmfR+YhwCH2D2jcczVIR1LNK3nMPNBD31losN7lQ/fkw==`. Введите соответствующий ключ и нажмите кнопку **Подключить**.
   
    При создании отчетов мы рекомендуем использовать ключ только для чтения.  Это защитит главный ключ от ненужных рисков безопасности. Ключ только для чтения можно найти в колонке [Ключи](manage-account.md#keys) на портале Azure. Можно также использовать сведения демонстрационной учетной записи, приведенные выше.
   
    ![Руководство по Power BI для соединителя Power BI Azure Cosmos DB — ключ учетной записи](./media/powerbi-visualize/power_bi_connector_pbidocumentdbkey.png)
    
    > [!NOTE] 
    > Если отобразится сообщение об ошибке "Не удалось найти указанную базу данных", то см. инструкции по устранению в [описании этой проблемы на странице сообщества Power BI](https://community.powerbi.com/t5/Issues/Document-DB-Power-BI/idi-p/208200).
    
9. После успешного подключения учетной записи откроется панель **Навигатор**.  На панели **Навигатор** отображается список баз данных в учетной записи.
10. Выберите и разверните узел базы данных, которая служит источником данных для отчета. Если вы используете демонстрационную учетную запись, то выберите **volcanodb**.   
11. Теперь выберите коллекцию с данными, которые вы намерены извлечь. Если вы используете демонстрационную учетную запись, то выберите **volcano1**.
    
    В области «Предварительный просмотр» отображается список элементов **Запись** .  Документ представлен как тип **Запись** в Power BI. Точно так же вложенный блок JSON в документе является **Записью**.
    
    ![Руководство по Power BI для соединителя Power BI Azure Cosmos DB — окно навигатора](./media/powerbi-visualize/power_bi_connector_pbinavigator.png)
12. Нажмите кнопку **Изменить**, чтобы открыть в новом окне редактор запросов для преобразования данных.

## <a name="flattening-and-transforming-json-documents"></a>Преобразование документов JSON в плоскую структуру и их трансформация
1. Переключитесь в окно редактора запросов Power BI, где по центру отображается столбец **Документ**.
   ![Редактор запросов Power BI Desktop](./media/powerbi-visualize/power_bi_connector_pbiqueryeditor.png)
2. В правой части заголовка столбца **Документ** щелкните кнопку развертывания.  Отобразится контекстное меню со списком полей.  Выберите поля, необходимые для вашего отчета, например "Volcano Name", "Country", "Region", "Location", "Elevation", "Type", "Status" и "Last Know Eruption", а затем нажмите кнопку "ОK". Снимите флажок **Использовать исходное имя столбца как префикс** и щелкните **ОК**.
   
    ![Руководство по Power BI для соединителя Power BI Azure Cosmos DB — раскрыть документы](./media/powerbi-visualize/power_bi_connector_pbiqueryeditorexpander.png)
3. В центральной области отображается предварительный просмотр результата с выбранными полями.
   
    ![Руководство по Power BI для соединителя Power BI Azure Cosmos DB — сделать результаты более плоскими](./media/powerbi-visualize/power_bi_connector_pbiresultflatten.png)
4. В нашем примере свойство «Расположение» является блоком GeoJSON в документе.  Как видно, значение свойства «Расположение» представлено типом **Запись** в Power BI Desktop.  
5. Щелкните элемент развертывания справа от заголовка столбца Document.Location.  Отобразится контекстное меню с полями типа и координат.  Выберите здесь поле координат, снимите флажок **Использовать исходное имя столбца как префикс** и щелкните **ОК**.
   
    ![Руководство по Power BI для соединителя Power BI Azure Cosmos DB — запись о расположении](./media/powerbi-visualize/power_bi_connector_pbilocationrecord.png)
6. Теперь в центральной области отображается столбец с координатами типа **Список** .  Как показано в начале этого руководства, данные GeoJSON в руководстве принадлежат типу «Точка» со значениями широты и долготы, записанными в массив координат.
   
    Элемент coordinates[0] представляет долготу, а элемент coordinates[1] — широту.
    ![Руководство по Power BI для соединителя Power BI Azure Cosmos DB — список координат](./media/powerbi-visualize/power_bi_connector_pbiresultflattenlist.png)
7. Чтобы преобразовать массив координат в плоскую структуру, создайте **настраиваемый столбец** с именем LatLong.  Выберите **Добавление столбца** на ленте и щелкните **Настраиваемый столбец**.  Откроется окно **Настраиваемый столбец**.
8. Укажите имя нового столбца, например LatLong.
9. Затем укажите настраиваемую формулу для нового столбца.  В нашем примере мы соединим значения широты и долготы, разделяя их запятыми, как показано ниже, используя следующую формулу: `Text.From([coordinates]{1})&","&Text.From([coordinates]{0})`. Последовательно выберите **ОК**.
   
    Дополнительные сведения о выражениях анализа данных (DAX), включая функции DAX, см. в статье [Основные сведения о DAX в Power BI Desktop](https://support.powerbi.com/knowledgebase/articles/554619-dax-basics-in-power-bi-desktop).
   
    ![Руководство по Power BI для соединителя Power BI Azure Cosmos DB — добавление пользовательского столбца](./media/powerbi-visualize/power_bi_connector_pbicustomlatlong.png)

10. Теперь в центральной области отображается новый столбец LatLong, заполненный значениями.
    
    ![Руководство по Power BI для соединителя Power BI Azure Cosmos DB — пользовательский столбец LatLong](./media/powerbi-visualize/power_bi_connector_pbicolumnlatlong.png)
    
    Если в новом столбце появляется сообщение об ошибке, убедитесь, что примененные шаги в разделе "Параметры запроса" соответствуют указанным на следующем рисунке:
    
    ![Примененные шаги должны быть такими: источник, навигация, развернут документ, развернуто расположение документа, добавлен настраиваемый элемент](./media/powerbi-visualize/power-bi-applied-steps.png)
    
    Если ваши шаги отличаются, удалите лишние шаги и снова попробуйте добавить настраиваемый столбец. 

11. Чтобы сохранить модель данных, нажмите кнопку **Закрыть и применить** .
    
    ![Руководство по Power BI для соединителя Power BI Azure Cosmos DB — закрыть и применить](./media/powerbi-visualize/power_bi_connector_pbicloseapply.png)

<a id="build-the-reports"></a>
## <a name="build-the-reports"></a>Создание отчетов
Для создания отчетов, в которых наглядно представлены данные, используется представление отчета в Power BI Desktop.  Отчеты можно создавать путем перетаскивания полей на поле **Отчет** .

![Представление отчета в Power BI Desktop — соединитель Power BI](./media/powerbi-visualize/power_bi_connector_pbireportview2.png)

В представлении «Отчет» найдите следующие элементы.

1. В области **Поля** отображается список моделей данных с перечислением полей, которые можно использовать для отчетов.
2. Область **Визуализации** . Отчет может содержать одну или несколько визуализаций.  В области **Визуализации** можно выбрать нужные типы визуальных элементов.
3. Холст **Отчет** позволяет создавать визуальные элементы для отчета.
4. Страница **Отчеты** . В Power BI Desktop можно добавить несколько страниц отчета.

Ниже приведены основные этапы создания простого интерактивного отчета с представлением карты.

1. В этом примере мы создадим представление карты, показывающей расположение каждого вулкана.  В области **Визуализации** выберите тип визуального элемента "Карта", который выделен на снимке экрана выше.  В области **Отчет** отобразится тип визуального элемента «Карта».  В области **Визуализация** также должен отобразиться набор свойств, связанных с этим типом визуального элемента.
2. Теперь из области **Поля** перетащите поле "LatLong" в свойство **Location** в области **Визуализации**.
3. Затем перетащите поле «Имя вулкана» в свойство **Условные обозначения** .  
4. Затем перетащите поле "Высота" в свойство **Размер** .  
5. Теперь на карте должны отображаться кружки, указывающие расположение каждого вулкана (размер пузырька соответствует высоте вулкана).
6. Базовый отчет готов.  Его можно настроить, добавив дополнительные визуальные элементы.  В нашем случае мы добавили срез «Тип вулкана», чтобы сделать отчет интерактивным.  
   
    ![Снимок экрана окончательного отчета Power BI Desktop после завершения работы с руководством Power BI для Azure Cosmos DB](./media/powerbi-visualize/power_bi_connector_pbireportfinal.png)
7. В меню "Файл" щелкните **Сохранить** и сохраните файл с именем PowerBITutorial.pbix.

## <a name="publish-and-share-your-report"></a>Публикация и совместное использование отчета
Для совместного использования отчета необходимо иметь учетную запись в службе PowerBI.com.

1. В средстве Power BI Desktop откройте ленту **Главная** .
2. Щелкните **Опубликовать**.  Вы увидите запрос на ввод имени пользователя и пароля учетной записи PowerBI.com.
3. После аутентификации учетных данных отчет публикуется в указанном вами расположении.
4. Для просмотра и совместного использования отчета на сайте PowerBI.com щелкните **Открыть PowerBITutorial.pbix в Power BI** .
   
    ![Публикация в Power BI успешно завершена! Открыть учебник в Power BI](./media/powerbi-visualize/power_bi_connector_open_in_powerbi.png)

## <a name="create-a-dashboard-in-powerbicom"></a>Создание панели мониторинга на PowerBI.com
Теперь, когда у вас уже есть отчет, используйте его совместно с другими пользователями на сайте PowerBI.com.

При публикации отчета из Power BI Desktop на сайт PowerBI.com он создает **отчет** и **набор данных** в клиенте PowerBI.com. Например, после публикации на сайте PowerBI.com отчета **PowerBITutorial** его имя отобразится в разделах **Отчеты** и **Наборы данных** на PowerBI.com.

   ![Снимок экрана нового отчета и набора данных на PowerBI.com](./media/powerbi-visualize/powerbi-reports-datasets.png)

Чтобы создать панель мониторинга для совместного использования, нажмите в своем отчете на PowerBI.com кнопку **Закрепление живой плитки** .

   ![Снимок экрана нового отчета и набора данных на PowerBI.com](./media/powerbi-visualize/power-bi-pin-live-tile.png)

Для создания новой панели мониторинга следуйте инструкциям в разделе [Закрепление плиток из отчета](https://powerbi.microsoft.com/documentation/powerbi-service-pin-a-tile-to-a-dashboard-from-a-report/#pin-a-tile-from-a-report) . 

Можно также внести в отчет ситуативные изменения, прежде чем создавать панель мониторинга. Однако для внесения изменений и повторной публикации отчета на PowerBI.com рекомендуется использовать Power BI Desktop.

<!-- ## Refresh data in PowerBI.com
There are two ways to refresh data, ad hoc and scheduled.

For an ad hoc refresh, simply click on the eclipses (…) by the **Dataset**, e.g. PowerBITutorial. You should see a list of actions including **Refresh Now**. Click **Refresh Now** to refresh the data.

![Screenshot of Refresh Now in PowerBI.com](./media/powerbi-visualize/power-bi-refresh-now.png)

For a scheduled refresh, do the following.

1. Click **Schedule Refresh** in the action list. 

    ![Screenshot of the Schedule Refresh in PowerBI.com](./media/powerbi-visualize/power-bi-schedule-refresh.png)
2. In the **Settings** page, expand **Data source credentials**. 
3. Click on **Edit credentials**. 
   
    The Configure popup appears. 
4. Enter the key to connect to the Azure Cosmos DB account for that data set, then click **Sign in**. 
5. Expand **Schedule Refresh** and set up the schedule you want to refresh the dataset. 
6. Click **Apply** and you are done setting up the scheduled refresh.
-->
## <a name="next-steps"></a>Дополнительная информация
* Дополнительные сведения о Power BI см. в статье [Приступая к работе с Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/).
* Дополнительные сведения об Azure Cosmos DB см. на [целевой странице документации по Azure Cosmos DB](https://azure.microsoft.com/documentation/services/cosmos-db/).

