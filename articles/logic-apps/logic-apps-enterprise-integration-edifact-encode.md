---
title: "Кодирование сообщений EDIFACT в Azure Logic Apps | Документация Майкрософт"
description: "Узнайте, как использовать кодировщик EDIFACT, входящий в пакет интеграции Enterprise в приложениях логики."
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: 974ac339-d97a-4715-bc92-62d02281e900
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 5bc7011d7b0d22a7f8c11a2fee8d002c24d3467c
ms.openlocfilehash: 94d120cd8a5e33733ecc39af96d2719ad59ab090


---
# <a name="get-started-with-encode-edifact-message"></a>Начало работы с соединителем кодирования сообщений EDIFACT
Проверяет EDI и свойства для конкретного партнера. 

## <a name="prereqs"></a>Предварительные требования
* Учетная запись Azure. Вы можете создать [бесплатную учетную запись](https://azure.microsoft.com/free).
* Для работы с соединителем кодирования сообщений EDIFACT потребуется учетная запись интеграции. См. подробные сведения о создании [учетной записи интеграции](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), [партнеров](logic-apps-enterprise-integration-partners.md) и [соглашений EDIFACT](../logic-apps/logic-apps-enterprise-integration-edifact.md).

## <a name="encode-edifact-messages"></a>Кодирование сообщений EDIFACT
1. [Создайте приложение логики](../logic-apps/logic-apps-create-a-logic-app.md).
2. Этот соединитель не содержит триггеров. Используйте для запуска приложения логики другие триггеры, например триггер запроса.  В конструкторе приложений логики добавьте триггер, а затем действие.  В раскрывающемся списке выберите параметр "Показать API, управляемые Майкрософт", а затем введите в поле поиска "EDIFACT".  Выберите пункт "Encode EDIFACT message by agreement name" (Кодирование сообщения EDIFACT по имени соглашения) или "Encode to EDIFACT message by identities" (Кодирование в сообщение EDIFACT по идентификаторам):
   
    ![поиск EDIFACT](media/logic-apps-enterprise-integration-edifact-encode/edifactdecodeimage1.png)  
3. Если до этого вы не создавали подключений к учетной записи интеграции, то вам будет предложено ввести сведения о подключении:
   
    ![создание подключения к учетной записи интеграции](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage1.png)  
4. Введите данные учетной записи интеграции.  Свойства, отмеченные звездочкой, являются обязательными:
   
   | Свойство | Сведения |
   | --- | --- |
   | Имя подключения* |Введите имя подключения. |
   | Учетная запись интеграции* |Введите имя учетной записи интеграции. Убедитесь, что учетная запись интеграции и приложение логики находятся в одном расположении Azure. |
   
    После завершения сведения о подключении будут выглядеть следующим образом:
   
    ![подключение к учетной записи интеграции](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage2.png)
5. Нажмите кнопку **Создать**.
6. Обратите внимание, что было создано подключение:
   
    ![сведения о подключении к учетной записи интеграции](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage4.png)

#### <a name="encode-edifact-message-by-agreement-name"></a>Кодирование сообщений EDIFACT по названию соглашения
Введите имя соглашения EDIFACT и XML-сообщение для кодирования:
   
   ![заполнение обязательных полей](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage6.png)

#### <a name="encode-edifact-message-by-identities"></a>Кодирование сообщения EDIFACT по идентификаторам
Введите идентификатор отправителя, квалификатор отправителя, идентификатор получателя и квалификатор получателя, как указано в соглашении EDIFACT. Выберите XML-сообщение для кодирования:  
    ![заполнение обязательных полей](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage7.png)

## <a name="edifact-encode-details"></a>Сведения о кодировании EDIFACT
Соединитель для кодирования EDIFACT выполняет следующие функции: 

* Определяет соглашение, сопоставляя квалификатор и идентификатор отправителя с квалификатором и идентификатором получателя.
* Сериализует обмен EDI путем преобразования сообщения в кодировке XML в набор транзакций EDI.
* Генерирует сегменты заголовков и окончаний для наборов транзакций.
* Создает контрольное число обмена, контрольное число группы и контрольное число набора транзакций для каждого исходящего обмена.
* Заменяет разделители в полезных данных.
* Проверяет EDI и свойства для конкретного партнера.
  * Проверяет схемы элементов данных в наборе транзакций на соответствие схеме сообщения.
  * Выполняет проверку EDI для элементов данных в наборе транзакций.
  * Выполняет расширенную проверку для элементов данных в наборе транзакций.
* Создает XML-документ для каждого набора транзакций.
* Запрашивает техническое и (или) функциональное подтверждение (если настроено).
  * Техническим подтверждением является сообщение CONTRL, сообщающее о получении обмена.
  * Функциональным подтверждением является сообщение CONTRL, информирующее о принятии или отклонении полученного обмена, отдельной группы или сообщения, и содержащее список ошибок или неподдерживаемых функциональных возможностей.

## <a name="next-steps"></a>Дальнейшие действия
[Обзор пакета интеграции Enterprise](logic-apps-enterprise-integration-overview.md "Обзор пакета интеграции Enterprise") 




<!--HONumber=Jan17_HO5-->


