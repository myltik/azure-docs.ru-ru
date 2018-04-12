---
title: Метаданные OpenAPI в Функциях Azure | Документация Майкрософт
description: Обзор поддержки OpenAPI в Функциях Azure
services: functions
documentationcenter: ''
author: alexkarcher-msft
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 03/23/2017
ms.author: alkarche
ms.openlocfilehash: b6aacc536e589a2036aba5a0784a4ba71641a59e
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/05/2018
---
# <a name="openapi-20-metadata-support-in-azure-functions-preview"></a>Поддержка метаданных OpenAPI 2.0 в Функциях Azure (предварительная версия)
Поддержка метаданных OpenAPI 2.0 (прежнее название — Swagger) в Функциях Azure — это предварительная версия функции, которая предназначена для записи определения OpenAPI 2.0 в приложении-функции. Затем можно разместить этот файл с помощью приложения-функции.

[Метаданные OpenAPI](http://swagger.io/) позволяют использовать функцию, на которой размещен REST API во всевозможном программном обеспечении. Это программное обеспечение включает предложения Майкрософт, такие как PowerApps и [функцию "Приложения API" службы приложений Azure](../app-service/app-service-web-overview.md), средства сторонних разработчиков, например [Postman](https://www.getpostman.com/docs/importing_swagger), и [многие дополнительные пакеты](http://swagger.io/tools/).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

>[!TIP]
>Мы рекомендуем начать с [учебника по началу работы](./functions-api-definition-getting-started.md), а затем вернуться к этому документу, чтобы узнать больше о конкретных функциях.

## <a name="enable"></a>Включение поддержки определения OpenAPI
Все параметры OpenAPI можно настроить на странице **Определение API** в приложении-функции **Функции платформы**.

Чтобы включить создание размещенного определения OpenAPI и кратких определений, задайте для параметра **Источник определения API** значение **Функция (предварительная версия)**. **Внешний URL-адрес** позволяет функции применять определение OpenAPI, размещенное в другом месте.

## <a name="generate-definition"></a>Создание схемы Swagger на основе метаданных функции
Шаблон поможет вам приступить к написанию первого определения OpenAPI. Функция шаблона определения создает разреженное определение OpenAPI, используя все метаданные в файле function.json для каждой функции "Триггер HTTP". Вам необходимо будет указать дополнительные сведения об API из [спецификации OpenAPI](http://swagger.io/specification/), например шаблоны запросов и ответов.

Пошаговые инструкции см. в статье [Создание метаданных OpenAPI 2.0 (Swagger) для приложения-функции (предварительная версия)](./functions-api-definition-getting-started.md).

### <a name="templates"></a>Доступные шаблоны

|ИМЯ| ОПИСАНИЕ |
|:-----|:-----|
|Созданное определение|Определение OpenAPI с максимальным объемом информации, которую можно извлечь из имеющихся метаданных функции.|

### <a name="quickstart-details"></a>Метаданные, включенные в созданное определение

В следующей таблице показано соответствие параметров портала Azure и данных в function.json согласно созданной схеме Swagger.

|Swagger.json|Пользовательский интерфейс портала|Function.json|
|:----|:-----|:-----|
|[Host](http://swagger.io/specification/#fixed-fields-15)|**Параметры приложения-функции** > **Параметры службы приложений** > **Обзор** > **URL-адрес**|*Отсутствует*
|[Paths](http://swagger.io/specification/#paths-object-29)|**Интегрировать** > **Выбранные методы HTTP**|Bindings: Route
|[Path Item](http://swagger.io/specification/#path-item-object-32)|**Интегрировать** > **Шаблон маршрута**|Bindings: Methods
|[Безопасность](http://swagger.io/specification/#security-scheme-object-112)|**Ключи**|*Отсутствует*|
|operationID*|**Маршрут + допустимые команды**|Маршрут + допустимые команды|

\*Идентификатор операции необходим только для интеграции с PowerApps и Flow.
> [!NOTE]
> Расширение x-ms-summary предоставляет отображаемое имя в Logic Apps, PowerApps и Flow.
>
> Дополнительные сведения см. в статье [Customize your Swagger definition for PowerApps](https://powerapps.microsoft.com/tutorials/customapi-how-to-swagger/) (Настройка определения Swagger для PowerApps).

## <a name="CICD"></a>Создание определения API с помощью процесса непрерывной интеграции и доставки

 Прежде чем включать систему управления версиями для изменения из нее определения API, необходимо включить размещение определения API на портале. Выполните следующие действия:

1. Перейдите к **API Definition (preview)** (Определение API (предварительная версия)) в параметрах приложения-функции.
  1. Задайте для параметра **API definition source** (Источник определения API) значение **Функция**.
  1. Щелкните **Generate API definition template** (Создать шаблон определения API), а затем **Сохранить**, чтобы создать определение шаблона, которое можно будет изменить позже.
  1. Обратите внимание на URL-адрес и ключ определения API.
1. [Настройте непрерывную интеграцию и развертывание](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment#continuous-deployment-requirements).
2. Измените файл swagger.json в системе управления исходным кодом по адресу \site\wwwroot\.azurefunctions\swagger\swagger.json.

Теперь изменения, внесенные в файл swagger.json в репозитории, размещаются приложением-функцией с использованием URL-адреса и ключа определения API, которые указаны на шаге 1.в.

## <a name="next-steps"></a>Дополнительная информация
* [Создание метаданных OpenAPI 2.0 (Swagger) для приложения-функции (предварительная версия)](functions-api-definition-getting-started.md). Попробуйте воспользоваться нашим пошаговым руководством, чтобы увидеть определения OpenAPI в действии.
* [Репозиторий GitHub для Функций Azure](https://github.com/Azure/Azure-Functions/). Ознакомьтесь со сведениями о Функциях в репозитории и оставьте свои отзывы о предварительной версии средства поддержки определений API. Добавьте на GitHub сведения обо всех проблемах, которые необходимо устранить.
* [Руководство для разработчиков по Функциям Azure](functions-reference.md). Сведения о написании кода функций и определении триггеров и привязок.
