---
title: Подключение к Azure Analysis Services с помощью Excel | Документы Майкрософт
description: Сведения о подключении к серверу Azure Analysis Services с помощью Excel.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 8414597c2c394e0b642ff47cba79c87488f56b24
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
---
# <a name="connect-with-excel"></a>Подключение с помощью Excel

Создав в Azure сервер и развернув на нем табличную модель, вы сможете подключиться к нему для работы с данными.


## <a name="connect-in-excel"></a>Подключение в Excel

Подключиться к серверу из Excel можно с помощью функции "Получить данные" в Excel 2016. Подключение с помощью мастера импорта таблиц в Power Pivot не поддерживается. 

**Подключение в Excel 2016**

1. В Excel 2016 на ленте **Данные** последовательно выберите **Получить внешние данные** > **Из других источников** > **Из служб Analysis Services**.

2. В мастере подключения к данным введите имя сервера, включая протокол и универсальный код ресурса (URI), в поле **Имя сервера**. Затем для параметра **Учетные данные** выберите значение **Использовать следующие имя пользователя и пароль**, введите имя пользователя, например nancy@adventureworks.com, и пароль для него.

    > [!NOTE]
    > Если вы входите в систему с учетной записью Майкрософт, Live ID, Yahoo, Gmail, и т. д. или требуется войти с помощью многофакторной проверки подлинности, оставьте поле пароля пустым. Пароль запрашивается после нажатия кнопки "Далее".

    ![Подключение из Excel: вход](./media/analysis-services-connect-excel/aas-connect-excel-logon.png)

3. В разделе **Выбор базы данных и таблицы** выберите нужную базу данных, а также модель или перспективу, и нажмите кнопку **Готово**.
   
    ![Подключение из Excel: выбор модели](./media/analysis-services-connect-excel/aas-connect-excel-select.png)


## <a name="see-also"></a>См. также
[Клиентские библиотеки](analysis-services-data-providers.md)   
[Управление службами Analysis Services](analysis-services-manage.md)     


