---
title: "Использование Microsoft Graph для защиты идентификации Azure Active Directory | Документация Майкрософт"
description: "Узнайте, как выполнять запросы к Microsoft Graph для получения списка событий риска и связанных сведений из Azure Active Directory."
services: active-directory
keywords: "защита идентификации azure active directory, события риска, уязвимость, политика безопасности, Microsoft Graph"
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: fa109ba7-a914-437b-821d-2bd98e681386
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: markvi
ms.reviewer: nigu
ms.custom: seohack1
ms.openlocfilehash: df0d89fc93f1b9c19d669c29306398a8b25ee425
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/18/2018
---
# <a name="get-started-with-azure-active-directory-identity-protection-and-microsoft-graph"></a>Начало работы с защитой идентификации Azure Active Directory и Microsoft Graph
Microsoft Graph — это конечная точка Unified API (Майкрософт) и источник интерфейсов API [защиты идентификации Azure Active Directory](active-directory-identityprotection.md). Первый API, **identityRiskEvents**, позволяет запрашивать у Microsoft Graph список [событий риска](active-directory-identityprotection-risk-events-types.md) и связанные с ними сведения. В статье описывается, как выполнять запросы к этому API. Дополнительные сведения, полную документацию и доступ к Graph Explorer можно получить на [сайте Microsoft Graph](https://graph.microsoft.io/).


Получить доступ к данным защиты идентификации с помощью Microsoft Graph можно в четыре этапа:

1. Получение имени домена.
2. Создание регистрации приложения. 
2. Использование этого секрета и других данных для прохождения проверки подлинности в Microsoft Graph и получения маркера проверки подлинности. 
3. Использование полученного маркера для отправки запросов к конечной точке API и получения данных защиты идентификации.

Перед началом работы вам потребуются:

* права администратора для создания приложения в Azure AD;
* имя домена клиента (например, contoso.onmicrosoft.com).


## <a name="retrieve-your-domain-name"></a>Получение имени домена 

1. [Войдите](https://portal.azure.com) на портал Azure с учетной записью администратора. 

2. В области навигации слева щелкните **Active Directory**. 
   
    ![Создание приложения](./media/active-directory-identityprotection-graph-getting-started/41.png)


3. В разделе **Управление** щелкните **Свойства**.

    ![Создание приложения](./media/active-directory-identityprotection-graph-getting-started/42.png)

4. Скопируйте имя домена.


## <a name="create-a-new-app-registration"></a>Создание регистрации приложения

1. На странице **Active Directory** в разделе **Управление** щелкните **Регистрация приложений**.

    ![Создание приложения](./media/active-directory-identityprotection-graph-getting-started/42.png)


2. В меню в верхней части страницы щелкните **Регистрация нового приложения**.
   
    ![Создание приложения](./media/active-directory-identityprotection-graph-getting-started/43.png)

3. На странице **Создание** выполните следующие действия.
   
    ![Создание приложения](./media/active-directory-identityprotection-graph-getting-started/44.png)

    a. В текстовое поле **Имя** введите имя приложения (например, приложение API рискового события AADIP).
   
    Б. В качестве **типа** выберите **Веб-приложение и/или веб-API**.
   
    c. В текстовом поле **URL-адрес входа** введите `http://localhost`.

    d. Нажмите кнопку **Создать**.

4. Чтобы открыть страницу **Параметры**, в списке приложений щелкните только что созданную регистрацию приложения. 

5. Скопируйте **идентификатор приложения**.


## <a name="grant-your-application-permission-to-use-the-api"></a>Предоставление приложению разрешения на использование API

1. На странице **Параметры** щелкните **Необходимые разрешения**.
   
    ![Создание приложения](./media/active-directory-identityprotection-graph-getting-started/15.png)

2. На странице **Необходимые разрешения** на панели инструментов вверху нажмите кнопку **Добавить**.
   
    ![Создание приложения](./media/active-directory-identityprotection-graph-getting-started/16.png)
   
3. На странице **Добавить доступ через API** щелкните **Выбор API**.
   
    ![Создание приложения](./media/active-directory-identityprotection-graph-getting-started/17.png)

4. На странице **Выбор API** выберите **Microsoft Graph**, а затем нажмите кнопку **Выбор**.
   
    ![Создание приложения](./media/active-directory-identityprotection-graph-getting-started/18.png)

5. На странице **Добавить доступ через API** щелкните **Выбрать разрешения**.
   
    ![Создание приложения](./media/active-directory-identityprotection-graph-getting-started/19.png)

6. На странице **Включение доступа** щелкните **Read all identity risk information** (Прочитать все сведения о рисках идентификаторов), а затем щелкните **Выбор**.
   
    ![Создание приложения](./media/active-directory-identityprotection-graph-getting-started/20.png)

7. На странице **Добавить доступ через API** щелкните **Готово**.
   
    ![Создание приложения](./media/active-directory-identityprotection-graph-getting-started/21.png)

8. На странице **Требуемые разрешения** щелкните **Предоставить разрешения** и **Да**.
   
    ![Создание приложения](./media/active-directory-identityprotection-graph-getting-started/22.png)



## <a name="get-an-access-key"></a>Получение ключа доступа

1. На странице **Параметры** щелкните **Ключи**.
   
    ![Создание приложения](./media/active-directory-identityprotection-graph-getting-started/23.png)

2. На странице **Ключи** выполните следующие действия.
   
    ![Создание приложения](./media/active-directory-identityprotection-graph-getting-started/24.png)

    a. В текстовом поле **Описание ключа** введите описание (например, *Событие риска AADIP*).
    
    Б. Для параметра **Длительность** выберите значение **Через один год**.

    c. Выберите команду **Сохранить**.
   
    d. Скопируйте значение ключа и вставьте его в безопасное место.   
   
   > [!NOTE]
   > Если ключ будет утерян, вам нужно будет вернуться в этот раздел и создать новый ключ. Не предоставляйте этот ключ никому: с его помощью кто угодно может получить доступ к вашим данным.
   > 
   > 

## <a name="authenticate-to-microsoft-graph-and-query-the-identity-risk-events-api"></a>Проверка подлинности в Microsoft Graph и выполнение запросов с помощью API рисковых событий идентификации
На этом этапе вам понадобятся:

- имя домена клиента;

- идентификатор клиента; 

- ключ. 


Чтобы выполнить проверку подлинности, отправьте запрос POST по адресу `https://login.microsoft.com` со следующими параметрами в тексте:

- grant_type: “**client_credentials**”

-  resource: “**https://graph.microsoft.com**”

- client_id: \<ваш идентификатор клиента\>;

- client_secret: \<ваш ключ\>.


В случае успешного выполнения этот метод возвращает маркер проверки подлинности.  
Для вызова API создайте заголовок со следующим параметром:

    `Authorization`=”<token_type> <access_token>"


При проверке подлинности тип маркера и маркер доступа можно найти в возвращаемом маркере.

Отправьте этот заголовок как запрос по следующему URL-адресу API: `https://graph.microsoft.com/beta/identityRiskEvents`

В случае успеха в ответ вы получите коллекцию событий риска идентификаторов и связанных данных в формате OData JSON. Эти данные можно проанализировать и обработать по своему усмотрению.

Ниже приведен пример кода для аутентификации и вызова API с помощью PowerShell.  
Просто добавьте свои идентификатор клиента, секретный ключ и домен клиента.

    $ClientID       = "<your client ID here>"        # Should be a ~36 hex character string; insert your info here
    $ClientSecret   = "<your client secret here>"    # Should be a ~44 character string; insert your info here
    $tenantdomain   = "<your tenant domain here>"    # For example, contoso.onmicrosoft.com

    $loginURL       = "https://login.microsoft.com"
    $resource       = "https://graph.microsoft.com"

    $body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
    $oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

    Write-Output $oauth

    if ($oauth.access_token -ne $null) {
        $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

        $url = "https://graph.microsoft.com/beta/identityRiskEvents"
        Write-Output $url

        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)

        foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
            Write-Output $event
        }

    } else {
        Write-Host "ERROR: No Access Token"
    } 


