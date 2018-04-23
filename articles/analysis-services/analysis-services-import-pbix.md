---
title: Импорт файла Power BI Desktop в Azure Analysis Services | Документация Майкрософт
description: Из этой статьи вы узнаете, как импортировать файл Power BI Desktop (PBIX-файл) с помощью портала Azure.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 35bf2ba85017de43788f802b6244d61ed2bb62df
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
---
# <a name="import-a-power-bi-desktop-file"></a>Импорт файла Power BI Desktop

Можно создать новую модель в службах Azure AS, импортировав файл Power BI Desktop (PBIX-файл). Импортируются метаданные модели, кэшированные данные и подключения к источникам данных. Отчеты и визуализации не импортируются.

**Ограничения**   
- PBIX-модель может подключаться только к таким источникам данных, как база данных SQL Azure и хранилище данных SQL Azure. 
- Для PBIX-модели не должны быть установлены активные подключения или подключения DirectQuery. 
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
