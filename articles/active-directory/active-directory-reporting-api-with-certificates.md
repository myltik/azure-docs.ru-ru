---
title: "Получение данных, используя API отчетов Azure AD с сертификатами | Документация Майкрософт"
description: "Описание использования API отчетов Azure AD с учетными данными сертификатов для получения данных из каталогов без вмешательства пользователя."
services: active-directory
documentationcenter: 
author: ramical
writer: v-lorisc
manager: kannar
ms.assetid: 
ms.service: active-directory
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/08/2017
ms.author: ramical
ms.openlocfilehash: 4900e47084256ad6c85886f7ba363399678da9aa
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2017
---
# <a name="get-data-using-the-azure-ad-reporting-api-with-certificates"></a>Получение данных, используя API отчетов Azure AD с сертификатами
В статье описывается использование API отчетов Azure AD с учетными данными сертификатов для получения данных из каталогов без вмешательства пользователя. 

## <a name="use-the-azure-ad-reporting-api"></a>Использование API отчетов Azure AD 
Чтобы использовать API отчетов Azure AD, нужно выполнить следующие действия:
 *  установить необходимые компоненты;
 *  установить сертификат в приложении;
 *  Получение маркера доступа
 *  использовать маркер доступа для вызова API Graph.

Сведения об исходном коде см. в [практическом руководстве по использованию модуля API отчетов](https://github.com/AzureAD/azure-activedirectory-powershell/tree/gh-pages/Modules/AzureADUtils). 

### <a name="install-prerequisites"></a>Установка необходимых компонентов
Необходимо установить Azure AD PowerShell V2 и модуль служебных программ Azure AD.

1. Скачайте и установите Azure AD PowerShell V2, следуя инструкциям, описанным в статье [Azure Active Directory PowerShell версии 2](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/Azure AD Cmdlets/AzureAD/index.md).
2. Скачайте модуль служебных программ Azure AD на сайте [AzureAD/azure-activedirectory-powershell](https://github.com/AzureAD/azure-activedirectory-powershell/blob/gh-pages/Modules/AzureADUtils/AzureADUtils.psm1). 
  Этот модуль предоставляет несколько служебных командлетов, позволяющих получить:
   * последнюю версию ADAL с использованием Nuget;
   * маркеры доступа пользователя, ключи приложений и сертификаты с использованием ADAL;
   * обработку результатов с разбивкой на страницы с помощью API Graph.

**Чтобы установить модуль служебных программ Azure AD:**

1. Создайте каталог, чтобы сохранить модуль служебных программ (например, C:\azureAD), и скачайте модуль с сайта GitHub.
2. Откройте сеанс PowerShell и перейдите в созданный каталог. 
3. Импортируйте модуль и установите его в путь к модулю PowerShell с помощью командлета Install-AzureADUtilsModule. 

Сеанс должен выглядеть так, как показано ниже.

  ![Windows PowerShell](./media/active-directory-report-api-with-certificates/windows-powershell.png)

### <a name="set-the-certificate-in-your-app"></a>Установка сертификата в приложении
1. Если приложение уже имеется, получите его идентификатор объекта на портале Azure. 

  ![Портал Azure](./media/active-directory-report-api-with-certificates/azure-portal.png)

2. Откройте сеанс PowerShell и подключитесь к Azure AD с помощью командлета Connect-AzureAD.

  ![Портал Azure](./media/active-directory-report-api-with-certificates/connect-azuaread-cmdlet.png)

3. Используйте командлет New-AzureADApplicationCertificateCredential из модуля служебных программ Azure AD, чтобы добавить учетные данные сертификата. 

>[!Note]
>Необходимо указать сохраненный ранее идентификатор объекта, а также объект сертификата (с использованием диска Cert:).
>


  ![Портал Azure](./media/active-directory-report-api-with-certificates/add-certificate-credential.png)
  
### <a name="get-an-access-token"></a>Получение маркера доступа

Чтобы получить маркер доступа, используйте командлет Get-AzureADGraphAPIAccessTokenFromCert из модуля служебных программ Azure AD. 

>[!NOTE]
>Необходимо использовать идентификатор приложения вместо идентификатора объекта, который использовался в предыдущем разделе.
>

 ![Портал Azure](./media/active-directory-report-api-with-certificates/application-id.png)

### <a name="use-the-access-token-to-call-the-graph-api"></a>Использование маркера доступа для вызова API Graph

Теперь можно создать скрипт. Ниже приведен пример использования командлета Invoke-AzureADGraphAPIQuery из модуля служебных программ Azure AD. Этот командлет обрабатывает результаты с разбивкой на несколько страниц, а затем отправляет их в конвейер PowerShell. 

 ![Портал Azure](./media/active-directory-report-api-with-certificates/script-completed.png)

Теперь можно выполнить экспорт в CSV-файл и сохранить его в системе SIEM. Также можно перенести скрипт в запланированную задачу, чтобы периодически получать данные Azure AD из клиента без необходимости сохранять ключи приложений в исходном коде. 

## <a name="next-steps"></a>Дополнительная информация
[Основы управления удостоверениями Azure](https://docs.microsoft.com/azure/active-directory/fundamentals-identity)<br>



