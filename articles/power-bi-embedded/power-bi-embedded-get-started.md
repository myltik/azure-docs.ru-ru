---
title: "Начало работы с Microsoft Power BI Embedded"
description: "Power BI Embedded, добавление интерактивных отчетов Power BI в приложение бизнес-аналитики"
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.assetid: 4787cf44-5d1c-4bc3-b3fd-bf396e5c1176
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 03/11/2017
ms.author: asaxton
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: 4afa8d2c7f8ec1942521ba5fa131967dfd581c91
ms.lasthandoff: 03/14/2017

---
# <a name="get-started-with-microsoft-power-bi-embedded"></a>Начало работы с Microsoft Power BI Embedded

**Power BI Embedded** — это служба Azure, которая позволяет разработчикам приложений добавлять интерактивные отчеты Power BI в свои приложения. **Power BI Embedded** работала с существующими приложениями, вам не нужно изменять способ, с помощью которого пользователи выполняют вход.

Ресурсы для **Microsoft Power BI Embedded** подготавливаются через [API-интерфейсы ARM Azure](https://msdn.microsoft.com/library/mt712306.aspx). В нашем примере подготавливаемый ресурс — это **коллекция рабочих областей Power BI**.

![](media/power-bi-embedded-get-started/introduction.png)

## <a name="create-a-workspace-collection"></a>Создание коллекции рабочей области

**Коллекция рабочей области** — это ресурс и контейнер Azure верхнего уровня для содержимого, которое будет внедрено в приложение. **Коллекцию рабочей области** можно создать двумя способами:

* вручную с помощью портала Azure;
* программно с помощью интерфейсов API Resource Manager (ARM).

Давайте узнаем, как создать **коллекцию рабочей области** с помощью портала Azure.

1. Войдите на **портал Azure**по адресу [http://portal.azure.com](http://portal.azure.com).
2. На панели вверху щелкните **+Создать** .
   
   ![](media/power-bi-embedded-get-started/create-workspace-1.png)
3. В разделе **Данные+аналитика** щелкните **Power BI Embedded**.
4. В колонке **Коллекция рабочей области** введите необходимые сведения. Дополнительные сведения о **ценообразовании** см. на странице с информацией о [ценах на Power BI Embedded](http://go.microsoft.com/fwlink/?LinkID=760527).
   
   ![](media/power-bi-embedded-get-started/create-workspace-2.png)
5. Щелкните **Создать**.

Подготовка **коллекции рабочей области** займет несколько секунд. После этого откроется колонка **Коллекция рабочей области**.

   ![](media/power-bi-embedded-get-started/create-workspace-3.png)

Эта **колонка создания** содержит сведения, необходимые для вызова API-интерфейсов, которые создают рабочие области и развертывают содержимое.

<a name="view-access-keys"/>

## <a name="view-power-bi-api-access-keys"></a>Просмотр ключей доступа для вызова API Power BI

Сведения, необходимые для вызова REST API Power BI, включают в себя такой важный элемент, как **ключи доступа**. Эти ключи используются для создания **маркеров приложения** для проверки подлинности запросов к API. Чтобы просмотреть **ключи доступа**, в **колонке параметров** щелкните **Ключи доступа**. Дополнительные сведения о **маркерах приложений** см. в статье [Аутентификация и авторизация в Power BI Embedded](power-bi-embedded-app-token-flow.md).

   ![](media/power-bi-embedded-get-started/access-keys.png)

Обратите внимание: у вас есть два ключа.

   ![](media/power-bi-embedded-get-started/access-keys-2.png)

Скопируйте эти ключи и надежно сохраните их в приложении. Очень важно обращаться с этими ключами как с паролем, так как они предоставляют доступ ко всему содержимому в **коллекции рабочей области**.

Хотя указаны два ключа, в один момент времени используется только один из них. Второй ключ нужен, чтобы не прерывать доступ к службе во время периодического повторного создания ключей.

Теперь, когда у вас есть экземпляр Power BI для приложения и **ключи доступа**, вы можете импортировать отчет в свое приложение. Прежде чем вы узнаете, как импортировать отчет, в следующем разделе мы расскажем о создании наборов данных и отчетов Power BI, которые будут внедрены в приложение.

## <a name="working-with-workspaces"></a>Работа с рабочими областями

После создания коллекции рабочих областей необходимо создать рабочую область для размещения отчетов и наборов данных. Чтобы создать рабочую область, необходимо использовать [Post Worksapce REST API](https://msdn.microsoft.com/library/azure/mt711503.aspx).

## <a name="create-power-bi-datasets-and-reports-to-embed-into-an-app-using-power-bi-desktop"></a>Создание наборов данных и отчетов Power BI для внедрения в приложение с помощью Power BI Desktop

Теперь, когда вы создали экземпляр Power BI для приложения и получили **ключи доступа**, можно приступать к созданию наборов данных и отчетов Power BI для внедрения. Наборы данных и отчеты можно создавать с помощью **Power BI Desktop**. [Power BI Desktop](https://go.microsoft.com/fwlink/?LinkId=521662) можно скачать бесплатно. Или же, чтобы быстро приступить к работе, можно скачать [PBIX-файл с примером анализа данных о продажах](http://go.microsoft.com/fwlink/?LinkID=780547).

> [!NOTE]
> Дополнительные сведения об использовании **Power BI Desktop** см. в статье [Начало работы с Power BI Desktop](https://powerbi.microsoft.com/guided-learning/powerbi-learning-0-2-get-started-power-bi-desktop).

С помощью **Power BI Desktop** вы можете подключиться к источнику данных. Для этого импортируйте копию данных в **Power BI Desktop** или подключитесь непосредственно к источнику данных с помощью **DirectQuery**.

Ниже описаны различия между **импортом** и **DirectQuery**.

| Импорт | DirectQuery |
| --- | --- |
| Таблицы, столбцы *и данные* импортируются или копируются в **Power BI Desktop**. При работе с представлениями **Power BI Desktop** запрашивает копию данных. Чтобы определить изменения в базовых данных, вам нужно повторно обновить или импортировать готовый текущий набор данных. |В *Power BI Desktop* импортируются или копируются только **таблицы и столбцы**. При работе с представлениями **Power BI Desktop** запрашивает базовый источник данных (это значит, что вы всегда просматриваете актуальные данные). |

Дополнительные сведения о подключении к источнику данных см. в [этой статье](power-bi-embedded-connect-datasource.md).

Когда вы сохраните документ в **Power BI Desktop**, будет создан PBIX-файл. Этот файл содержит ваш отчет. Кроме того, при импорте данных PBIX-файл будет содержать полный набор данных, а при использовании **DirectQuery**— только схему набора данных. В рабочей области PBIX-файл развертывается программным способом с помощью [API импорта Power BI](https://msdn.microsoft.com/library/mt711504.aspx).

> [!NOTE]
> **Power BI Embedded** предусматривает дополнительные API. С их помощью можно изменять сервер и базу данных, на которые указывает ваш набор данных, а также указывать данные учетной записи службы для подключения к базе данных. Дополнительные сведения см. в статьях [Post SetAllConnections](https://msdn.microsoft.com/library/mt711505.aspx) (Публикация SetAllConnections) и [Patch Gateway Datasource](https://msdn.microsoft.com/library/mt711498.aspx) (Исправление источника данных шлюза).

## <a name="create-power-bi-datasets-and-reports-using-apis"></a>Создание наборов данных и отчетов Power BI с помощью API

### <a name="datsets"></a>Наборы данных

Вы можете создавать наборы данных в Power BI Embedded с помощью REST API. Затем данные можно передать в свой набор данных. Это позволит работать с данными без использования Power BI Desktop. Дополнительные сведения см. в статье о [публикации наборов данных](https://msdn.microsoft.com/library/azure/mt778875.aspx).

### <a name="reports"></a>Отчеты

С помощью API JavaScript можно создать отчет на основе набора данных прямо в приложении. Дополнительные сведения см. в статье [Создание отчета из набора данных в Power BI Embedded](power-bi-embedded-create-report-from-dataset.md).

## <a name="see-also"></a>См. также

[Приступая к работе с примером Microsoft Power BI Embedded](power-bi-embedded-get-started-sample.md)  
[Аутентификация и авторизация в Power BI Embedded](power-bi-embedded-app-token-flow.md)  
[Внедрение отчета в Power BI Embedded](power-bi-embedded-embed-report.md)  
[Создание отчета из набора данных в Power BI Embedded](power-bi-embedded-create-report-from-dataset.md)
[Сохранение отчетов в Power BI Embedded](power-bi-embedded-save-reports.md)  
[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[Пример внедрения JavaScript](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
У вас имеются и другие вопросы? [Попробуйте задать их в сообществе Power BI](http://community.powerbi.com/)


