---
title: Создание решений B2B в Azure Logic Apps | Документация Майкрософт
description: Получение данных в приложениях логики с использованием функций B2B из пакета интеграции Enterprise
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: jeconnoc
editor: cgronlun
ms.assetid: 20fc3722-6f8b-402f-b391-b84e9df6fcff
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: a27a413ba9a0d974cf90fe842d5fc325ab308a56
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35298123"
---
# <a name="receive-data-in-logic-apps-with-the-b2b-features-in-the-enterprise-integration-pack"></a>Получение данных в приложениях логики с помощью функций B2B из пакета интеграции Enterprise

После создания учетной записи интеграции, для которой определены партнеры и соглашения, вы сможете с помощью [пакета интеграции Enterprise](logic-apps-enterprise-integration-overview.md) создать рабочий процесс "бизнес — бизнес" (B2B) для приложения логики.

## <a name="prerequisites"></a>предварительным требованиям

Чтобы использовать действия AS2 и X12, потребуется учетная запись интеграции Enterprise. Узнайте, [как создать учетную запись интеграции Enterprise](../logic-apps/logic-apps-enterprise-integration-accounts.md).

## <a name="create-a-logic-app-with-b2b-connectors"></a>Создание приложения логики с помощью соединителей B2B

Выполните следующие действия, чтобы создать приложение логики B2B, которое использует действия AS2 и X12 для получения данных от торгового партнера.

1. Создайте приложение логики и [свяжите его с учетной записью интеграции](../logic-apps/logic-apps-enterprise-integration-accounts.md).

2. Добавьте триггер **Request - When an HTTP request is received** (Запрос: при получении HTTP-запроса) в свое приложение логики.

    ![](./media/logic-apps-enterprise-integration-b2b/flatfile-1.png)

3. Добавьте действие **Decode AS2** (Декодирование AS2), щелкнув **Добавить действие**.

    ![](./media/logic-apps-enterprise-integration-b2b/transform-2.png)

4. Введите слово **as2** в поле поиска, чтобы оставить в списке только нужные действия.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-5.png)

5. Выберите действие **AS2 - Decode AS2 message** (AS2: декодирование сообщений AS2).

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-6.png)

6. Добавьте **текст**, который будет использоваться в качестве входных данных. Для этого примера выберите текст HTTP-запроса, который активирует приложение логики. Или введите выражение, которое передает заголовки для поля **HEADERS** (Заголовки):

    @triggerOutputs()['headers']

7. Добавьте необходимые **заголовки** для AS2, которые содержатся в заголовках HTTP-запроса. Для этого примера выберите заголовки HTTP-запроса, который активирует приложение логики.

8. Теперь добавьте действие для декодирования сообщений X12 (Decode X12 message). Выберите **Добавить действие**.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-9.png)

9. Введите слово **x12** в поле поиска, чтобы оставить в списке только нужные действия.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-10.png)

10. Выберите действие **X12 - Decode X12 message** (X12: декодирование сообщений X12).

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-as2message.png)

11. Теперь нужно указать входные данные для этого действия. Здесь это будут выходные данные предыдущего действия AS2.

    Содержимое сообщения по сути представляет собой объект JSON в кодировке base64, поэтому в качестве входных данных нужно указать выражение. 
    В поле ввода **X12 FLAT FILE MESSAGE TO DECODE** (Сообщение для декодирования в формате неструктурированного файла X12) введите следующее выражение:
    
    @base64ToString(body('Decode_AS2_message')?['AS2Message']?['Content'])

    Теперь добавьте шаги для декодирования данных X12, полученных от торгового партнера, и вывода элементов в формате объекта JSON. 
    Чтобы известить партнера о получении данных, можно отправлять ему ответ, содержащий уведомление о состоянии сообщения AS2 (MDN), в действии ответа HTTP.

12. Чтобы добавить действие **Ответ**, щелкните **Добавить действие**.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-14.png)

13. Введите слово **response** (ответ) в поле поиска, чтобы оставить в списке только нужные действия.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-15.png)

14. Выберите действие **Response** (Ответ).

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-16.png)

15. Чтобы использовать MDN из выходных данных действия **Decode X12 message** (Декодирование сообщения X12), в качестве значения для поля **BODY** (Текст) укажите следующее выражение:

    @base64ToString(body('Decode_AS2_message')?['OutgoingMdn']?['Content'])

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-17.png)  

16. Сохраните результаты своих действий.

    ![](./media/logic-apps-enterprise-integration-b2b/transform-5.png)  

Настройка приложения логики B2B завершена. В настоящем приложении вам нужно будет сохранить декодированные данные X12 в бизнес-приложении или в хранилище данных. Для подключения к своим бизнес-приложениям и использования этих API-интерфейсов в приложении логики вы можете добавить дополнительные действия или создать пользовательские API.

## <a name="features-and-use-cases"></a>Функции и варианты использования

* Действия кодирования и декодирования AS2 и X12 позволяют передавать данные между торговыми партнерами в приложениях логики с использованием стандартных протоколов.
* AS2 и X12 можно использовать вместе или по отдельности для обмена данными с торговыми партнерами.
* Действия B2B упрощают создание партнеров и соглашений в учетной записи интеграции и их использование в приложениях логики.
* Вы можете дополнительно расширить возможности приложения логики, настроив обмен данными с другими приложениями и службами, например Salesforce.

## <a name="learn-more"></a>Подробнее
[Дополнительные сведения о Пакете интеграции Enterprise](logic-apps-enterprise-integration-overview.md)
