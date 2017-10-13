---
title: "Подключение к Azure Analysis Services с помощью Power BI | Документы Майкрософт"
description: "Сведения о подключении к серверу Azure Analysis Services с помощью Power BI."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 08/15/2017
ms.author: owend
ms.openlocfilehash: 3a2af043feddb4a1d6d63f50e838c8a39035449f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="connect-with-power-bi"></a>Подключение с помощью Power BI

Когда вы создадите Azure сервер и развернете в него табличную модель, пользователи вашей организации смогут подключиться к нему для использования данных. 

> [!TIP]
> Обязательно используйте последнюю версию [Power BI Desktop](https://powerbi.microsoft.com/desktop/).
> 
> 
  
## <a name="connect-in-power-bi-desktop"></a>Подключение в Power BI Desktop

1. В Power BI Desktop выберите **Получить данные** > **Azure** > **База данных Azure Analysis Services**.

2. В поле **Сервер** укажите имя сервера. 
    
    Не забудьте включить полный URL-адрес. Например, asazure://westcentralus.asazure.windows.net/advworks.

3. В поле **База данных** вставьте имя базы данных табличной модели или перспективы, к которой нужно подключиться, если вы знаете это имя. В противном случае вы можете оставить это поле пустым и выбрать базу данных или перспективу позже.

4. Оставьте стандартное значение **Подключение в реальном времени**, а затем нажмите **Подключиться**. 

5. При отображении запроса введите свои учетные данные. 

6. В разделе **Навигатор** разверните узел сервера и выберите модель или перспективу, к которой вы хотите подключиться, затем нажмите **Подключиться**. Щелкнув модель или перспективу, можно отобразить все объекты для этого представления.

    Модель открывается в Power BI Desktop с пустым отчетом в представлении "Отчет". Список "Поля" содержит все нескрытые объекты модели. Состояние подключения отображается в правом нижнем углу.

## <a name="connect-in-power-bi-service"></a>Подключение в Power BI (служба)

1. Создайте файл Power BI Desktop с возможностью активного подключения к нужной модели на сервере.
2. В [Power BI](https://powerbi.microsoft.com) последовательно выберите **Получить данные** > **Файлы**. Найдите и выберите созданный файл.



## <a name="see-also"></a>См. также
[Подключение к службам Azure Analysis Services](analysis-services-connect.md)   
[Клиентские библиотеки](analysis-services-data-providers.md)

