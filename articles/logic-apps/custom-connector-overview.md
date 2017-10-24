---
title: "Обзор пользовательских соединителей для Azure Logic Apps | Документация Майкрософт"
description: "Общие сведения о создании пользовательских соединителей для поддержки и расширения сценариев интеграции"
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
ms.openlocfilehash: 0515b21bc7c7cc737e14fda016606bbea1aab476
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="custom-connectors-overview"></a>Обзор пользовательских соединителей

Рабочие процессы и приложения можно создавать с помощью [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/), [Microsoft Flow](https://flow.microsoft.com) или [Microsoft PowerApps](https://powerapps.microsoft.com) без написания какого-либо кода. Чтобы помочь вам интегрировать свои данные и бизнес-процессы, эти службы предлагают [более 100 соединителей](../connectors/apis-list.md). Они подходят не только для служб и продуктов Майкрософт, таких как Azure и SQL Server, но и для других служб, таких как GitHub, Salesforce, Twitter и т. д. 

Тем не менее, иногда требуется вызвать API, службы или системы, которые не предоставляются в качестве готовых соединителей. Для поддержки более специализированных сценариев, адаптированных под бизнес-потребности пользователей и их потребности в производительности, можно создать *пользовательские соединители*, которые будут содержать собственные триггеры и действия.
Пользовательские соединители расширяют возможности интеграции, охвата, обнаруживаемости и приносят больше пользы для службы или продукта, помогая клиентам лучше и быстрее принимать новые продуктов.

Например, на этой диаграмме показано взаимодействие между веб-API, пользовательским соединителем Logic Apps, созданных для этого API, и приложением логики, которое работает с этим API через пользовательский соединитель:

![Концептуальный обзор веб-API, пользовательского соединителя и приложения логики](./media/custom-connector-overview/custom-connector-conceptual.png)

В этом обзоре показана структура общих задач высокого уровня для создания, защиты, регистрации и использования, а также (дополнительно) предоставления общего доступа к соединителям или их сертификации:

![Шаги по разработке пользовательских соединителей](./media/custom-connector-overview/authoring-steps.png)

## <a name="prerequisites"></a>Предварительные требования

Чтобы создать соединитель от начала до конца, требуются следующие элементы:

* Подписка Azure. Если у вас нет подписки, вы можете для начала использовать [бесплатную учетную запись Azure](https://azure.microsoft.com/free/). Или зарегистрируйтесь для получения [подписки с оплатой по мере использования](https://azure.microsoft.com/pricing/purchase-options/).

* Интерфейс RESTful API с определенным типом аутентифицируемого доступа. Дополнительные сведения см. в разделе [Создание соединителей с помощью интерфейсов веб-API](../logic-apps/custom-connector-build-web-api-app-tutorial.md). Шаблоны, которые можно использовать для создания триггеров и действий своего соединителя, см. в разделе [Создание пользовательских API в качестве соединителей для приложений логики](../logic-apps/logic-apps-create-api-app.md).

* Любой из этих элементов:

  * [файл OpenAPI 2.0](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/2.0.md), который раньше назывался Swagger;
  * URL-адрес определения OpenAPI;
  * [коллекция Postman](../logic-apps/custom-connector-api-postman-collection.md) для вашего API. 

  При отсутствии всех этих элементов мы предоставим свои рекомендации.

* Необязательно: изображение, которое будет использоваться в качестве значка для вашего пользовательского соединителя.

## <a name="1-build-your-restful-api"></a>1. Создание собственного интерфейса RESTful API

С технической точки зрения соединитель является программой-оболочкой вокруг REST API, которая основана на спецификации OpenAPI (прежнее название — Swagger) и позволяет вашей базовой службе обращаться к Logic Apps, Flow или PowerApps. Поэтому прежде чем создавать пользовательский соединитель, необходимо создать полнофункциональный API. 

Для API можно использовать любой язык и любую платформу. Для технологий Майкрософт мы рекомендуем использовать одну из этих платформ:

* [Функции Azure](https://azure.microsoft.com/services/functions/)
* [Веб-приложения Azure](https://azure.microsoft.com/services/app-service/web/)
* [Приложения API Azure](https://azure.microsoft.com/services/app-service/api/)

Например, в этом руководстве показано, [как создать пользовательский соединитель с помощью интерфейса веб-API](../logic-apps/custom-connector-build-web-api-app-tutorial.md). Шаблоны, которые можно использовать для создания триггеров и действий своего соединителя, см. в разделе [Создание пользовательских API в качестве соединителей для приложений логики](../logic-apps/logic-apps-create-api-app.md).

## <a name="2-secure-your-api"></a>2. Защита API

Для интерфейсов API и соединителей можно использовать следующие стандарты аутентификации:

   * [OAuth 2.0](https://oauth.net/2/), включая [Azure Active Directory](https://azure.microsoft.com/develop/identity/), или отдельные службы, например Dropbox, GitHub и SalesForce.
   * Универсальная OAuth 2.0
   * [Обычная аутентификация](https://swagger.io/docs/specification/authentication/basic-authentication/)
   * [Ключ API](https://swagger.io/docs/specification/authentication/api-keys/).

Вы можете настроить аутентификацию Azure Active Directory (Azure AD) для своего API на портале Azure, чтобы не реализовывать аутентификацию посредством кода. Вы можете также применить обязательную проверку подлинности с помощью кода API. 

Дополнительные сведения см. в соответствующих руководствах:

* [Повышение уровня безопасности API и соединителя с помощью Azure Active Directory (Azure AD)](../logic-apps/custom-connector-azure-active-directory-authentication.md)
* [Использование Azure Active Directory с настраиваемым соединителем](https://ms.flow.microsoft.com/documentation/customapi-azure-resource-manager-tutorial/)
* [Использование Azure Active Directory с настраиваемым соединителем в PowerApps](https://powerapps.microsoft.com/tutorials/customapi-azure-resource-manager-tutorial/)

## <a name="3-describe-your-api"></a>3. Описание API 

Если ваш API имеет определенный тип аутентифицируемого доступа, то вам необходимо описать интерфейс и операции API, чтобы Logic Apps, Flow или PowerApps могли взаимодействовать с вашим API.
Используйте одно из этих стандартных определений:

* [Файл OpenAPI 2.0](https://swagger.io/). Можно взять за основу существующий файл OpenAPI.

  Если вы еще не знакомы с OpenAPI, то посетите страницу [Getting started with Swagger](http://swagger.io/getting-started/) (Приступая к работе со Swagger) на сайте swagger.io.
  Пример файла OpenAPI см. в [документации по API текстовой аналитики](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/export?DocumentFormat=Swagger&ApiName=Azure). 

* Коллекция Postman, которая создает файл OpenAPI автоматически. Если у вас еще нет файла OpenAPI и вы не хотите его создавать, то можно просто создать пользовательский соединитель с помощью коллекции Postman.

  Если вы еще не знакомы с Postman, то [установите приложение Postman](https://www.getpostman.com/apps) с соответствующего сайта. Дополнительные сведения см. в разделе [Describe custom APIs and custom connectors with Postman](../logic-apps/custom-connector-api-postman-collection.md) (Описание пользовательских API и соединителей с помощью Postman).

> [!IMPORTANT]
> Размер файла не должен превышать 1 МБ.

В конечном счете Logic Apps, Flow и PowerApps используют OpenAPI, анализируют коллекцию Postman и преобразуют ее в файл определения OpenAPI. Несмотря на то, что OpenAPI 2.0 и коллекция Postman используют разные форматы, оба они не зависят от языка и являются машиночитаемыми документами, описывающими операции и параметры API. Эти документы можно создать с помощью различных инструментов, основываясь на языке и платформе, которые используются API. Файл OpenAPI также можно создать при регистрации соединителя.

Например, можно создать файл OpenAPI или коллекцию Postman из любой конечной точки REST API, включая:

* общедоступные соединители, например [Spotify](https://developer.spotify.com/), [Slack](https://api.slack.com/), [Rackspace](http://docs.rackspace.com/) и др.;

* API, который вы создали и развернули в любой поставщик услуг размещения в облаке, такой как Azure, Heroku, Google Cloud и др.;

* пользовательский бизнес-API, который развернут в сети, но только в том случае, если этот API предоставляет общий доступ через Интернет.

## <a name="4-register-your-connector"></a>4. Регистрация соединителя

Процесс регистрации помогает Logic Apps, Flow и PowerApps понять характеристики API, включая описание, обязательную аутентификацию и операции, а также параметры и выходные данные для каждой операции. При запуске процесса регистрации можно предоставить файл OpenAPI или коллекцию Postman, которая автоматически заполняет поля метаданных в мастере регистрации. Значения этих полей можно изменить в любой момент.

![Описание API](./media/custom-connector-overview/choose-api-definition-file.png)

Сведения о регистрации соединителя см. в соответствующем руководстве:

* [Register custom connectors in Azure Logic Apps](../logic-apps/logic-apps-custom-connector-register.md) (Регистрация пользовательских соединителей в Azure Logic Apps)
* [Регистрация настраиваемого соединителя](https://ms.flow.microsoft.com/documentation/register-custom-api/#register-your-custom-connector)
* [Регистрация настраиваемого соединителя](https://powerapps.microsoft.com/tutorials/register-custom-api/#register-your-custom-connector)

## <a name="5-use-your-connector-in-a-logic-app-flow-or-app"></a>5. Использование соединителя в приложении логики, последовательности или приложении 

Соединитель можно использовать таким же образом, как используются соединители, управляемые корпорацией Майкрософт. Например, в рабочем процессе приложения логики добавьте пользовательский соединитель, чтобы можно было создать подключение к API, и вызовите любые операции, предоставляемые API. Это делается так же, как вызов операций соединителей, управляемых корпорацией Майкрософт.

## <a name="6-share-your-connector"></a>6. Предоставление общего доступа к соединителю 

Вы можете предоставить пользователям в своей организации общий доступ к соединителю. Это делается таким же образом, как предоставление общего доступа к ресурсам в Logic Apps, Flow или PowerApps. Предоставление общего доступа не является обязательным, но в некоторых сценариях может потребоваться использовать соединители совместно с другими пользователями.

Зарегистрированные, но несертифицированные пользовательские соединители работают как соединители, управляемые корпорацией Майкрософт. При этом они отображаются и доступны *только* своим создателям и пользователям с тем же клиентом Azure Active Directory и той же подпиской Azure для приложений логики в регионе, где развернуты эти приложения. Далее рассматривается предоставление общего доступа к соединителю для внешних пользователей, находящихся за этими пределами. Например, для всех пользователей Logic Apps, Flow и PowerApps.

> [!IMPORTANT]
> Если предоставить общий доступ к соединителю, то у других пользователей может возникнуть зависимость от этого соединителя. 
> ***При удалении соединителя удаляются все подключения к этому соединителю.***

* [Register custom connectors in Azure Logic Apps](../logic-apps/logic-apps-custom-connector-register.md) (Регистрация пользовательских соединителей в Azure Logic Apps)
* [Flow: предоставление доступа к настраиваемому соединителю](https://ms.flow.microsoft.com/documentation/register-custom-api/#share-your-custom-connector)
* [PowerApps: совместное использование настраиваемого соединителя](https://powerapps.microsoft.com/tutorials/register-custom-api/#share-your-custom-connector)

## <a name="7-certify-your-connector"></a>7. Сертификация соединителя

Если требуется предоставить доступ к соединителю всем пользователям в Logic Apps, Flow и PowerApps, отправьте этот соединитель на сертификацию Майкрософт. Этот процесс включает в себя проверку соединителя на соответствие техническим требованиям и требованиям к содержимому. Также проверяется его работоспособность в сочетании с Logic Apps, Flow и PowerApps, после чего корпорация Майкрософт может опубликовать ваш соединитель. Узнайте, [как отправить свой соединитель на сертификацию Майкрософт](../logic-apps/custom-connector-submit-certification.md).

## <a name="get-support"></a>Получение поддержки

* Если вам нужна поддержка при подключении и разработке, то отправьте сообщение по адресу [condevhelp@microsoft.com](mailto:condevhelp@microsoft.com). Корпорация Майкрософт активно отслеживает эту учетную запись на наличие вопросов от разработчиков и направляет их соответствующей группе поддержки. 

* Часто задаваемые вопросы см. в разделе [Часто задаваемые вопросы о настраиваемых соединителях](../logic-apps/custom-connector-faq.md).

## <a name="next-steps"></a>Дальнейшие действия

* [Создание соединителей с помощью интерфейсов веб-API](../logic-apps/custom-connector-build-web-api-app-tutorial.md)
* [Повышение уровня безопасности API и соединителя с помощью Azure Active Directory (Azure AD)](../logic-apps/custom-connector-azure-active-directory-authentication.md)
* [Describe custom APIs and custom connectors with Postman](../logic-apps/custom-connector-api-postman-collection.md) (Описание пользовательских API и соединителей с помощью Postman)
* [Submit custom connectors for Microsoft certification](../logic-apps/custom-connector-submit-certification.md) (Отправка пользовательских соединителей на сертификацию Майкрософт)
