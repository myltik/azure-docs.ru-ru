---
title: Анализ данных в Data Lake Store с помощью Power BI | Документация Майкрософт
description: Использование Power BI для анализа данных в хранилище озера данных Azure
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 57d19d27-e135-49d9-a7ea-46c48ef4e3bd
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: 37cde17703a15ba5659814e1b65a54d24b67edad
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34624793"
---
# <a name="analyze-data-in-data-lake-store-by-using-power-bi"></a>Анализ данных в хранилище озера данных с помощью Power BI
В этой статье вы узнаете, как использовать Power BI Desktop для анализа и визуализации данных в хранилище озера данных Azure.

## <a name="prerequisites"></a>предварительным требованиям
Перед началом работы с этим учебником необходимо иметь следующее:

* **Подписка Azure**. См. страницу [бесплатной пробной версии Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Учетная запись хранилища озера данных Azure**. Следуйте инструкциям в разделе [Приступая к работе с хранилищем озера данных Azure на портале Azure](data-lake-store-get-started-portal.md). В этой статье предполагается, что вы уже создали учетную запись Data Lake Store с именем **mybidatalakestore** и добавили в нее образец файла данных (**Drivers.txt**). Этот образец файла можно скачать в [репозитории Git для озера данных Azure](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt).
* **Power BI Desktop**. Это средство можно скачать в [Центре загрузки Майкрософт](https://www.microsoft.com/en-us/download/details.aspx?id=45331). 

## <a name="create-a-report-in-power-bi-desktop"></a>Создание отчета в Power BI Desktop
1. Запустите Power BI Desktop на своем компьютере.
2. На ленте **Главная** нажмите кнопку **Получить данные**, а затем щелкните "Дополнительно". В диалоговом окне **Получение данных** выберите пункт **Azure**, а затем выберите пункт **Azure Data Lake Store** и нажмите кнопку **Подключиться**.
   
    ![Подключение к Data Lake Store](./media/data-lake-store-power-bi/get-data-lake-store-account.png "Подключение к Data Lake Store")
3. Если появится диалоговое окно с сообщением о том, что соединитель находится в стадии разработки, выберите вариант "Продолжить".
4. В диалоговом окне **Хранилище озера данных Microsoft Azure** укажите URL-адрес для своей учетной записи Data Lake Store и нажмите кнопку **ОК**.
   
    ![URL-адрес для Data Lake Store](./media/data-lake-store-power-bi/get-data-lake-store-account-url.png "URL-адрес для Data Lake Store")
5. В следующем диалоговом окне нажмите кнопку **Вход** , чтобы войти в учетную запись хранилища озера данных. Вы будете перенаправлены на страницу входа своей организации. Следуйте инструкциям для входа в учетную запись.
   
    ![Вход в Data Lake Store](./media/data-lake-store-power-bi/get-data-lake-store-account-signin.png "Вход в Data Lake Store")
6. Успешно выполнив вход, нажмите кнопку **Подключиться**.
   
    ![Подключение к Data Lake Store](./media/data-lake-store-power-bi/get-data-lake-store-account-connect.png "Подключение к Data Lake Store")
7. В следующем диалоговом окне будет указан файл, который вы добавили в учетную запись хранилища озера данных. Проверьте информацию и нажмите кнопку **Загрузить**.
   
    ![Скачивание данных из Data Lake Store](./media/data-lake-store-power-bi/get-data-lake-store-account-load.png "Скачивание данных из Data Lake Store")
8. После успешной загрузки данных в Power BI вы увидите приведенные ниже поля на вкладке **Поля** .
   
    ![Импортированные поля](./media/data-lake-store-power-bi/imported-fields.png "Импортированные поля")
   
    Однако для визуализации и анализа данных мы рекомендуем использовать показанные ниже поля.
   
    ![Нужные поля](./media/data-lake-store-power-bi/desired-fields.png "Нужные поля")
   
    В следующих шагах мы обновим запрос, чтобы преобразовать импортированные данные в нужный формат.
9. На вкладке **Главная** ленты нажмите кнопку **Изменить запросы**.
   
    ![Изменение запросов](./media/data-lake-store-power-bi/edit-queries.png "Изменение запросов")
10. В редакторе запросов в столбце **Содержимое** выберите вариант **Двоичные данные**.
    
    ![Изменение запросов](./media/data-lake-store-power-bi/convert-query1.png "Изменение запросов")
11. Вы увидите значок, представляющий добавленный вами файл **Drivers.txt** . Щелкните файл правой кнопкой мыши и выберите пункт **CSV**.    
    
    ![Изменение запросов](./media/data-lake-store-power-bi/convert-query2.png "Изменение запросов")
12. В результате вы увидите выходные данные, приведенные ниже. Данные теперь имеют формат, который можно использовать для создания визуализаций.
    
    ![Изменение запросов](./media/data-lake-store-power-bi/convert-query3.png "Изменение запросов")
13. На вкладке **Главная** ленты нажмите кнопку **Закрыть и применить** и выберите пункт **Закрыть и применить**.
    
    ![Изменение запросов](./media/data-lake-store-power-bi/load-edited-query.png "Изменение запросов")
14. После обновления запроса на вкладке **Поля** появятся новые поля, доступные для визуализации.
    
    ![Обновление полей](./media/data-lake-store-power-bi/updated-query-fields.png "Обновление полей")
15. Создадим круговую диаграмму, на которой будет представлено количество водителей в каждом городе данной страны. Для этого выполним указанные ниже действия.
    
    1. На вкладке "Визуализации" щелкните символ круговой диаграммы.
       
        ![Создание круговой диаграммы](./media/data-lake-store-power-bi/create-pie-chart.png "Создание круговой диаграммы")
    2. Мы собираемся использовать следующие столбцы: **Столбец 4** (название города) и **Столбец 7** (название страны). Перетащите их с вкладки **Поля** на вкладку **Визуализации**, как показано ниже.
       
        ![Создание визуализации](./media/data-lake-store-power-bi/create-visualizations.png "Создание визуализации")
    3. Круговая диаграмма теперь должна выглядеть так, как показано ниже.
       
        ![Круговая диаграмма](./media/data-lake-store-power-bi/pie-chart.png "Создание визуализаций")
16. Выбрав определенную страну в фильтрах уровня страницы, вы можете теперь увидеть количество водителей в каждом городе этой страны. Например, на вкладке **Визуализации** в списке **Фильтры** уровня страницы выберите **Бразилия**.
    
    ![Выбор страны](./media/data-lake-store-power-bi/select-country.png "Выбор страны")
17. Круговая диаграмма автоматически обновится, и вы увидите количество водителей в городах Бразилии.
    
    ![Количество водителей в стране](./media/data-lake-store-power-bi/driver-per-country.png "Количество водителей в каждой стране")
18. В меню **Файл** выберите пункт **Сохранить**, чтобы сохранить визуализацию как файл Power BI Desktop.

## <a name="publish-report-to-power-bi-service"></a>Публикация отчета в службе Power BI
Создав визуализации в Power BI Desktop, вы можете предоставить общий доступ к ним другим пользователям, опубликовав их в службе Power BI. Инструкции см. в разделе [Публикация из Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-upload-desktop-files/).

## <a name="see-also"></a>См. также
* [Анализ данных в хранилище озера данных с помощью Аналитики озера данных](../data-lake-analytics/data-lake-analytics-get-started-portal.md)

