---
title: "Аутентификация с помощью интерфейсов REST API Служб мобильного взаимодействия"
description: "Описывается аутентификация с помощью интерфейсов REST API Служб мобильного взаимодействия Azure."
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: da82cb36-957a-4e19-a805-b44733cf6597
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 10/05/2016
ms.author: wesmc;ricksal
ms.openlocfilehash: 66bcd738b86f846eae3499b289a6629323009a44
ms.sourcegitcommit: d6984ef8cc057423ff81efb4645af9d0b902f843
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/05/2018
---
# <a name="authenticate-with-mobile-engagement-rest-apis"></a>Аутентификация с помощью интерфейсов REST API Служб мобильного взаимодействия

## <a name="overview"></a>Обзор

Этот документ описывает, как получить маркер для проверки подлинности с помощью API REST для Mobile Engagement допустимым AD Oauth Azure.

Предполагается, что у вас есть действительная подписка Azure, и вы создали приложение Mobile Engagement, с помощью одного из [учебники разработчика](mobile-engagement-windows-store-dotnet-get-started.md).

## <a name="authentication"></a>Authentication

Для проверки подлинности необходимо использовать маркер OAuth на основе Microsoft Azure Active Directory. 

Чтобы запрос проверки подлинности API-Интерфейс необходимо добавить заголовок авторизации для каждого запроса, который имеет следующий вид:

    Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGmJlNmV2ZWJPamg2TTNXR1E...

> [!NOTE]
> Токены Azure Active Directory срок действия истекает в течение часа.
> 
> 

Существует несколько способов получить токен. Поскольку API-интерфейсы вызываются из облачной службы, необходимо использовать ключ API. Ключ API в Azure — это пароль субъекта-службы. Следующая процедура описывает один из способов его ручной настройки.

### <a name="one-time-setup-using-script"></a>Однократная настройка (с использованием сценария)

Следуйте инструкциям ниже, чтобы выполнить установку, с помощью сценария PowerShell, который требуется минимальное время для программы установки, но использует наиболее допустимые значения по умолчанию. При необходимости также можно выполнить указания по [установке вручную](mobile-engagement-api-authentication-manual.md) непосредственно на портале Azure и осуществить более точную настройку.

