---
title: Возможности получения поддержки и справки для разработчиков служб удостоверений Azure | Документация Майкрософт
description: Узнайте, как получить справку и поддержку по вопросам и проблемам разработки, возникающим при создании приложения, которое интегрируется со службами удостоверений Microsoft Azure (Azure Active Directory и MSA)
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/27/2017
ms.author: celested
ms.reviewer: andret
ms.custom: aaddev
ms.openlocfilehash: ae682830a8d0e2c5c27211a3396d04c881c41585
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/14/2018
ms.locfileid: "34155486"
---
# <a name="support-and-help-options-for-developers"></a>Возможности получения поддержки и справки для разработчиков 

Независимо от задачи (начало интеграции с Azure Active Directory, службами удостоверений Майкрософт или API Microsoft Graph либо реализация в приложении новой функции) бывают случаи, когда разработчику требуется помощь сообщества или техническая поддержка. Эта статья поможет вам составить представление о том, как можно получить такую поддержку:

> [!div class="checklist"]
> * Прежде всего нужно проверить, есть ли ответ на ваш вопрос в сообществе или документация по функции, которую вы намерены реализовать.
> * В некоторых случаях для решения конкретной проблемы вам могут потребоваться наши средства технической поддержки.
> * Если вам не удается найти нужный ответ, можно задать вопрос на сайте *Stack Overflow*.
> * Если возникла проблема с одной из наших библиотек аутентификации, сообщите об этом на сайте *GitHub*.
> * И, наконец, если вам требуется консультация, подайте запрос в службу поддержки.


## <a name="search"></a>поиска

Если у вас есть вопрос по разработке, можно найти ответ в нашей документации, [примерах на сайте GitHub](https://github.com/azure-samples) или в ответах на сайте [Stack Overflow](https://www.stackoverflow.com).

### <a name="scoped-search"></a>Поиск в заданных областях
Чтобы ускорить получение результатов, задайте области поиска, указав Stack Overflow, нашу документацию и примеры кода в [любой поисковой системе](https://bing.com) таким образом:
```
{Your Search Terms} (site:stackoverflow.com OR site:docs.microsoft.com OR site:github.com/azure-samples OR site:cloudidentity.com OR site:developer.microsoft.com/en-us/graph)
```
*{Условия поиска}* — это ключевые слова для поиска.
<br/>

## <a name="use-our-development-support-tools"></a>Использование средств технической поддержки для разработки

|Средство  |ОПИСАНИЕ  |
|---------|---------|
|[jwt.ms](https://jwt.ms)| Вставьте идентификатор или маркеры доступа, чтобы декодировать имена и значения утверждений |
|[Анализатор кода ошибки](https://apps.dev.microsoft.com/portal/tools/errors)| Вставьте код ошибки, полученный во время входа или на страницах авторизации, чтобы определить возможные причины возникновения проблемы и пути ее решения |
|[Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)| Средство для выполнения запросов и получения ответов по API Microsoft Graph.|

<br/>

[![Stack Overflow](media/active-directory-develop-help-support/stackoverflow-logo.png)](https://www.stackoverflow.com)
## <a name="post-a-question-to-stack-overflow"></a>Публикация вопроса на сайте Stack Overflow

Stack Overflow — это предпочтительный канал для обработки вопросов по разработке. Здесь члены сообщества и команды Майкрософт напрямую помогут вам решить проблему.

Если не удается решить проблему при помощи функции поиска, отправьте вопрос в Stack Overflow. При этом используйте один из указанных ниже тегов. Он поможет сообществу своевременно определить ваш вопрос и ответить на него.

|Компонент или область  |Теги  |
|---------|---------|
|Библиотека ADAL |[[adal]](http://stackoverflow.com/questions/tagged/adal)|
|Библиотека MSAL     |[[msal]](http://stackoverflow.com/questions/tagged/msal)|
|ПО промежуточного слоя OWIN  |[[azure-active-directory]](http://stackoverflow.com/questions/tagged/azure-active-directory)|
|[Azure B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)  |[[azure-ad-b2b]](http://stackoverflow.com/questions/tagged/azure-ad-b2b)|
|[Azure B2C](https://azure.microsoft.com/services/active-directory-b2c/)  |[[azure-ad-b2c]](http://stackoverflow.com/questions/tagged/azure-ad-b2b)|
|[API Microsoft Graph](https://developer.microsoft.com/graph/) |[[microsoft-graph]](http://stackoverflow.com/questions/tagged/microsoft-graph)
|Любая другая область, которая относится к аутентификации или авторизации |[[azure-active-directory]](http://stackoverflow.com/questions/tagged/azure-active-directory)
<br/>
> [!TIP]
> Указанные ниже публикации на Stack Overflow содержат советы по постановке вопросов и добавлению исходного кода. Следуя этим рекомендациям, вы повысите вероятность того, что участники сообщества оперативно оценят ваш вопрос и ответят на него.  
> - [How do I ask a good question](https://stackoverflow.com/help/how-to-ask) (Как правильно задать вопрос).
> - [How to create a Minimal, Complete, and Verifiable example](https://stackoverflow.com/help/mcve) (Как создать минимальный, полный и доступный для проверки пример).

<br/>


[![Stack Overflow](media/active-directory-develop-help-support/github-logo.png)](https://www.github.com)
## <a name="create-a-github-issue"></a>Сообщение о проблеме на GitHub

 Если возникла ошибка или проблема, связанная с нашими библиотеками, сообщите о ней на сайте GitHub. В наших библиотеках используется открытый код. Поэтому вы также можете отправить запрос на вытягивание. Список библиотек и соответствующих репозиториев GitHub можно найти в следующей статье:

- Библиотеки и репозитории GitHub [ADAL, MSAL и ПО промежуточного слоя OWIN](active-directory-authentication-libraries.md).

<br/>

## <a name="open-a-support-request"></a>Подача запроса в службу поддержки

Если вам требуется консультация, подайте запрос в службу поддержки. Для клиентов Azure предусмотрено несколько вариантов поддержки. Сравнить планы можно на [этой странице](https://azure.microsoft.com/support/plans/). Клиенты Azure также могут получить поддержку уровня Developer. Сведения о приобретении планов для поддержки уровня Developer см. на [этой странице](https://azure.microsoft.com/support/plans/developer/).

- Если у вас уже есть план поддержки Azure, [подайте запрос в службу поддержки здесь](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

- Если вы не являетесь клиентом Azure, можно подать запрос в корпорацию Майкрософт на странице [коммерческой поддержки](https://support.microsoft.com/en-us/gp/contactus81?Audience=Commercial).

Также можно получить поддержку или задать вопрос при помощи [нашего виртуального агента](https://support.microsoft.com/contactus/?ws=support).

### <a name="free-chat-support-for-a-limited-time"></a>Бесплатная техническая поддержка в формате чата

Кроме того, вы можете воспользоваться технической поддержкой в формате чата. Она предоставляется бесплатно для партнеров корпорации Майкрософт в течение ограниченного времени. Если ваша компания не является партнером корпорации Майкрософт, можно бесплатно зарегистрировать ее для использования других возможностей [здесь](https://partners.microsoft.com/PartnerProgram/simplifiedenrollment.aspx).

После регистрации компании подайте запрос в чат [здесь](https://aka.ms/devchat).