---
title: "Сведения о соединителе расшифровки сообщений AS2 из Пакета интеграции Enterprise | Документация Майкрософт"
description: "Узнайте, как использовать партнеры с пакетом интеграции Enterprise и приложениями логики."
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: erikre
editor: 
ms.assetid: cf44af18-1fe5-41d5-9e06-cc57a968207c
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2016
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 5836e0d2bb7d1d97e9f175396f51047af0f84ca5


---
# <a name="get-started-with-decode-as2-message"></a>Начало работы с расшифровкой сообщений AS2
Подключитесь к соединителю расшифровки сообщений AS2, чтобы обеспечить безопасность и надежность при передаче сообщений. Эта служба выполняет цифровое подписывание, расшифровку и подтверждение через уведомления о состоянии сообщений (MDN).

## <a name="create-the-connection"></a>Создание подключения
### <a name="prerequisites"></a>Предварительные требования
* Учетная запись Azure. Вы можете создать [бесплатную учетную запись](https://azure.microsoft.com/free).
* Для работы с соединителем расшифровки сообщений AS2 потребуется учетная запись интеграции. Подробнее о создании [учетной записи интеграции](app-service-logic-enterprise-integration-create-integration-account.md), [партнеров](app-service-logic-enterprise-integration-partners.md) и [соглашений AS2](app-service-logic-enterprise-integration-as2.md).

### <a name="connect-to-decode-as2-message-using-the-following-steps"></a>Действия для подключения к соединителю расшифровки сообщений AS2.
1. Пример см. в статье о [создании приложения логики](app-service-logic-create-a-logic-app.md).
2. Этот соединитель не содержит триггеров. Используйте для запуска приложения логики другие триггеры, например триггер запроса.  В конструкторе приложений логики добавьте триггер, а затем действие.  В раскрывающемся списке выберите параметр "Показать API, управляемые Майкрософт", а затем введите в поле поиска AS2.  Выберите AS2 — Decode AS2 message (AS2 — расшифровка сообщений AS2).
   
    ![Поиск AS2](./media/app-service-logic-enterprise-integration-AS2connector/as2decodeimage1.png)
3. Если до этого вы не создавали подключения к учетной записи интеграции, вам будет предложено ввести сведения о подключении.
   
    ![Создание подключения к системе интеграции](./media/app-service-logic-enterprise-integration-AS2connector/as2decodeimage2.png)
4. Введите данные учетной записи интеграции.  Свойства, отмеченные звездочкой, являются обязательными.
   
   | Свойство | Сведения |
   | --- | --- |
   | Имя подключения* |Введите имя подключения. |
   | Учетная запись интеграции* |Введите имя учетной записи интеграции. Убедитесь, что учетная запись интеграции и приложение логики находятся в одном расположении Azure. |
   
      Введенные сведения о подключении будут выглядеть так:
   
      ![подключение к системе интеграции](./media/app-service-logic-enterprise-integration-AS2connector/as2decodeimage3.png)
5. Нажмите кнопку **Создать**
6. Обратите внимание, что было создано подключение.  Теперь перейдите к другим действиям в приложении логики.
   
    ![подключение к системе интеграции создано](./media/app-service-logic-enterprise-integration-AS2connector/as2decodeimage4.png) 
7. Выберите текст и заголовки из выходных данных запроса
   
    ![заполнение обязательных полей](./media/app-service-logic-enterprise-integration-AS2connector/as2decodeimage5.png) 

## <a name="the-as2-decode-does-the-following"></a>Соединитель расшифровки AS2 выполняет следующие функции:
* обрабатывает заголовки AS2 и HTTP;
* проверяет подпись (если настроено);
* расшифровывает сообщения (если настроено);
* распаковывает сообщения (если настроено);
* сопоставляет полученный MDN с первоначальным исходящим сообщением;
* обновляет и сопоставляет записи в базе данных неотрекаемости;
* сохраняет записи отчетов о состоянии AS2;
* кодирует методом base64 содержимое полезных выходных данных;
* определяет, требуется ли MDN, а также синхронность или асинхронность MDN, исходя из установленной в соглашении AS2 конфигурации;
* создает синхронное или асинхронное MDN (в зависимости от конфигурации соглашения);
* задает маркеры корреляции и свойства для MDN.

## <a name="try-it-for-yourself"></a>Попробуйте сами
Почему бы не попробовать. Щелкните [здесь](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/) , чтобы развернуть собственное полностью работоспособное приложение логики с применением функций AS2 Logic Apps. 

## <a name="next-steps"></a>Дальнейшие действия
[Обзор пакета интеграции Enterprise](app-service-logic-enterprise-integration-overview.md "Обзор пакета интеграции Enterprise") 




<!--HONumber=Nov16_HO3-->


