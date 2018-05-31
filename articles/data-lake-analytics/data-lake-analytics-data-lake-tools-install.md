---
title: Установка Средств Azure Data Lake для Visual Studio | Документация Майкрософт
description: Скачивание и установка Средств Azure Data Lake для Visual Studio.
services: data-lake-analytics
documentationcenter: ''
author: saveenr
manager: jhubbard
editor: cgronlun
ms.assetid: ad8a6992-02c7-47d4-a108-62fc5a0777a3
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/03/2018
ms.author: saveenr, yanacai
ms.openlocfilehash: 37b01c404006bbba79b185049aea6c7f77ce3c68
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2018
ms.locfileid: "33887354"
---
# <a name="install-data-lake-tools-for-visual-studio"></a>Установка инструментов Data Lake для Visual Studio

Узнайте, как с помощью Visual Studio создавать учетные записи Azure Data Lake Analytics, определять задания в [U-SQL](data-lake-analytics-u-sql-get-started.md) и отправлять их в службу Data Lake Analytics. Дополнительные сведения о Data Lake Analytics см. в [обзоре Azure Data Lake Analytics](data-lake-analytics-overview.md).

## <a name="prerequisites"></a>предварительным требованиям

* **Visual Studio**: поддерживаются все выпуски, кроме Express.
    * Visual Studio 2017
    * Visual Studio 2015
    * Visual Studio 2013
* **Microsoft Azure SDK для .NET** (версии 2.7.1 или выше).  Можно установить его с помощью [установщика веб-платформы](http://www.microsoft.com/web/downloads/platform.aspx).
* Учетная запись **Data Lake Analytics**. Чтобы создать учетную запись, ознакомьтесь со статьей [Руководство. Начало работы с Azure Data Lake Analytics с помощью портала Azure](data-lake-analytics-get-started-portal.md).

## <a name="install-azure-data-lake-tools-for-visual-studio-2017"></a>Установка Средств Azure Data Lake для Visual Studio 2017

Средства Azure Data Lake для Visual Studio не поддерживаются в Visual Studio 2017 15.3 или более поздних версий. Средства являются частью рабочей нагрузки **хранилища и обработки данных** и **разработки Azure** в Visual Studio Installer. Включите одну из этих рабочих нагрузок как часть установки Visual Studio.  

Включите рабочую нагрузку **хранилища и обработки данных**, как показано ниже: ![Включение рабочей нагрузки хранилища и обработки данных](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-tools-for-vs-2017-install-01.png)

Включите рабочую нагрузку **разработки Azure**, как показано ниже: ![Включение рабочей нагрузки разработки Azure](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-tools-for-vs-2017-install-02.png)

## <a name="install-azure-data-lake-tools-for-visual-studio-2013-and-2015"></a>Установка Средств Azure Data Lake для Visual Studio 2013 и 2015

Загрузите и установите средства Azure Data Lake для Visual Studio [из центра загрузки](http://aka.ms/adltoolsvs). После установки обратите внимание на следующее:
* узел **Обозреватель сервера** > **Azure** содержит узел **Data Lake Analytics**; 
* в меню **Средства** появился пункт **Data Lake**.


## <a name="next-steps"></a>Дальнейшие действия
* Сведения о том, как записывать диагностические данные в журнал, см. в статье [Доступ к журналам диагностики для Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md).
* Более сложный запрос можно посмотреть в статье [Анализ журналов веб-сайта с помощью аналитики озера данных Azure](data-lake-analytics-analyze-weblogs.md).
* Дополнительные сведения см. в статье [Use the Vertex Execution View in Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md) (Использование представления выполнения вершин в инструментах Data Lake для Visual Studio).
