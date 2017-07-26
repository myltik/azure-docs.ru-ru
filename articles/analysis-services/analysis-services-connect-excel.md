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
ms.date: 06/01/2017
ms.author: owend
ms.translationtype: Human Translation
ms.sourcegitcommit: 43aab8d52e854636f7ea2ff3aae50d7827735cc7
ms.openlocfilehash: ff96602642c56a3cd02aeada056c059573707731
ms.contentlocale: ru-ru
ms.lasthandoff: 06/03/2017


---
# <a name="connect-with-excel"></a>Подключение с помощью Excel

Создав в Azure сервер и развернув на нем табличную модель, вы сможете подключиться к нему для работы с данными.


## <a name="connect-in-excel"></a>Подключение в Excel

Подключиться к серверу из Excel можно с помощью функции "Получить данные" в Excel 2016. Подключение с помощью мастера импорта таблиц в Power Pivot не поддерживается. 

**Подключение в Excel 2016**

1. В Excel 2016 на ленте **Данные** последовательно выберите **Получить внешние данные** > **Из других источников** > **Из служб Analysis Services**.

2. В мастере подключения к данным введите имя сервера, включая протокол и универсальный код ресурса (URI), в поле **Имя сервера**. Затем для параметра **Учетные данные** выберите значение **Использовать следующие имя пользователя и пароль**, введите имя пользователя, например nancy@adventureworks.com, и пароль для него.

    ![Подключение из Excel: вход](./media/analysis-services-connect-excel/aas-connect-excel-logon.png)

3. В разделе **Выбор базы данных и таблицы** выберите нужную базу данных, а также модель или перспективу, и нажмите кнопку **Готово**.
   
    ![Подключение из Excel: выбор модели](./media/analysis-services-connect-excel/aas-connect-excel-select.png)


## <a name="see-also"></a>См. также
[Клиентские библиотеки](analysis-services-data-providers.md)   
[Управление службами Analysis Services](analysis-services-manage.md)     



