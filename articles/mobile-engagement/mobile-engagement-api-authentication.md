---
title: Аутентификация с помощью интерфейсов REST API Служб мобильного взаимодействия
description: Описывается аутентификация с помощью интерфейсов REST API Служб мобильного взаимодействия Azure.
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: da82cb36-957a-4e19-a805-b44733cf6597
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 10/05/2016
ms.author: wesmc;ricksal
ms.openlocfilehash: 52843c926248627e4e530612cfe9326d4302eb95
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/19/2018
---
# <a name="authenticate-with-mobile-engagement-rest-apis"></a>Аутентификация с помощью интерфейсов REST API Служб мобильного взаимодействия
> [!IMPORTANT]
> Срок действия Служб мобильного взаимодействия истекает 31.03.2018. Вскоре после этого страница будет удалена.
> 

## <a name="overview"></a>Обзор

В этом документе описывается, как получить допустимый маркер OAuth Azure Active Directory (Azure AD) для проверки подлинности с помощью интерфейсов REST API Служб мобильного взаимодействия.

Предполагается, что у вас есть действующая подписка Azure и вы создали приложение Служб мобильного взаимодействия, используя одно из наших [руководств для разработчиков](mobile-engagement-windows-store-dotnet-get-started.md).

## <a name="authentication"></a>Authentication

Для проверки подлинности необходимо использовать маркер OAuth на основе Microsoft Azure Active Directory. 

Чтобы выполнить проверку подлинности запроса API, в него нужно добавить заголовок авторизации, который имеет следующий вид:

    Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGmJlNmV2ZWJPamg2TTNXR1E...

> [!NOTE]
> Срок действия токенов Azure Active Directory истекает через 1 час.
> 
> 

Существует несколько способов получить токен. Так как вызовы API осуществляются из облачной службы, возможно, понадобится использовать ключ API. Ключ API в Azure — это пароль субъекта-службы. Следующая процедура описывает один из способов его ручной настройки.

### <a name="one-time-setup-using-a-script"></a>Однократная настройка (с использованием сценария)

Чтобы выполнить установку с помощью сценария PowerShell, следуйте приведенным ниже инструкциям. Для реализации этого сценария требуется минимальное время, но используются наиболее допустимые значения по умолчанию. 

При необходимости также можно выполнить указания по [установке вручную](mobile-engagement-api-authentication-manual.md) непосредственно на портале Azure. На портале Azure можно выполнить более точную настройку.

