---
title: "Устранение неполадок с заданиями Azure Data Lake Analytics с помощью портала Azure | Документация Майкрософт"
description: "Узнайте, как использовать портал Azure для устранения неполадок с заданиями аналитики озера данных. "
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: saveenr
editor: cgronlun
ms.assetid: b7066d81-3142-474f-8a34-32b0b39656dc
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: edmaca
ms.openlocfilehash: b9c7453cc0a94f70d0098ed83e5f127832065a62
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-azure-data-lake-analytics-jobs-using-azure-portal"></a>Устранение неполадок с заданиями аналитики озера данных Azure с помощью портала Azure
Узнайте, как использовать портал Azure для устранения неполадок с заданиями аналитики озера данных.

При прохождении этого учебника вы смоделируете проблему с отсутствующим исходным файлом и устраните ее с помощью портала Azure.

## <a name="submit-a-data-lake-analytics-job"></a>Отправка задания аналитики озера данных

Отправьте следующее задание U-SQL:

```
@searchlog =
   EXTRACT UserId          int,
           Start           DateTime,
           Region          string,
           Query           string,
           Duration        int?,
           Urls            string,
           ClickedUrls     string
   FROM "/Samples/Data/SearchLog.tsv1"
   USING Extractors.Tsv();

OUTPUT @searchlog   
   TO "/output/SearchLog-from-adls.csv"
   USING Outputters.Csv();
```
    
В сценарии определен исходный файл **/Samples/Data/SearchLog.tsv1**, а должен быть **/Samples/Data/SearchLog.tsv**.


## <a name="troubleshoot-the-job"></a>Устранение неполадок задания

**Просмотр всех заданий**

1. На портале Azure щелкните **Microsoft Azure** в левом верхнем углу.
2. Щелкните элемент с именем вашей учетной записи аналитики озера данных.  Сводные данные задания отображаются на элементе **Управление заданиями** .

    ![Аналитика озера данных Azure: управление заданиями](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-job-management.png)

    Элемент управления заданиями позволяет взглянуть на состояние заданий. Обратите внимание, что здесь имеется задание, завершившееся сбоем.
3. Щелкните элемент **Управление заданиями** для просмотра заданий. Задания делятся на три категории: **Выполняется**, **В очереди** и **Завершено**. Задание, завершившееся сбоем, будет отображено в разделе **Завершено** . Оно должно быть первым в списке. При наличии большого числа заданий можно щелкнуть **Фильтр** , чтобы упростить их поиск.

    ![Аналитика озера данных Azure: фильтрация заданий](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-filter-jobs.png)
4. Щелкните невыполненное задание из списка, чтобы открыть сведения о задании в новой колонке:

    ![Аналитика озера данных Azure: невыполненное задание](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job.png)

    Обратите внимание на кнопку **Отправить повторно** . После устранения проблемы можно отправить задание повторно.
5. Щелкните выделенную часть на предыдущем снимке экрана, чтобы открыть сведения об ошибке.  Отобразится примерно следующий текст:

    ![Аналитика озера данных Azure: сведения о невыполненном задании](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job-details.png)

    В нем сообщается, что исходная папка не найдена.
6. Щелкните **Дублировать скрипт**.
7. Измените путь **FROM** на:

    /Samples/Data/SearchLog.tsv
8. Щелкните **Отправить задание**.

## <a name="see-also"></a>Дополнительные материалы
* [Обзор аналитики озера данных Azure](data-lake-analytics-overview.md)
* [Начало работы с аналитикой озера данных Azure с помощью Azure PowerShell](data-lake-analytics-get-started-powershell.md)
* [Начало работы с аналитикой озера данных Azure и U-SQL с помощью Visual Studio](data-lake-analytics-u-sql-get-started.md)
* [Управление аналитикой озера данных Azure с помощью портала Azure](data-lake-analytics-manage-use-portal.md)
