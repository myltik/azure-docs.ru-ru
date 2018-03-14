---
title: "Импорт файла Power BI Desktop в Azure Analysis Services | Документация Майкрософт"
description: "Из этой статьи вы узнаете, как импортировать файл Power BI Desktop (PBIX-файл) с помощью портала Azure."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 02/26/2018
ms.author: owend
ms.openlocfilehash: e0be0c69b501d7e93c65bcf23d4dd1b6bfa89caf
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/02/2018
---
# <a name="import-a-power-bi-desktop-file"></a>Импорт файла Power BI Desktop

Можно создать новую модель в службах Azure AS, импортировав файл Power BI Desktop (PBIX-файл). Импортируются метаданные модели, кэшированные данные и подключения к источникам данных. Отчеты и визуализации не импортируются.

**Ограничения**   
- PBIX-модель должна подключаться только к [поддерживаемым источникам данных Analysis Services](analysis-services-datasource.md). 
- Для PBIX-модели не должны быть установлены активные подключения или подключения DirectQuery. 
- Если PBIX-модель подключается к локальным источникам данных, для сервера Analysis Services необходимо настроить [локальный шлюз данных](analysis-services-gateway.md).
- Если PBIX-модель данных содержит метаданные, которые не поддерживаются в Analysis Services, импорт может завершиться ошибкой.

## <a name="to-import-from-pbix"></a>Импорт из PBIX-файла

1. На сервере последовательно выберите **Обзор** > **Дизайнер веб-страниц** и щелкните **Открыть**.

    ![Создание модели на портале Azure](./media/analysis-services-create-model-portal/aas-create-portal-overview-wd.png)

2. Выбрав **Web designer** (Конструктор веб-приложений)  >  **Модели**, нажмите кнопку **+ Добавить**.

    ![Создание модели на портале Azure](./media/analysis-services-create-model-portal/aas-create-portal-models.png)

3. В диалоговом окне **Новая модель** введите имя модели и выберите файл Power BI Desktop.

    ![Диалоговое окно "Новая модель" на портале Azure](./media/analysis-services-import-pbix/aas-import-pbix-new-model.png)

4. В окне **Импорт** найдите и выберите нужный файл.

     ![Диалоговое окно "Подключение" на портале Azure](./media/analysis-services-import-pbix/aas-import-pbix-select-file.png)

## <a name="see-also"></a>См. также

[Создание модели на портале Azure](analysis-services-create-model-portal.md)   
[Подключение к службам Azure Analysis Services](analysis-services-connect.md)  
