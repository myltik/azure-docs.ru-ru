---
title: Кодирование сообщений AS2 в Azure Logic Apps | Документация Майкрософт
description: Использование кодировщика AS2 (входит в пакет интеграции Enterprise) в Azure Logic Apps
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: jeconnoc
editor: ''
ms.assetid: 332fb9e3-576c-4683-bd10-d177a0ebe9a3
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: fe8a2b00f15fa737c8ed343a47e1cab1c260346b
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35297936"
---
# <a name="encode-as2-messages-for-azure-logic-apps-with-the-enterprise-integration-pack"></a>Кодирование сообщений AS2 для Azure Logic Apps с помощью пакета интеграции Enterprise

Чтобы обеспечить безопасность и надежность при передаче сообщений, используйте соединитель кодирования сообщений AS2. Этот соединитель выполняет цифровое подписывание, расшифровку и подтверждение через уведомления о состоянии сообщений (MDN), что также предусматривает невозможность отказа.

## <a name="before-you-start"></a>Перед началом работы

Вам понадобится следующее:

* Учетная запись Azure. Вы можете создать [бесплатную учетную запись](https://azure.microsoft.com/free).
* [Учетная запись интеграции](logic-apps-enterprise-integration-create-integration-account.md), уже определенная и связанная с подпиской Azure. Для работы с соединителем для кодирования сообщений AS2 потребуется учетная запись интеграции.
* В учетной записи интеграции должны быть определены по крайней мере два [партнера](logic-apps-enterprise-integration-partners.md).
* В учетной записи интеграции должно быть определено [соглашение AS2](logic-apps-enterprise-integration-as2.md).

## <a name="encode-as2-messages"></a>Кодирование сообщений AS2

1. [Создание приложения логики](quickstart-create-first-logic-app-workflow.md).

2. В соединителе кодирования сообщений AS2 нет триггеров, поэтому вам придется добавить триггер, чтобы запустить приложение логики (например, триггер запроса). В конструкторе приложений логики добавьте триггер, а затем добавьте действие в приложение логики.

3.  В поле поиска введите "AS2" для фильтрации. Выберите **AS2 — Encode AS2 message** (AS2 — кодирование сообщения AS2).
   
    ![Поиск по запросу "AS2"](./media/logic-apps-enterprise-integration-as2-encode/as2decodeimage1.png)

4. Если ранее вы не создавали подключения к учетной записи интеграции, вам будет предложено сделать это сейчас. Присвойте подключению имя и выберите учетную запись интеграции, которую следует подключить. 
   
    ![установка подключения к учетной записи интеграции](./media/logic-apps-enterprise-integration-as2-encode/as2encodeimage1.png)  

    Свойства со звездочкой обязательные.

    | Свойство | Сведения |
    | --- | --- |
    | Имя подключения* |Введите имя подключения |
    | Учетная запись интеграции* |Выберите имя для учетной записи интеграции. Убедитесь, что учетная запись интеграции и приложение логики находятся в одном регионе Azure. |

5.  Когда все будет готово, данные для подключения должны выглядеть примерно так, как показано в примере. Чтобы завершить создание подключения, нажмите кнопку **Создать**.
   
    ![Сведения для подключения учетной записи интеграции](./media/logic-apps-enterprise-integration-as2-encode/as2encodeimage2.png)

6. Создав подключения, как показано в следующем примере, введите данные в поля **AS2-From identifiers** (AS2 — от идентификаторов) , **AS2-To identifiers** (AS2 — к идентификаторам) в соответствии с соглашением, а также заполните поле **Body** (Текст), предназначенное для полезных данных сообщения.
   
    ![заполнение обязательных полей](./media/logic-apps-enterprise-integration-as2-encode/as2encodeimage3.png)

## <a name="as2-encoder-details"></a>Сведения о кодировщике AS2

Соединитель кодирования AS2 выполняет следующие задачи: 

* Обработка заголовков AS2 и HTTP.
* Подписывание исходящих сообщений (если настроено).
* Кодирование исходящих сообщений (если настроено).
* Сжатие сообщения (если настроено).

## <a name="try-this-sample"></a>Пробное использование примера

Чтобы попробовать развернуть полностью рабочее приложение логики и пример сценария AS2, см. [шаблон и сценарий приложения логики AS2](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/).

## <a name="view-the-swagger"></a>Просмотр Swagger
Ознакомьтесь с [дополнительными сведениями о Swagger](/connectors/as2/). 

## <a name="next-steps"></a>Дополнительная информация
[Обзор пакета интеграции Enterprise](logic-apps-enterprise-integration-overview.md "Обзор пакета интеграции Enterprise") 