## <a name="next-steps"></a>Дополнительная информация

Поздравляем, вы только что выполнили первый вызов Microsoft Graph.  
Теперь вы можете запрашивать рисковые события идентификации и использовать данные по своему усмотрению.

Дополнительные сведения о Microsoft Graph и инструкции по созданию приложения с помощью API Graph см. в [документации](https://graph.microsoft.io/docs), а также на [веб-сайте Microsoft Graph](https://graph.microsoft.io/). Кроме того, сохраните в закладки страницу, посвященную [API защиты идентификации Azure AD](https://graph.microsoft.io/docs/api-reference/beta/resources/identityprotection_root). Она содержит список всех доступных в Graph интерфейсов API защиты идентификации. Мы будем добавлять на эту страницу новые способы работы с защитой идентификации через API, поэтому следите за новостями.

Связанные сведения:

-  [Защита идентификации Azure Active Directory.](active-directory-identityprotection.md)

-  [Типы событий риска, обнаруживаемые защитой идентификации Azure Active Directory](active-directory-identityprotection-risk-events-types.md)

- [Microsoft Graph](https://graph.microsoft.io/)

- [Overview of Microsoft Graph (Обзор Microsoft Graph)](https://graph.microsoft.io/docs)

- [Azure AD Identity Protection Service Root (Корень службы защиты идентификации Azure AD)](https://graph.microsoft.io/docs/api-reference/beta/resources/identityprotection_root)

