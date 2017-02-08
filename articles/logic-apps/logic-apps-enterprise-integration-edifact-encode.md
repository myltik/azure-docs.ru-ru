---
title: "Сведения о соединителе шифрования сообщений EDIFACT из Пакета интеграции Enterprise | Документация Майкрософт"
description: "Узнайте, как использовать партнеры с пакетом интеграции Enterprise и приложениями логики."
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
ms.date: 08/15/2016
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: dc8c9eac941f133bcb3a9807334075bfba15de46
ms.openlocfilehash: 7f9feb7b9ae01c18712f14035ac8c56256cf4d26


---
# <a name="get-started-with-encode-edifact-message"></a>Начало работы с соединителем кодирования сообщений EDIFACT
Проверяет EDI и свойства для конкретного партнера. 

## <a name="create-the-connection"></a>Создание подключения
### <a name="prerequisites"></a>Предварительные требования
* Учетная запись Azure. Вы можете создать [бесплатную учетную запись](https://azure.microsoft.com/free).
* Для работы с соединителем кодирования сообщений EDIFACT потребуется учетная запись интеграции. См. подробные сведения о создании [учетной записи интеграции](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), [партнеров](logic-apps-enterprise-integration-partners.md) и [соглашений EDIFACT](../logic-apps/logic-apps-enterprise-integration-edifact.md).

### <a name="connect-to-decode-edifact-message-using-the-following-steps"></a>Этапы подключения к соединителю расшифровки сообщений EDIFACT.
1. Пример см. в статье о [создании приложения логики](../logic-apps/logic-apps-create-a-logic-app.md).
2. Этот соединитель не содержит триггеров. Используйте для запуска приложения логики другие триггеры, например триггер запроса.  В конструкторе приложений логики добавьте триггер, а затем действие.  В раскрывающемся списке выберите параметр "Показать API, управляемые Майкрософт", а затем введите в поле поиска "EDIFACT".  Выберите пункт "Encode EDIFACT Message by agreement name" (Кодирование сообщений EDIFACT по названию соглашения) или "Encode to EDIFACT message by identities" (Кодирование в сообщение EDIFACT по идентификаторам).
   
    ![поиск EDIFACT](media/logic-apps-enterprise-integration-edifact-encode/edifactdecodeimage1.png)  
3. Если до этого вы не создавали подключения к учетной записи интеграции, вам будет предложено ввести сведения о подключении.
   
    ![создание подключения к учетной записи интеграции](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage1.png)  
4. Введите данные учетной записи интеграции.  Свойства, отмеченные звездочкой, являются обязательными.
   
   | Свойство | Сведения |
   | --- | --- |
   | Имя подключения* |Введите имя подключения. |
   | Учетная запись интеграции* |Введите имя учетной записи интеграции. Убедитесь, что учетная запись интеграции и приложение логики находятся в одном расположении Azure. |
   
    Введенные сведения о подключении будут выглядеть так:
   
    ![подключение к учетной записи интеграции](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage2.png)
5. Нажмите кнопку **Создать**
6. Подключение создано.
   
    ![сведения о подключении к учетной записи интеграции](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage4.png)

#### <a name="encode-edifact-message-by-agreement-name"></a>Кодирование сообщений EDIFACT по названию соглашения
1. Укажите имя соглашения EDIFACT и XML-сообщение для кодирования.
   
   ![заполнение обязательных полей](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage6.png)

#### <a name="encode-edifact-message-by-identities"></a>Кодирование сообщения EDIFACT по идентификаторам
1. Укажите идентификатор отправителя, квалификатор отправителя, идентификатор получателя и квалификатор получателя, как указано в соглашении EDIFACT.  Выберите XML-сообщение для кодирования.
   
    ![заполнение обязательных полей](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage7.png)

## <a name="edifact-encode-does-following"></a>Кодирование EDIFACT выполняет следующие действия.
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




<!--HONumber=Jan17_HO3-->


