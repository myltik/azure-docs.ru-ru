---
title: "Разработка скриптов U-SQL с помощью средств Data Lake для Visual Studio | Документация Майкрософт"
description: "Сведения об установке средств Data Lake для Visual Studio, разработке и тестировании скриптов U-SQL."
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: saveenr
editor: cgronlun
ms.assetid: ad8a6992-02c7-47d4-a108-62fc5a0777a3
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/06/2017
ms.author: edmaca, yanacai
ms.translationtype: Human Translation
ms.sourcegitcommit: 7948c99b7b60d77a927743c7869d74147634ddbf
ms.openlocfilehash: b2fd62683f0272da271eebeb6eb3a71388ecb59e
ms.contentlocale: ru-ru
ms.lasthandoff: 06/20/2017


---
# <a name="develop-u-sql-scripts-by-using-data-lake-tools-for-visual-studio"></a>Разработка скриптов U-SQL с помощью средств Data Lake для Visual Studio
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]


Узнайте, как с помощью Visual Studio создавать учетные записи Azure Data Lake Analytics, определять задания в [U-SQL](data-lake-analytics-u-sql-get-started.md) и отправлять их в службу Data Lake Analytics. Дополнительные сведения о Data Lake Analytics см. в [обзоре Azure Data Lake Analytics](data-lake-analytics-overview.md).


## <a name="prerequisites"></a>Предварительные требования

* **Visual Studio**: поддерживаются все выпуски, кроме Express.
    * Visual Studio 2017
    * Visual Studio 2015 с обновлением 4
    * Visual Studio 2013
* **Microsoft Azure SDK для .NET** (версии 2.7.1 или выше).  Можно установить его с помощью [установщика веб-платформы](http://www.microsoft.com/web/downloads/platform.aspx).
* Учетная запись **Data Lake Analytics**. Чтобы создать учетную запись, ознакомьтесь со статьей [Руководство. Начало работы с Azure Data Lake Analytics с помощью портала Azure](data-lake-analytics-get-started-portal.md).

## <a name="install-azure-data-lake-tools-for-visual-studio"></a>Установка средств Azure Data Lake для Visual Studio 

Загрузите и установите средства Azure Data Lake для Visual Studio [из центра загрузки](http://aka.ms/adltoolsvs). После установки обратите внимание на следующее:
* узел **Обозреватель сервера** > **Azure** содержит узел **Data Lake Analytics**; 
* в меню **Средства** появился пункт **Data Lake**.

## <a name="connect-to-an-azure-data-lake-analytics-account"></a>Подключение к учетной записи Azure Data Lake Analytics

1. Откройте Visual Studio.
2. Откройте обозреватель сервера, последовательно выбрав пункты **Представление** > **Обозреватель сервера**.
3. Щелкните правой кнопкой мыши **Azure**. Затем выберите **Подключиться к подписке Microsoft Azure** и следуйте инструкциям.
4. В обозревателе сервера последовательно выберите пункты **Azure** > **Data Lake Analytics**. Отобразится список учетных записей Data Lake Analytics.


## <a name="write-your-first-u-sql-script"></a>Создание первого скрипта U-SQL

Ниже приводится простой скрипт U-SQL. Он определяет небольшой набор данных и по умолчанию записывает его в хранилище Data Lake Store как файл с именем `/data.csv`.

```
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS 
              D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
```

### <a name="submit-a-data-lake-analytics-job"></a>Отправка задания аналитики озера данных

1. Выберите **Файл** > **Создать** > **Проект**.

2. Выберите тип **Проект U-SQL** и нажмите кнопку **ОК**. Visual Studio создаст решение с помощью файла **Script.usql**.

3. Вставьте предыдущий скрипт в окно **Script.usql**.

4. В левом верхнем углу окна **Script.usql** укажите учетную запись Data Lake Analytics.

    ![Отправка проекта U-SQL в Visual Studio](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-submit-job.png)

5. В левом верхнем углу окна **Script.usql** выберите **Отправить**.
6. Проверьте **учетную запись Analytics** и выберите **Отправить**. По завершении отправки ее результаты появятся в окне результатов средств Data Lake для Visual Studio.

    ![Отправка проекта U-SQL в Visual Studio](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-submit-job-advanced.png)
7. Чтобы отобразились сведения о последнем состоянии задания, нажмите кнопку **Обновить**. Если задание завершилось успешно, отобразятся параметры **Граф задания**, **Операции с метаданными**, **Журнал состояний** и **Диагностика**:

    ![График выполнения задания аналитики озера данных U-SQL в Visual Studio](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-performance-graph.png)

   *  В окне **Сводные данные задания** представлена сводка задания.   
   * В окне **Сведения о задании** содержатся более конкретные сведения о задании, в частности о скрипте, ресурсах и вершинах.
   *  В окне **Граф задания** визуализируется ход выполнения задания.
   *  В окне **Операции с метаданными** представлены сведения обо всех действиях, выполненных в каталоге U-SQL.
   * В окне **Данные** отображаются все входные и выходные данные.
   * В окне **Диагностика** представлены данные расширенного анализа для выполнения задания и оптимизации производительности.

### <a name="to-check-job-state"></a>Проверка состояния задания

1. В обозревателе сервера последовательно выберите пункты **Azure** > **Data Lake Analytics**. 
2. Разверните окно имени учетной записи Data Lake Analytics.
3. Дважды щелкните **Задания**.
4. Выберите задание, отправленное ранее.

### <a name="to-see-the-output-of-a-job"></a>Просмотр выходных данных задания

1. В обозревателе сервера перейдите к отправленному заданию.
2. Перейдите на вкладку **Данные** .
3. На вкладке **Job Outputs** (Выходные данные задания) выберите файл `"/data.csv"`.

## <a name="next-steps"></a>Дальнейшие действия

* [Руководство. Начало работы с Azure Data Lake Analytics с помощью портала Azure](data-lake-analytics-get-started-portal.md) | [Руководство. Начало работы с Azure Data Lake Analytics с помощью Azure PowerShell](data-lake-analytics-get-started-powershell.md) 
* [Отладка заданий U-SQL](data-lake-analytics-debug-u-sql-jobs.md)
* [Использование средств Azure Data Lake для Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)

