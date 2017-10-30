---
title: "Описание пользовательских API-интерфейсов и соединителей с помощью Postman (Azure Logic Apps) | Документация Майкрософт"
description: "Создавайте коллекции Postman, чтобы описывать, группировать и упорядочивать пользовательские API-интерфейсы и соединители"
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 072d544e5d29c4abb3d69651e53cfb33d5e0c9e9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="describe-custom-apis-and-custom-connectors-with-postman"></a>Описание пользовательских API и соединителей с помощью Postman

Чтобы упростить и ускорить разработку [пользовательских API](../logic-apps/logic-apps-create-api-app.md) и [настраиваемых соединителей](../logic-apps/custom-connector-overview.md), вы можете вместо документов OpenAPI создавать коллекции [Postman](https://www.getpostman.com/), которые описывают API-интерфейсы и соединители. Коллекции Postman помогают упорядочивать и группировать связанные запросы к API. Например, Postman можно применять при создании соединителей для Azure Logic Apps, Microsoft Flow или Microsoft PowerApps. 

В этом руководстве описано создание [коллекции Postman](https://www.getpostman.com/docs/postman/collections/creating_collections) на примере [API обнаружения языка](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) из [текстовой аналитики Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/text-analytics/). Этот API определяет язык, тональность и ключевые фразы в переданном тексте.

## <a name="prerequisites"></a>Предварительные требования

* Если вы еще не работали с Postman, сначала [установите приложение Postman](https://www.getpostman.com/apps).

* Подписка Azure. Если у вас нет подписки, вы можете для начала использовать [бесплатную учетную запись Azure](https://azure.microsoft.com/free/). Или зарегистрируйтесь для получения [подписки с оплатой по мере использования](https://azure.microsoft.com/pricing/purchase-options/).

* Если у вас есть подписка Azure, зарегистрируйтесь для использования API-интерфейсов текстовой аналитики, выполнив [задачу 1](../cognitive-services/text-analytics/how-tos/text-analytics-how-to-signup.md). 

## <a name="create-a-postman-collection"></a>Создание коллекции Postman

Прежде чем создавать коллекцию, следует создать HTTP-запрос к конечной точке API. 

### <a name="create-an-http-request-for-your-api"></a>Создание HTTP-запроса для API

1. Откройте приложение Postman, чтобы создать в нем [HTTP-запрос](https://www.getpostman.com/docs/postman/sending_api_requests/requests) к конечной точке API. Дополнительные сведения см. в [документации по запросам в Postman](https://www.getpostman.com/docs/postman/sending_api_requests/requests).

   1. На вкладке **Builder** (Построитель) выберите метод HTTP, введите URL-адрес запроса для конечной точки API и выберите протокол авторизации, если он используется. 
   Когда все будет готово, выберите элемент **Параметры**.

      Для этого руководства вы можете использовать параметры из этого примера:

      ![Создание запроса: метод HTTP, URL-адрес запроса, авторизация](./media/custom-connector-api-postman-collection/01-create-api-http-request.png)

      | Параметр | Рекомендуемое значение | 
      | --------- | --------------- | 
      | **HTTP method** (Метод HTTP) | ПУБЛИКАЦИЯ | 
      | **Request URL** (URL-адрес запроса) | `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/languages` | | 
      | **Авторизация** | No Auth (Без авторизации) | | 
      ||| 

   2. Здесь вы можете ввести пары "ключ — значение", которые будут использоваться в качестве параметров запроса или пути в URL-адресе запроса. Postman объединяет эти элементы в строку запроса.
   Когда все будет готово, выберите элемент **Headers** (Заголовки).

      ![Продолжение запроса: параметры](./media/custom-connector-api-postman-collection/02-create-api-http-request-params.png)

      | Параметр | Рекомендуемое значение | 
      | --------- | --------------- | 
      | **Params** (Параметры) | **Key** (Ключ): numberOfLanguagesToDetect </br>**Value** (Значение): 1 | 
      ||| 

   3. Введите пары "ключ — значение" для заголовка запроса. 
   Имя заголовка может быть любой строкой. Стандартные заголовки HTTP можно выбрать из раскрывающегося списка. Когда все будет готово, выберите элемент **Body** (Текст). 
   
      ![Продолжение запроса: заголовки](./media/custom-connector-api-postman-collection/03-create-api-http-request-header.png)

      | Параметр | Значение | 
      | --------- | ----- | 
      | **Заголовки** | **Key** (Ключ): Ocp-Apim-Subscription-Key </br>**Value** (Значение): *ключ_подписки_вашего_API*, который вы можете найти в настройках учетной записи Cognitive Services <p>**Key** (Ключ): Content-Type </br> **Value** (Значение): application/json | 
      ||| 

   4. Введите содержимое, которое будет отправляться в тексте запроса. 
   Чтобы проверить работу запроса, получив ответ, нажмите кнопку **Send** (Отправить). 
   
      ![Продолжение запроса: текст](./media/custom-connector-api-postman-collection/04-create-api-http-request-body.png)

      | Параметр | Рекомендуемое значение | 
      | --------- | --------------- |    
      | **Текст** | ```{"documents": [{ "id": "1", "text": "Hello World"}]}``` | 
      ||| 

      Поле ответа содержит полный ответ от API-интерфейса, включая результат или ошибку, если таковая произойдет.

      ![Получение ответа на запрос](./media/custom-connector-api-postman-collection/05-create-api-http-request-response.png)

2. Когда вы убедитесь, что запрос правильно работает, сохраните его в коллекции Postman. 

   1. Нажмите **Сохранить**. 
      
      ![Нажмите кнопку Save (Сохранить)](./media/custom-connector-api-postman-collection/06a-save-request.png)
 
   2. В окне **Save Request** (Сохранение запроса) заполните поля **Request Name** (Имя запроса) и, при необходимости, **Request description** (Описание запроса). 

      > [!NOTE]
      > Позже эти значения будут использоваться для настраиваемого соединителя. Поэтому важно использовать те же значения, которые вы позже примените для сводки и описания работы API.

   3. Выберите **+ Create Collection** (+ Создать коллекцию) и укажите имя коллекции. 
   Выберите флажок, который создает папку коллекции, а затем щелкните **Save to *имя_коллекции_Postman*** (Сохранить в <имя_коллекции_Postman>).

      ![Сохранение запроса](./media/custom-connector-api-postman-collection/06b-save-request.png)

### <a name="save-the-request-response"></a>Сохранение ответа на запрос

После сохранения запроса можно сохранить ответ на него. Это позволит отобразить пример ответа, когда вы позднее будете загружать запрос.

1. Над окном ответа нажмите кнопку **Save Response** (Сохранить ответ). 

   ![Сохранение ответа](./media/custom-connector-api-postman-collection/07-create-api-http-request-save-response.png)

   Настраиваемые соединители поддерживают только один ответ для каждого запроса. 
   Если вы сохраните несколько ответов на запрос, будет использоваться только первый из них.

2. Укажите имя для вашего примера и щелкните **Save Example** (Сохранить пример).

3. Продолжайте наполнять коллекцию Postman дополнительными запросами и ответами.

### <a name="export-your-postman-collection"></a>Экспорт коллекции Postman

1. Когда все будет готово, экспортируйте коллекцию в JSON-файл.

   ![Экспорт коллекции](./media/custom-connector-api-postman-collection/08-export-http-request.png)

2. Выберите для экспорта формат **Collection v1** (Коллекция (версия 1)) и перейдите к расположению, в котором хотите сохранить этот JSON-файл.

   > [!NOTE]
   > Сейчас для настраиваемых соединителей можно использовать только версию 1.

   ![Выбор формата экспорта: Collection v1 (Коллекция (версия 1))](./media/custom-connector-api-postman-collection/09-export-format.png)
   
Теперь вы можете применить эту коллекцию Postman для создания пользовательских API-интерфейсов и соединителей. Полученный в результате экспорта коллекции JSON-файл можно импортировать в Logic Apps, Flow или PowerApps.

> [!IMPORTANT]
> Когда вы будете создавать настраиваемый соединитель из коллекции Postman, не забудьте удалить заголовок `Content-type` из действий и триггеров. Некоторые целевые службы, например Flow, автоматически добавляют этот заголовок. Также удалите заголовки аутентификации из раздела `securityDefintions` для действий и триггеров.

## <a name="next-steps"></a>Дальнейшие действия

* [Регистрация настраиваемых соединителей в Logic Apps](../logic-apps/logic-apps-custom-connector-register.md)
* [Регистрация соединителя в Microsoft Flow](https://ms.flow.microsoft.com/documentation/register-custom-api/#register-your-custom-connector)
* [Регистрация соединителя в PowerApps](https://powerapps.microsoft.com/tutorials/register-custom-api/#register-your-custom-connector)
* [Часто задаваемые вопросы о настраиваемых соединителях](../logic-apps/custom-connector-faq.md)
