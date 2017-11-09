---
title: "Начало работы с защитой идентификации Azure Active Directory и Microsoft Graph | Документация Майкрософт"
description: "Общие сведения о выполнении запросов с помощью Microsoft Graph для получения списка рисковых событий и связанных сведений из Azure Active Directory."
services: active-directory
keywords: "защита идентификации azure active directory, события риска, уязвимость, политика безопасности, Microsoft Graph"
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: fa109ba7-a914-437b-821d-2bd98e681386
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 568cad4e394dfedddce1bfce66ddf627947d7568
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/09/2017
---
# <a name="get-started-with-azure-active-directory-identity-protection-and-microsoft-graph"></a>Начало работы с защитой идентификации Azure Active Directory и Microsoft Graph
Microsoft Graph — это конечная точка Unified API (Майкрософт) и источник интерфейсов API [защиты идентификации Azure Active Directory](active-directory-identityprotection.md). Первый API, **identityRiskEvents**, позволяет запрашивать у Microsoft Graph список [событий риска](active-directory-identityprotection-risk-events-types.md) и связанные с ними сведения. В статье описывается, как выполнять запросы к этому API. Дополнительные сведения, полную документацию и доступ к Graph Explorer можно получить на [сайте Microsoft Graph](https://graph.microsoft.io/).

> [!IMPORTANT]
> Для управления службой Azure AD мы рекомендуем использовать [Центр администрирования Azure AD](https://aad.portal.azure.com) на портале Azure, а не классический портал Azure, который упоминается в этой статье.

Получить доступ к данным защиты идентификации с помощью Microsoft Graph можно в три этапа.

1. Добавление приложения с секретом клиента. 
2. Использование этого секрета и других данных для прохождения проверки подлинности в Microsoft Graph и получения маркера проверки подлинности. 
3. Использование полученного маркера для отправки запросов к конечной точке API и получения данных защиты идентификации.

Перед началом работы вам потребуются:

* права администратора для создания приложения в Azure AD;
* имя домена клиента (например, contoso.onmicrosoft.com).

## <a name="add-an-application-with-a-client-secret"></a>Добавление приложения с секретом клиента
1. [Войдите](https://manage.windowsazure.com) на классический портал Azure, используя учетную запись администратора. 
2. В области навигации слева щелкните **Active Directory**. 
   
    ![Создание приложения](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_01.png)
3. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
4. В меню вверху щелкните **Приложения**.
   
    ![Создание приложения](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_02.png)
5. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Создание приложения](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_03.png)
6. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение, разрабатываемое моей организацией**.
   
    ![Создание приложения](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_04.png)
7. В диалоговом окне **Расскажите о своем приложении** выполните следующие действия.
   
    ![Создание приложения](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_05.png)
   
    а. В текстовое поле **Имя** введите имя приложения (например, приложение API рискового события AADIP).
   
    b. В качестве **типа** выберите **Веб-приложение и/или веб-API**.
   
    В. Щелкните **Далее**.
8. В диалоговом окне **Свойства приложения** выполните следующие действия:
   
    ![Создание приложения](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_06.png)
   
    а. В текстовом поле **URL-адрес для входа** введите `http://localhost`.
   
    b. В текстовом поле **URI кода приложения** введите `http://localhost`.
   
    c. Нажмите **Завершено**.

Теперь вы можете настроить приложение.

![Создание приложения](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_07.png)

## <a name="grant-your-application-permission-to-use-the-api"></a>Предоставление приложению разрешения на использование API
1. На странице приложения в меню вверху щелкните **Настройка**. 
   
    ![Создание приложения](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_08.png)
2. В разделе **Разрешения для других приложений** щелкните **Добавить приложение**.
   
    ![Создание приложения](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_09.png)
3. В диалоговом окне **Разрешения для других приложений** выполните следующие действия:
   
    ![Создание приложения](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_10.png)
   
    а. Выберите **Microsoft Graph**.
   
    b. Нажмите **Завершено**.
4. Щелкните **Разрешения приложения: 0**, а затем выберите **Read all identity risk event information** (Прочитать все сведения о событиях риска идентификации).
   
    ![Создание приложения](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_11.png)
5. В нижней части страницы нажмите кнопку **Сохранить** .
   
    ![Создание приложения](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_12.png)

## <a name="get-an-access-key"></a>Получение ключа доступа
1. На странице приложения в разделе **ключей** выберите для продолжительности значение в 1 год.
   
    ![Создание приложения](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_13.png)
2. В нижней части страницы нажмите кнопку **Сохранить** .
   
    ![Создание приложения](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_12.png)
3. В разделе ключей скопируйте значение созданного ключа и сохраните его в надежном месте.
   
    ![Создание приложения](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_14.png)
   
   > [!NOTE]
   > Если ключ будет утерян, вам нужно будет вернуться в этот раздел и создать новый ключ. Не предоставляйте этот ключ никому: с его помощью кто угодно может получить доступ к вашим данным.
   > 
   > 
4. В разделе **свойств** скопируйте **идентификатор клиента** и сохраните его в надежном месте. 

## <a name="authenticate-to-microsoft-graph-and-query-the-identity-risk-events-api"></a>Проверка подлинности в Microsoft Graph и выполнение запросов с помощью API рисковых событий идентификации
На этом этапе вам понадобятся:

* скопированный ранее идентификатор клиента;
* скопированный ранее ключ;
* имя домена клиента.

Чтобы выполнить проверку подлинности, отправьте запрос POST по адресу `https://login.microsoft.com` со следующими параметрами в тексте:

* grant_type: “**client_credentials**”
* resource: “**https://graph.microsoft.com**”
* client_id: <your client ID>
* client_secret: <your key>

> [!NOTE]
> Необходимо указать значения для параметров **client_id** и **client_secret**.
> 
> 

В случае успешного выполнения этот метод возвращает маркер проверки подлинности.  
Для вызова API создайте заголовок со следующим параметром:

    `Authorization`=”<token_type> <access_token>"


При проверке подлинности тип маркера и маркер доступа можно найти в возвращаемом маркере.

Отправьте этот заголовок как запрос по следующему URL-адресу API: `https://graph.microsoft.com/beta/identityRiskEvents`

В случае успеха в ответ вы получите коллекцию рисковых событий идентификации и связанных данных в формате OData JSON. Эти данные можно проанализировать и обработать по своему усмотрению.

Ниже приведен образец кода для проверки подлинности и вызова API с помощью Powershell.  
Просто добавьте идентификатор клиента, ключ и домен клиента.

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


## <a name="next-steps"></a>Дальнейшие действия
Поздравляем, вы только что выполнили первый вызов Microsoft Graph.  
Теперь вы можете запрашивать рисковые события идентификации и использовать данные по своему усмотрению.

Дополнительные сведения о Microsoft Graph и инструкции по созданию приложения с помощью API Graph см. в [документации](https://graph.microsoft.io/docs), а также на [веб-сайте Microsoft Graph](https://graph.microsoft.io/). Кроме того, сохраните в закладки страницу, посвященную [API защиты идентификации Azure AD](https://graph.microsoft.io/docs/api-reference/beta/resources/identityprotection_root). Она содержит список всех доступных в Graph интерфейсов API защиты идентификации. Мы будем добавлять на эту страницу новые способы работы с защитой идентификации через API, поэтому следите за новостями.

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Защита идентификации Azure Active Directory.](active-directory-identityprotection.md)
* [Типы событий риска, обнаруживаемые защитой идентификации Azure Active Directory](active-directory-identityprotection-risk-events-types.md)
* [Microsoft Graph](https://graph.microsoft.io/)
* [Overview of Microsoft Graph (Обзор Microsoft Graph)](https://graph.microsoft.io/docs)
* [Azure AD Identity Protection Service Root (Корень службы защиты идентификации Azure AD)](https://graph.microsoft.io/docs/api-reference/beta/resources/identityprotection_root)

