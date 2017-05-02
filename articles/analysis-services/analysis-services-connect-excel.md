---
title: "Подключение к Azure Analysis Services с помощью Excel | Документы Майкрософт"
description: "Сведения о подключении к серверу Azure Analysis Services с помощью Excel."
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
ms.date: 04/12/2017
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: a83e313413d87179b89e78d2b90a08a9bcc92fa1
ms.lasthandoff: 04/17/2017


---
# <a name="connect-with-excel"></a>Подключение с помощью Excel

Создав в Azure сервер и развернув на нем табличную модель, вы сможете подключиться к нему для работы с данными. 


## <a name="connect-in-excel"></a>Подключение в Excel

Подключиться к серверу из Excel можно с помощью функции "Получить данные" (в Excel 2016) или Power Query (в более ранних версиях). Подключение с помощью мастера импорта таблиц в Power Pivot не поддерживается. 

**Подключение в Excel 2016**

1. В Excel 2016 на ленте **Данные** последовательно выберите **Получить внешние данные** > **Из других источников** > **Из служб Analysis Services**.

2. В мастере подключения к данным вставьте имя сервера из буфера обмена в поле **Имя сервера**. Затем для параметра **Учетные данные** выберите значение **Использовать следующие имя пользователя и пароль**, введите имя пользователя, например nancy@adventureworks.com, и пароль для него.

    ![Подключение из Excel: вход](./media/analysis-services-connect-excel/aas-connect-excel-logon.png)

3. В разделе **Выбор базы данных и таблицы** выберите нужную базу данных, а также модель или перспективу, и нажмите кнопку **Готово**.
   
    ![Подключение из Excel: выбор модели](./media/analysis-services-connect-excel/aas-connect-excel-select.png)


## <a name="see-also"></a>См. также
[Клиентские библиотеки](analysis-services-data-providers.md)   
[Управление службами Analysis Services](analysis-services-manage.md)     



