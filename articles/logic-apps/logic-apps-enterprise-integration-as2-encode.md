---
title: "Кодирование сообщений AS2 в Azure Logic Apps | Документация Майкрософт"
description: "Узнайте, как использовать кодировщик AS2, входящий в пакет интеграции Enterprise и Logic Apps."
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: 332fb9e3-576c-4683-bd10-d177a0ebe9a3
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: a2ca24c1800a01e6a15af35937a654093cf224af
ms.openlocfilehash: 754ec2e829babd2e5ca3e5e5290db950ef5035e7


---
# <a name="get-started-with-encode-as2-message"></a>Начало работы с соединителем кодирования сообщений AS2
Подключитесь к соединителю кодирования сообщений AS2, чтобы обеспечить безопасность и надежность при передаче сообщений. Эта служба выполняет цифровое подписывание, кодирование и подтверждение через уведомления о состоянии сообщений (MDN), что также обеспечивает поддержку неотрекаемости от получения.

## <a name="prereqs"></a>Предварительные требования
* Учетная запись Azure. Вы можете создать [бесплатную учетную запись](https://azure.microsoft.com/free).
* Для работы с соединителем кодирования сообщений AS2 потребуется учетная запись интеграции. Подробнее о создании [учетной записи интеграции](logic-apps-enterprise-integration-create-integration-account.md), [партнеров](logic-apps-enterprise-integration-partners.md) и [соглашений AS2](logic-apps-enterprise-integration-as2.md).

## <a name="encode-as2-messages"></a>Кодирование сообщений AS2
1. [Создание приложения логики](logic-apps-create-a-logic-app.md).
2. Этот соединитель не содержит триггеров. Используйте для запуска приложения логики другие триггеры, например триггер запроса.  В конструкторе приложений логики добавьте триггер, а затем действие.  В раскрывающемся списке выберите параметр "Показать API, управляемые Майкрософт", а затем введите в поле поиска AS2.  Выберите AS2 – Encode AS2 message (AS2 – кодирование сообщения AS2):
   
    ![поиск AS2](./media/logic-apps-enterprise-integration-as2-encode/as2decodeimage1.png)
3. Если до этого вы не создавали подключений к учетной записи интеграции, то вам будет предложено ввести сведения о подключении:
   
    ![установка подключения к учетной записи интеграции](./media/logic-apps-enterprise-integration-as2-encode/as2encodeimage1.png)  
4. Введите данные учетной записи интеграции.  Свойства, отмеченные звездочкой, являются обязательными:
   
   | Свойство | Сведения |
   | --- | --- |
   | Имя подключения* |Введите имя подключения. |
   | Учетная запись интеграции* |Введите имя учетной записи интеграции. Убедитесь, что учетная запись интеграции и приложение логики находятся в одном расположении Azure. |
   
      После завершения сведения о подключении будут выглядеть следующим образом:
   
      ![подключение к учетной записи интеграции установлено](./media/logic-apps-enterprise-integration-as2-encode/as2encodeimage2.png)  
5. Нажмите кнопку **Создать**.
6. Обратите внимание, что было создано подключение.  Введите идентификаторы AS2-From (AS2 — от) и AS2-To (AS2 — кому) (как определено в соглашении) и текст (полезные данные сообщения):
   
    ![заполнение обязательных полей](./media/logic-apps-enterprise-integration-as2-encode/as2encodeimage3.png)

## <a name="as2-encoder-details"></a>Сведения о кодировщике AS2
Соединитель для кодирования AS2 выполняет следующие функции: 

* Обработка заголовков AS2 и HTTP.
* Подписывание исходящих сообщений (если настроено).
* Кодирование исходящих сообщений (если настроено).
* Сжатие сообщения (если настроено).

## <a name="try-it-yourself"></a>Попробуйте сами
Просто попробуйте. Используйте [шаблон приложения логики AS2 и сценарий](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/) для развертывания полностью рабочего приложения логики.

## <a name="next-steps"></a>Дальнейшие действия
[Обзор пакета интеграции Enterprise](logic-apps-enterprise-integration-overview.md "Обзор пакета интеграции Enterprise") 




<!--HONumber=Jan17_HO5-->


