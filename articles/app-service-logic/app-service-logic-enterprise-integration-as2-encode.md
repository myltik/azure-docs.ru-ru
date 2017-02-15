---
title: "Сведения о соединителе шифрования сообщений AS2 из Пакета интеграции Enterprise | Документация Майкрософт"
description: "Узнайте, как использовать партнеры с пакетом интеграции Enterprise и приложениями логики."
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: erikre
editor: 
ms.assetid: 332fb9e3-576c-4683-bd10-d177a0ebe9a3
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2016
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: ebfefbafe6a47beb406bbea0f7c48b61a37ac151


---
# <a name="get-started-with-encode-as2-message"></a>Начало работы с соединителем кодирования сообщений AS2
Подключитесь к соединителю кодирования сообщений AS2, чтобы обеспечить безопасность и надежность при передаче сообщений. Эта служба выполняет цифровое подписывание, кодирование и подтверждение через уведомления о состоянии сообщений (MDN), что также обеспечивает поддержку неотрекаемости от получения.

## <a name="create-the-connection"></a>Создание подключения
### <a name="prerequisites"></a>Предварительные требования
* Учетная запись Azure. Вы можете создать [бесплатную учетную запись](https://azure.microsoft.com/free).
* Для работы с соединителем кодирования сообщений AS2 потребуется учетная запись интеграции. Подробнее о создании [учетной записи интеграции](app-service-logic-enterprise-integration-create-integration-account.md), [партнеров](app-service-logic-enterprise-integration-partners.md) и [соглашений AS2](app-service-logic-enterprise-integration-as2.md).

### <a name="connect-to-encode-as2-message-using-the-following-steps"></a>Подключение к соединителю кодирования сообщений AS2
1. Пример см. в статье о [создании приложения логики](app-service-logic-create-a-logic-app.md).
2. Этот соединитель не содержит триггеров. Используйте для запуска приложения логики другие триггеры, например триггер запроса.  В конструкторе приложений логики добавьте триггер, а затем действие.  В раскрывающемся списке выберите параметр "Показать API, управляемые Майкрософт", а затем введите в поле поиска AS2.  Выберите AS2 - Encode AS2 message (AS2: кодирование сообщений AS2).
   
    ![поиск AS2](./media/app-service-logic-enterprise-integration-AS2connector/as2decodeimage1.png)
3. Если до этого вы не создавали подключения к учетной записи интеграции, вам будет предложено ввести сведения о подключении.
   
    ![установка подключения к учетной записи интеграции](./media/app-service-logic-enterprise-integration-AS2connector/as2encodeimage1.png)  
4. Введите данные учетной записи интеграции.  Свойства, отмеченные звездочкой, являются обязательными.
   
   | Свойство | Сведения |
   | --- | --- |
   | Имя подключения* |Введите имя подключения. |
   | Учетная запись интеграции* |Введите имя учетной записи интеграции. Убедитесь, что учетная запись интеграции и приложение логики находятся в одном расположении Azure. |
   
      Введенные сведения о подключении будут выглядеть так:
   
      ![подключение к учетной записи интеграции установлено](./media/app-service-logic-enterprise-integration-AS2connector/as2encodeimage2.png)  
5. Нажмите кнопку **Создать**
6. Обратите внимание, что было создано подключение.  Введите идентификаторы AS2-From (AS2 — от) и AS2-To (AS2 — кому) (как определено в соглашении) и текст (полезные данные сообщения). 
   
    ![заполнение обязательных полей](./media/app-service-logic-enterprise-integration-AS2connector/as2encodeimage3.png)

## <a name="the-as2-encode-does-the-following"></a>Функции соединителя кодирования AS2
* Обработка заголовков AS2 и HTTP.
* Подписывание исходящих сообщений (если настроено).
* Кодирование исходящих сообщений (если настроено).
* Сжатие сообщения (если настроено).

## <a name="try-it-for-yourself"></a>Попробуйте сами
Почему бы не попробовать. Щелкните [здесь](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/) , чтобы развернуть собственное полностью работоспособное приложение логики с применением функций AS2 Logic Apps.

## <a name="next-steps"></a>Дальнейшие действия
[Обзор пакета интеграции Enterprise](app-service-logic-enterprise-integration-overview.md "Обзор пакета интеграции Enterprise") 




<!--HONumber=Nov16_HO3-->


