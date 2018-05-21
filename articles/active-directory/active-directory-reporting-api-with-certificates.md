---
title: Получение данных, используя API отчетов Azure AD с сертификатами | Документация Майкрософт
description: Описание использования API отчетов Azure AD с учетными данными сертификатов для получения данных из каталогов без вмешательства пользователя.
services: active-directory
documentationcenter: ''
author: ramical
writer: v-lorisc
manager: kannar
ms.assetid: ''
ms.service: active-directory
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/07/2018
ms.author: ramical
ms.openlocfilehash: 54e661284c539b835089e858ba7b5e0016e89a83
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2018
---
# <a name="get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>Получение данных с помощью API отчетов Azure Active Directory с сертификатами

[API-интерфейсы отчетов Azure Active Directory (Azure AD)](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) предоставляют программный доступ к данным с помощью набора API-интерфейсов на базе REST. Эти интерфейсы API можно вызвать, используя различные языки и инструменты программирования.

Чтобы получить доступ к API отчетов Azure AD без вмешательства пользователя, можно настроить доступ с помощью сертификатов.

В этой статье:

- описываются необходимые действия для доступа к API отчетов Azure AD с помощью сертификатов;
- предполагается, что вы выполнили [предварительные требования для доступа к API отчетов Azure Active Directory](active-directory-reporting-api-prerequisites-azure-portal.md). 


Чтобы получить доступ к API отчетов с помощью сертификатов, необходимо сделать следующее:

1. Установка необходимых компонентов
2. установить сертификат в приложении; 
3. Предоставление разрешений
4. Получение маркера доступа




Сведения об исходном коде см. в [практическом руководстве по использованию модуля API отчетов](https://github.com/AzureAD/azure-activedirectory-powershell/tree/gh-pages/Modules/AzureADUtils). 

## <a name="install-prerequisites"></a>установить необходимые компоненты;

Необходимо установить Azure AD PowerShell версии 2 и модуль служебных программ Azure AD.

1. Скачайте и установите Azure AD PowerShell V2, следуя инструкциям, описанным в статье [Azure Active Directory PowerShell версии 2](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/Azure AD Cmdlets/AzureAD/index.md).

2. Скачайте модуль служебных программ Azure AD на сайте [AzureAD/azure-activedirectory-powershell](https://github.com/AzureAD/azure-activedirectory-powershell/blob/gh-pages/Modules/AzureADUtils/AzureADUtils.psm1). 
  Этот модуль предоставляет несколько служебных командлетов, позволяющих получить:
    - последнюю версию ADAL с использованием Nuget;
    - маркеры доступа пользователя, ключи приложений и сертификаты с использованием ADAL;
    - обработку результатов с разбивкой на страницы с помощью API Graph.

**Чтобы установить модуль служебных программ Azure AD:**

1. Создайте каталог, чтобы сохранить модуль служебных программ (например, C:\azureAD), и скачайте модуль с сайта GitHub.
2. Откройте сеанс PowerShell и перейдите в созданный каталог. 
3. Импортируйте модуль и установите его в путь к модулю PowerShell с помощью командлета Install-AzureADUtilsModule. 

Сеанс должен выглядеть так, как показано ниже.

  ![Windows PowerShell](./media/active-directory-report-api-with-certificates/windows-powershell.png)

## <a name="set-the-certificate-in-your-app"></a>установить сертификат в приложении;

**Чтобы установить сертификат в приложении, сделайте следующее.**

1. [Получите идентификатор объекта](active-directory-reporting-api-prerequisites-azure-portal.md#get-your-applications-client-id) для приложения на портале Azure. 

  ![Портал Azure](./media/active-directory-report-api-with-certificates/azure-portal.png)

2. Откройте сеанс PowerShell и подключитесь к Azure AD с помощью командлета Connect-AzureAD.

  ![Портал Azure](./media/active-directory-report-api-with-certificates/connect-azuaread-cmdlet.png)

3. Используйте командлет New-AzureADApplicationCertificateCredential из модуля служебных программ Azure AD, чтобы добавить учетные данные сертификата. 

>[!Note]
>Необходимо указать сохраненный ранее идентификатор объекта, а также объект сертификата (с использованием диска Cert:).
>


  ![Портал Azure](./media/active-directory-report-api-with-certificates/add-certificate-credential.png)
  
## <a name="get-an-access-token"></a>Получение маркера доступа

Чтобы получить маркер доступа, используйте командлет **Get-AzureADGraphAPIAccessTokenFromCert** из модуля служебных программ Azure AD. 

>[!NOTE]
>Необходимо использовать идентификатор приложения вместо идентификатора объекта, который использовался в предыдущем разделе.
>

 ![Портал Azure](./media/active-directory-report-api-with-certificates/application-id.png)

## <a name="use-the-access-token-to-call-the-graph-api"></a>использовать маркер доступа для вызова API Graph.

Теперь можно создать скрипт. Ниже приведен пример использования командлета **Invoke-AzureADGraphAPIQuery** из модуля служебных программ Azure AD. Этот командлет обрабатывает результаты с разбивкой на несколько страниц, а затем отправляет их в конвейер PowerShell. 

 ![Портал Azure](./media/active-directory-report-api-with-certificates/script-completed.png)

Теперь можно выполнить экспорт в CSV-файл и сохранить его в системе SIEM. Также можно перенести скрипт в запланированную задачу, чтобы периодически получать данные Azure AD из клиента без необходимости сохранять ключи приложений в исходном коде. 

## <a name="next-steps"></a>Дополнительная информация

- [Ознакомление с API отчетов](active-directory-reporting-api-getting-started-azure-portal.md#explore)

- [Создание собственного решения](active-directory-reporting-api-getting-started-azure-portal.md#customize)