1. Получите последнюю версию Azure PowerShell [здесь](http://aka.ms/webpi-azps). Дополнительные сведения об инструкциях по скачиванию см. по этой [ссылке](/powershell/azure/overview).
2. После установки Azure PowerShell используйте следующие команды, чтобы проверить наличие установленного **модуля Azure** :

    a. Убедитесь, что модуль Azure PowerShell находится в списке доступных модулей.

        Get-Module –ListAvailable

    ![Доступные модули Azure][1]

    Б. Если не удается найти модуль Azure PowerShell в списке выше, необходимо запустить:

        Import-Module Azure
3. Вход в диспетчер ресурсов Azure из PowerShell, выполнив следующую команду и указав имя пользователя и пароль для учетной записи Azure: 

        Login-AzureRmAccount
4. Если у вас несколько подписок, затем следует запустить:

    a. Получите список всех подписок и скопируйте SubscriptionId нужной подписки. Убедитесь, что эта подписка использует тот же, имеющая Mobile Engagement приложения, что требуется взаимодействовать с помощью интерфейсов API. 

        Get-AzureRmSubscription

    Б. Выполните указанную далее команду и укажите SubscriptionId подписки, чтобы настроить использование подписки.

        Select-AzureRmSubscription –SubscriptionId <subscriptionId>
5. Скопируйте текст скрипта [New-AzureRmServicePrincipalOwner.ps1](https://raw.githubusercontent.com/matt-gibbs/azbits/master/src/New-AzureRmServicePrincipalOwner.ps1) на локальный компьютер, сохраните его как командлет PowerShell (например, `APIAuth.ps1`) и выполните его `.\APIAuth.ps1`.
6. Сценарий запросит ввести значение для **principalName**. Укажите подходящее имя, которое будет использоваться для создания приложения Active Directory (например APIAuth). 
7. По завершении сценария отобразятся четыре значения, которые нужны для программной проверки подлинности с использованием AD. Скопируйте их. 

    **TenantId**, **SubscriptionId**, **ApplicationId** и **Secret**.

    Вы будете использовать TenantId в качестве `{TENANT_ID}`, ApplicationId — в качестве `{CLIENT_ID}`, а секрет — в качестве `{CLIENT_SECRET}`.

   > [!NOTE]
   > Политика безопасности по умолчанию может блокировать выполнение сценариев PowerShell. В таком случае используйте следующую команду, чтобы настроить временную политику и обеспечить возможность выполнить сценарий:
   > 
   > Set-ExecutionPolicy RemoteSigned
8. Вот как выглядит набор командлетов PS.
    ![][3]
9. На портале Azure перейдите в Active Directory можно, щелкнув **регистрации приложения** и найдите приложение, чтобы убедиться, что существует![][4]

### <a name="steps-to-get-a-valid-token"></a>Получение допустимого маркера

1. Вызовите API со следующими параметрами и замените TENANT\_ID, CLIENT\_ID и CLIENT\_SECRET:
   
   * **URL-адрес запроса** как`https://login.microsoftonline.com/{TENANT_ID}/oauth2/token`
   * **Заголовок HTTP Content-Type** как`application/x-www-form-urlencoded`
   * **Текст запроса HTTP** как`grant_type=client\_credentials&client_id={CLIENT_ID}&client_secret={CLIENT_SECRET}&resource=https%3A%2F%2Fmanagement.core.windows.net%2F`
     
    Вот пример запроса:
    ```
    POST /{TENANT_ID}/oauth2/token HTTP/1.1
    Host: login.microsoftonline.com
    Content-Type: application/x-www-form-urlencoded
    grant_type=client_credentials&client_id={CLIENT_ID}&client_secret={CLIENT_SECRET}&reso
    urce=https%3A%2F%2Fmanagement.core.windows.net%2F
    ```
    Вот пример ответа на запрос:
    ```
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Content-Length: 1234

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1445395811","not_before":"144
    5391911","resource":"https://management.core.windows.net/","access_token":{ACCESS_TOKEN}}
    ```
     В этом примере предусмотрено кодирование параметров POST в URL-адреса, поэтому на самом деле значение `resource` — `https://management.core.windows.net/`. Следует также закодировать параметр `{CLIENT_SECRET}` в URL-адрес, так как в нем могут содержаться специальные знаки.

     > [!NOTE]
     > Для тестирования можно использовать средство клиента HTTP, такое как [Fiddler](http://www.telerik.com/fiddler) или [расширение Chrome Postman](https://chrome.google.com/webstore/detail/postman/fhbjgbiflinjbdggehcddcbncdddomop). 
     > 
     > 
2. Теперь при каждом вызове API добавляйте в запрос заголовок авторизации:
   
        Authorization: Bearer {ACCESS_TOKEN}
   
    Если вернется код состояния 401, проверьте текст ответа. Возможно, истек срок действия маркера. В этом случае следует получить новый маркер.

## <a name="using-the-apis"></a>Использование API
Теперь, когда есть допустимый маркер, можно вызывать API.

1. С каждым запросом API необходимо передавать допустимый действующий маркер, полученный в результате выполнения действий, которые описаны в предыдущем разделе.
2. В универсальном коде ресурса (URI) запроса нужно добавить несколько параметров для идентификации вашего приложения. URI будет выглядеть следующим образом:
   
        https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/
        providers/Microsoft.MobileEngagement/appcollections/{app-collection}/apps/{app-resource-name}/
   
    Чтобы получить параметры, щелкните имя приложения, а затем — "Панель мониторинга", и вы увидите страницу с такими тремя параметрами:
   
   * **1** `{subscription-id}`
   * **2** `{app-collection}`
   * **3** `{app-resource-name}`
   * **4** Ваша группа ресурсов будет носить имя **MobileEngagement** до тех пор, пока не будет создана новая группа. 

> [!NOTE]
> <br/>
> 
> 1. Игнорируйте параметр корневого адреса API, так как он предназначался для предыдущих API.<br/>
> 2. Если вы создали приложение с помощью портала Azure необходимо использовать имя ресурса приложения, которое отличается от само имя приложения. Если вы создали приложение на портале Azure следует использовать имя приложения, сам (нет никакой разницы между имени ресурса приложения и приложения для приложений, созданных на новом портале).  
> 
> 

<!-- Images -->
[1]: ./media/mobile-engagement-api-authentication/azure-module.png
[2]: ./media/mobile-engagement-api-authentication/mobile-engagement-api-uri-params.png
[3]: ./media/mobile-engagement-api-authentication/ps-cmdlets.png
[4]: ./media/mobile-engagement-api-authentication/search-app.png



