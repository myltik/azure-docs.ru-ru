---
title: "Метаданные OpenAPI в Функциях Azure | Документация Майкрософт"
description: "Обзор поддержки OpenAPI в Функциях Azure"
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
ms.translationtype: Human Translation
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: aac362f5123038cc39e0d2b32c10f7747a702cfe
ms.contentlocale: ru-ru
ms.lasthandoff: 05/16/2017


---
# <a name="openapi-20-metadata-support-in-azure-functions-preview"></a>Поддержка метаданных OpenAPI 2.0 в Функциях Azure (предварительная версия)
С помощью этой предварительной версии функции можно записывать определения OpenAPI 2.0 (прежнее название — Swagger) в приложении-функции и размещать соответствующий файл, используя это приложение-функцию.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

### <a name="what-is-openapi-metadata"></a>Общие сведения об OpenAPI
[Метаданные OpenAPI](http://swagger.io/) позволяют использовать функцию размещения REST API во всевозможном программном обеспечении: от решений, предлагаемых непосредственно Майкрософт, например PowerApps и [приложений API](https://docs.microsoft.com/azure/app-service-api/app-service-api-dotnet-get-started#a-idcodegena-generate-client-code-for-the-data-tier), до сторонних инструментов для разработчиков, например [Postman](https://www.getpostman.com/docs/importing_swagger) и [многих других пакетов](http://swagger.io/tools/).

>[!TIP]
>Мы рекомендуем начать с [учебника по началу работы](./functions-api-definition-getting-started.md), а затем вернуться к этому документу, чтобы узнать больше о конкретных функциях.

## <a name="enable"></a>Включение поддержки определения OpenAPI
* Все параметры OpenAPI можно настроить на странице `API Definition (preview)` в параметрах приложения-функции.
* Задайте для `API definition source` значение `Function`, чтобы включить поддержку размещенных определений интерфейса OpenAPI и создание кратких определений.
  * Используя `External URL`, ваша функция сможет применять определение OpenAPI, размещенное в другом месте.

## <a name="generate-definition"></a>Создание схемы Swagger на основе метаданных функции
Первое определение OpenAPI удобнее всего создавать на основе шаблона. Функция шаблона определения создает разреженное определение OpenAPI, используя все метаданные в function.json для каждой функции "Триггер HTTP". **Вам необходимо будет указать дополнительные сведения об API из [спецификации OpenAPI](http://swagger.io/specification/), например шаблоны запросов и ответов.**

[Пошаговые инструкции см. в этом учебнике по началу работы](./functions-api-definition-getting-started.md).

### <a name="templates"></a>Доступные шаблоны

|Имя| Описание |
|:-----|:-----|
|Созданное определение|Определение OpenAPI с максимальным объемом информации, которую можно извлечь из имеющихся метаданных функции|

### <a name="quickstart-details"></a>Метаданные, включенные в созданное определение

В следующей таблице показано соответствие параметров портала и данных в function.json согласно созданной схеме Swagger.

|Swagger.json|Пользовательский интерфейс портала|Function.json|
|:----|:-----|:-----|
|[Host](http://swagger.io/specification/#fixed-fields-15)|`Function app settings` > `Go to App Service Settings` > `Overview` > `URL`|*отсутствует*
|[Paths](http://swagger.io/specification/#paths-object-29)|`Integrate` > `Selected HTTP methods`|Bindings: Route
|[Path Item](http://swagger.io/specification/#path-item-object-32)|`Integrate` > `Route template`|Bindings: Methods
|[Безопасность](http://swagger.io/specification/#security-scheme-object-112)|ключей|*отсутствует*|
|operationID*|Маршрут + допустимые команды|Маршрут + допустимые команды|

\*Идентификатор операции необходим только для интеграции с PowerApps и Flow.
> [!NOTE]
>  x-ms-summary содержит имя, отображаемое в Logic Apps, Flow и PowerApps.
>
> Дополнительные сведения см. в статье [Customize your Swagger definition for PowerApps](https://powerapps.microsoft.com/tutorials/customapi-how-to-swagger/) (Настройка определения Swagger для PowerApps).

## <a name="CICD"></a>Создание определения API с помощью процесса непрерывной интеграции и доставки

 Прежде чем включать систему управления версиями для изменения из нее определения API, необходимо включить размещение определения API на портале. Выполните приведенные далее инструкции.

1. Перейдите в параметрах приложения-функции к `API Definition (preview)`.
  1. Задайте для `API definition source` значение `Function`.
  1. Щелкните `Generate API definition template`, а затем `Save` для создания шаблона определения, который можно будет изменить позже.
  1. Запишите значения параметров `API definition URL` и `key`.
1. [Настройка процесса непрерывной интеграции и доставки](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment#continuous-deployment-requirements)
2. Измените свой файл `swagger.json` в системе управления версиями по адресу `\site\wwwroot\.azurefunctions\swagger\swagger.json`.

Теперь изменения, внесенные в `swagger.json` в репозитории, размещаются приложением-функцией по адресу `API definition URL` и с ключом `key`, которые были записаны на шаге 1.3.

## <a name="next-steps"></a>Дальнейшие действия
* [Учебник по началу работы](functions-api-definition-getting-started.md)
  * Попробуйте воспользоваться нашим пошаговым руководством, чтобы увидеть определения OpenAPI в действии!
* [Репозиторий GitHub для Функций Azure](https://github.com/Azure/Azure-Functions/)
  * Ознакомьтесь со сведениями о Функциях в Github и оставьте свои отзывы о предварительной версии средства поддержки определений API. Добавьте на GitHub сведения обо всех проблемах, которые необходимо устранить.
* [Справочник разработчика по функциям Azure](functions-reference.md)  
  * Справочник программиста по созданию функций, а также определению триггеров и привязок.

