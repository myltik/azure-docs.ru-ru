---
title: Развертывание и вызов веб-интерфейсов API и интерфейсов REST API из приложений логики Azure | Документация Майкрософт
description: Развертывание и вызов веб-интерфейсов API и интерфейсов REST API в рабочих процессах для интеграции системы с приложениями логики Azure
keywords: веб-интерфейсы API, интерфейсы REST API, соединители, рабочие процессы, интеграция системы, проверка подлинности
services: logic-apps
author: stepsic-microsoft-com
manager: jeconnoc
editor: ''
documentationcenter: ''
ms.assetid: f113005d-0ba6-496b-8230-c1eadbd6dbb9
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/26/2017
ms.author: LADocs; stepsic
ms.openlocfilehash: e808a463beb312df6ee2f8fc4378f72755dcdf33
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35299015"
---
# <a name="deploy-and-call-custom-apis-from-logic-app-workflows"></a>Развертывание и вызов пользовательских API из рабочих процессов приложения логики

После [создания пользовательских API](./logic-apps-create-api-app.md) для использования в рабочих процессах приложений логики необходимо развернуть эти API, прежде чем их вызывать. API-интерфейсы можно развернуть в качестве [веб-приложений](../app-service/app-service-web-overview.md), но лучше их развернуть в качестве [приложений API](../app-service/app-service-web-tutorial-rest-api.md), что облегчит создание, размещение и использование API-интерфейсов как в облаке, так и в локальной среде. Не нужно изменять код в API-интерфейсах, просто разверните свой код в приложении API. API-интерфейсы можно разместить в [службе приложений Azure](../app-service/app-service-web-overview.md). Это служба PaaS (платформа как услуга), предоставляющая удобное размещение API с высоким уровнем масштабирования.

Хотя из приложения логики можно вызвать любой API, для получения наилучших результатов добавьте [метаданные OpenAPI (ранее — Swagger)](http://swagger.io/specification/), которые описывают операции и параметры вашего API. Этот файл OpenAPI позволяет упростить интеграцию API и улучшить его работу с приложениями логики.

## <a name="deploy-your-api-as-a-web-app-or-api-app"></a>Развертывание API в качестве веб-приложения или приложения API

Прежде чем вызвать настраиваемый API из приложения логики, разверните его в качестве веб-приложения или приложения API в службе приложений Azure. Также задайте свойства определения API и включите [общий доступ к ресурсам независимо от источника (CORS)](../app-service/app-service-web-overview.md) для веб-приложения или приложения API, чтобы конструктор приложений логики мог считать файл OpenAPI.

1. На [портале Azure](https://portal.azure.com) выберите веб-приложение или приложение API.

2. В открывшемся меню приложения в разделе **API** выберите **Определение API**. Задайте в качестве значения параметра **Расположение определения API** URL-адрес файла OpenAPI swagger.json.

   Как правило, URL-адрес отображается в таком формате: `https://{name}.azurewebsites.net/swagger/docs/v1)`

   ![Ссылка на файл OpenAPI для настраиваемого API](./media/logic-apps-custom-api-deploy-call/custom-api-swagger-url.png)

3. В разделе **API** выберите **CORS**. Задайте для параметра **Разрешенные источники** политики CORS значение **"*"** (разрешить все).

   Этот параметр разрешает запросы из конструктора приложений логики.

   ![Разрешение запросов из конструктора приложений логики для настраиваемого API](./media/logic-apps-custom-api-deploy-call/custom-api-cors.png)

Дополнительные сведения см. в статье [Размещение API-интерфейсов RESTful с поддержкой CORS в службе приложений Azure](../app-service/app-service-web-tutorial-rest-api.md).

## <a name="call-your-custom-api-from-logic-app-workflows"></a>Вызов настраиваемого API из рабочих процессов приложения логики

После настройки CORS и свойств определения API триггеры и действия в пользовательском API должны быть доступны, чтобы вы могли включить их в рабочий процесс приложения логики. 

*  Чтобы просмотреть веб-сайты с URL-адресами OpenAPI, просмотрите веб-сайты, относящиеся к вашей подписке, в конструкторе приложений логики.

*  Чтобы просмотреть доступные действия и входные данные, указав документ OpenAPI, используйте действие [HTTP и Swagger](../connectors/connectors-native-http-swagger.md).

*  Чтобы вызвать любой API, даже тот, который не имеет документа OpenAPI или не предоставляет его, всегда можно создать запрос с помощью [действия HTTP](../connectors/connectors-native-http.md).

## <a name="next-steps"></a>Дополнительная информация

* [Custom connector overview](../logic-apps/custom-connector-overview.md) (Обзор настраиваемых соединителей)