---
title: "Приступая к работе с метаданными OpenAPI в Функциях Azure | Документация Майкрософт"
description: "Приступая к работе со средством поддержки OpenAPI в Функциях Azure"
services: functions
documentationcenter: 
author: alexkarcher-msft
manager: erikre
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 03/23/2017
ms.author: alkarche
translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: e066cc2ee99b14d13c5238266513edf61ecbe3e1
ms.lasthandoff: 04/15/2017


---
# <a name="creating-openapi-20-swagger-metadata-for-a-function-app-preview"></a>Создание метаданных OpenAPI 2.0 (Swagger) для приложения-функции (предварительная версия)

В этом документе описан пошаговый процесс создания определения OpenAPI для простого интерфейса API, размещенного в Функциях Azure.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

### <a name="what-is-openapi-swagger"></a>Общие сведения об OpenAPI (Swagger)
[Метаданные Swagger](http://swagger.io/) — это файл, который определяет функциональные возможности и режимы работы интерфейса API и позволяет другому программному обеспечению использовать функцию размещения REST API. Такие предложения Майкрософт, как PowerApps и [приложения API](https://docs.microsoft.com/azure/app-service-api/app-service-api-dotnet-get-started#a-idcodegena-generate-client-code-for-the-data-tier), а также сторонние инструменты для разработчиков, например [Postman](https://www.getpostman.com/docs/importing_swagger) и [многие другие пакеты](http://swagger.io/tools/), позволяют использовать API с помощью определения Swagger.

## <a name="prepare-function"></a>Создание функции с простым интерфейсом API
  Чтобы создать определение OpenAPI, сначала необходимо создать функцию с простым интерфейсом API. Если интерфейс API уже размещен в приложении-функции, можете перейти непосредственно к следующему разделу.
1. Создайте новое приложение-функцию.
  1. Перейдите на [портал Azure](https://portal.azure.com) > `+ New` и найдите "Приложение-функция".
1. Создайте новую функцию "Триггер HTTP" в новом приложении-функции.
  1. Функция заранее заполняется кодом, определяющим очень простой интерфейс REST API.
  1. Любая строка, переданная функции в качестве параметра запроса или в самом запросе, возвращается в виде "Hello {ввод}".
1. Перейдите на вкладку `Integrate` новой функции "Триггер HTTP".
  1. Переведите переключатель `Allowed HTTP methods` в положение `Selected methods`.
  1. В `Selected HTTP methods` снимите флажки рядом со всеми командами, за исключением POST.
    ![Выбор методов HTTP](./media/functions-api-definition-getting-started/selectedHTTPmethods.png)
  1. Сделав это, вы упростите определение API в дальнейшем.

## <a name="enable"></a>Включение поддержки определения API
1. Перейдите на страницу `your function name` > `API Definition (preview)`
1. Задайте для `API Definition Source` значение `Internal`.
  1. Выполнив это действие, вы включите набор параметров OpenAPI для приложения-функции, в том числе конечную точку для размещения файла OpenAPI из домена приложения-функции, встроенную копию [редактора OpenAPI](http://editor.swagger.io) и генератор кратких определений.
![Включенное определение](./media/functions-api-definition-getting-started/enabledefinition.png)

## <a name="create-definition"></a>Создание определения API на основе шаблона
1. Щелкните `Generate API Definition template`.
  1. На этом этапе выполняется проверка приложения-функции на наличие функций "Триггер HTTP", а данные, полученные в файле functions.json, используются для создания документа OpenAPI.
2. Добавьте объект операции в `paths: /api/yourfunctionroute post:`.
  1. Краткий документ OpenAPI представляет собой структуру полного документа OpenAPI. Чтобы превратить его в полное определение OpenAPI, например в объекты операций и шаблоны ответа, потребуются дополнительные метаданные.
  1. В приведенном ниже примере объекта операции заполнен раздел produces/consumes, указан объект parameter и объект response.
  
  ```yaml
      post:
        operationId: /api/yourfunctionroute/post
        consumes: [application/json]
        produces: [application/json]
        parameters:
          - name: name
            in: body
            description: Your name
            x-ms-summary: Your name
            required: true
            schema:
              type: object
              properties:
                name:
                  type: string
        description: >-
          Replace with Operation Object
          #http://swagger.io/specification/#operationObject
        responses:
          '200':
            description: A Greeting
            x-ms-summary: Your name
            schema:
              type: string
        security:
          - apikeyQuery: []
  ```

> [!NOTE]
>  x-ms-summary содержит имя, отображаемое в Logic Apps, Flow и PowerApps.
>
> Дополнительные сведения см. в статье [Customize your Swagger definition for PowerApps](https://powerapps.microsoft.com/tutorials/customapi-how-to-swagger/) (Настройка определения Swagger для PowerApps).

3. Щелкните `save` для сохранения изменений. ![Добавление определения шаблона](./media/functions-api-definition-getting-started/addingtemplate.png)

## <a name="use-definition"></a>Использование определения API
1. Скопируйте адрес `API definition URL` и вставьте его в новой вкладке браузера, чтобы просмотреть необработанный документ OpenAPI.
1. Документ OpenAPI можно импортировать в различные инструменты для тестирования и интеграции с использованием этого URL-адреса.
  1. Многие ресурсы Azure могут автоматически импортировать ваш документ OpenAPI, используя URL-адрес определения API, сохраненный в параметрах приложения-функции. В случае источника определения API `Function` этот URL-адрес обновляется автоматически.


## <a name="test-definition"></a>Использование пользовательского интерфейса Swagger для тестирования определения API
В представление пользовательского интерфейса редактора внедренных определений API встроены инструменты тестирования. Добавьте ключ API, а затем нажмите кнопку `Try this operation` под каждым методом. Инструмент будет использовать определение API для форматирования запросов, а успешное получение ответов будет указывать на правильность определения.

### <a name="steps"></a>Шаги:

1. Скопируйте ключ API функции.
  1. Ключ API можно найти в функции "Триггер HTTP" в разделе `function name` > `Keys` > `Function Keys`
  ![Функциональная клавиша](./media/functions-api-definition-getting-started/functionkey.png).
1. Перейдите на страницу `API Definition (preview)`.
  1. Щелкните `Authenticate` и добавьте ключ API функции в расположенный сверху объект безопасности.
  ![Ключ OpenAPI](./media/functions-api-definition-getting-started/definitionTest auth.png)
1. Выберите `/api/yourfunctionroute` > `POST`.
1. Щелкните `Try it out` и введите имя теста.
1. В разделе `Pretty`
![API Definition test](./media/functions-api-definition-getting-started/definitionTest.png) (Тест определения API) должен отобразиться результат "УСПЕШНО".

## <a name="next-steps"></a>Дальнейшие действия
* [Обзор определения OpenAPI в Функциях](functions-api-definition.md)
  * Дополнительные сведения о поддержке OpenAPI см. в полной документации.
* [Справочник разработчика по функциям Azure](functions-reference.md)  
  * Справочник программиста по созданию функций, а также определению триггеров и привязок.
* [Репозиторий GitHub для Функций Azure](https://github.com/Azure/Azure-Functions/)
  * Ознакомьтесь со сведениями о Функциях в Github и оставьте свои отзывы о предварительной версии средства поддержки определений API. Добавьте на GitHub сведения обо всех проблемах, которые необходимо устранить.