1. Получите последнюю версию Azure PowerShell, [загрузив ее](http://aka.ms/webpi-azps). Инструкции по загрузке см. в статье [Общие сведения об Azure PowerShell](/powershell/azure/overview).

2. После установки Azure PowerShell используйте следующие команды, чтобы проверить наличие установленного **модуля Azure**:

    a. Убедитесь, что модуль Azure PowerShell находится в списке доступных модулей.

        Get-Module –ListAvailable

    ![Доступные модули Azure][1]

    Б. Если модуль Azure PowerShell отсутствует в приведенном выше списке, выполните следующую команду:

        Import-Module Azure
3. Войдите в Azure Resource Manager из PowerShell, выполнив следующую команду. Введите имя пользователя и пароль для учетной записи Azure. 

        Connect-AzureRmAccount
4. Если у вас несколько подписок, сделайте следующее:

    a. Получите список всех подписок. Затем скопируйте **SubscriptionId** для подписки, которую необходимо использовать. Убедитесь, что в этой подписке имеется приложение Службы мобильного взаимодействия. Вы будете использовать его для взаимодействия с API-интерфейсами. 

        Get-AzureRmSubscription

    Б. Выполните следующую команду: Укажите **SubscriptionId**, чтобы настроить подписку, которую вы собираетесь использовать:

        Select-AzureRmSubscription –SubscriptionId <subscriptionId>
5. Скопируйте текст сценария [New-AzureRmServicePrincipalOwner.ps1](https://raw.githubusercontent.com/matt-gibbs/azbits/master/src/New-AzureRmServicePrincipalOwner.ps1) на локальный компьютер. Затем сохраните его как командлет PowerShell (например, `APIAuth.ps1`) и выполните его.

         `.\APIAuth.ps1`.

6. Сценарий запросит ввести значение для **principalName**. Укажите подходящее имя, которое будет использоваться для приложения Active Directory (например, APIAuth). 

7. По завершении выполнения сценария отобразятся следующие четыре значения. Обязательно скопируйте их, так как они нужны для программной проверки подлинности с использованием Active Directory: 

   - **TenantId**
   - **SubscriptionId**
   - **ApplicationId**
   - **Секрет**

   Используйте TenantId в качестве `{TENANT_ID}`, ApplicationId — в качестве `{CLIENT_ID}`, а секрет — в качестве `{CLIENT_SECRET}`.

   > [!NOTE]
   > Политика безопасности по умолчанию может блокировать выполнение сценариев PowerShell. В таком случае используйте следующую команду, чтобы настроить временную политику и обеспечить возможность выполнить сценарий:
   > 
   > Set-ExecutionPolicy RemoteSigned
8. В этом разделе представлен набор командлетов PowerShell.
    ![Командлеты PowerShell][3]
9. На портале Azure перейдите в Active Directory, выберите **Регистрация приложений**, а затем найдите приложение и убедитесь, что оно имеется.
    ![Поиск приложения][4]

### <a name="steps-to-get-a-valid-token"></a>Получение допустимого маркера

1. Вызовите программный интерфейс со следующими параметрами Обязательно замените значения **TENANT\_ID**, **CLIENT\_ID** и **CLIENT\_SECRET**:
   
   * **URL-адрес запроса** — `https://login.microsoftonline.com/{TENANT_ID}/oauth2/token`.

   * **Заголовок Content-Type HTTP-запроса** — `application/x-www-form-urlencoded`.
   
   * **Текст HTTP-запроса** — `grant_type=client\_credentials&client_id={CLIENT_ID}&client_secret={CLIENT_SECRET}&resource=https%3A%2F%2Fmanagement.core.windows.net%2F`.
     
    Вот пример запроса:
    ```
    POST /{TENANT_ID}/oauth2/token HTTP/1.1
    Host: login.microsoftonline.com
    Content-Type: application/x-www-form-urlencoded
    grant_type=client_credentials&client_id={CLIENT_ID}&client_secret={CLIENT_SECRET}&reso
    urce=https%3A%2F%2Fmanagement.core.windows.net%2F
    ```
    Далее представлен пример ответа на запрос:
    ```
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Content-Length: 1234

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1445395811","not_before":"144
    5391911","resource":"https://management.core.windows.net/","access_token":{ACCESS_TOKEN}}
    ```
     В этом примере предусмотрено кодирование параметров POST в URL-адреса, где `resource` на самом деле имеет значение `https://management.core.windows.net/`. Следует также закодировать параметр `{CLIENT_SECRET}` в URL-адрес, так как в нем могут содержаться специальные знаки.

     > [!NOTE]
     > Для тестирования можно использовать средство клиента HTTP, такое как [Fiddler](http://www.telerik.com/fiddler) или [расширение Chrome Postman](https://chrome.google.com/webstore/detail/postman/fhbjgbiflinjbdggehcddcbncdddomop). 
     > 
     > 
2. Теперь при каждом вызове API добавляйте в запрос заголовок авторизации:
   
        Authorization: Bearer {ACCESS_TOKEN}
   
    Если запрос возвращает код состояния 401, проверьте текст ответа. Возможно, срок действия маркера истек. В этом случае следует получить новый маркер.

## <a name="use-the-apis"></a>Использование API-интерфейсов
Теперь, когда есть допустимый маркер, можно вызывать API.

1. В каждом запросе API необходимо передать допустимый маркер, срок действия которого не истек. Такой маркер вы получили в предыдущем разделе.

2. Добавьте некоторые параметры к универсальному коду ресурса (URI) запроса, идентифицирующему приложение. Он будет выглядеть как следующий код:
   
        https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/
        providers/Microsoft.MobileEngagement/appcollections/{app-collection}/apps/{app-resource-name}/
   
    Чтобы получить параметры, выберите имя приложения. Затем выберите **панель мониторинга**. Отобразится страница со всеми тремя параметрами, как показано ниже.
   
   * **1** `{subscription-id}`
   * **2** `{app-collection}`
   * **3** `{app-resource-name}`
   * **4** Ваша группа ресурсов будет носить имя **MobileEngagement** до тех пор, пока не будет создана новая группа. 

> [!NOTE]
> Игнорируйте параметр корневого адреса API, так как он предназначался для предыдущих API.
> 
> Если вы создали приложение с помощью портала Azure, используемое имя ресурса приложения должно отличаться от имени самого приложения. Если вы создали приложение на портале Azure, следует использовать имя приложения. (Для приложений, созданных на новом портале, нет никакой разницы между именем ресурса приложения и именем приложения.)
> 
> 

<!-- Images -->
[1]: ./media/mobile-engagement-api-authentication/azure-module.png
[2]: ./media/mobile-engagement-api-authentication/mobile-engagement-api-uri-params.png
[3]: ./media/mobile-engagement-api-authentication/ps-cmdlets.png
[4]: ./media/mobile-engagement-api-authentication/search-app.png



