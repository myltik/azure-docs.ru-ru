---
title: "Доменные службы Azure Active Directory: устранение неполадок при настройке субъекта-службы | Документация Майкрософт"
description: "Устранение неполадок конфигурации субъекта-службы для доменных служб Azure AD"
services: active-directory-ds
documentationcenter: 
author: eringreenlee
manager: 
editor: 
ms.assetid: f168870c-b43a-4dd6-a13f-5cfadc5edf2c
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/19/2018
ms.author: ergreenl
ms.openlocfilehash: 7388bb291f665f195355a01d19a82cba9ed453eb
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/21/2018
---
# <a name="troubleshoot-invalid-service-principal-configuration-for-your-managed-domain"></a>Устранение неполадок с недопустимой конфигурацией субъекта-службы для управляемого домена

Эта статья поможет вам определить и устранить ошибки конфигурации субъекта-службы, которые приводят к появлению следующего оповещения.

## <a name="alert-aadds102-service-principal-not-found"></a>Оповещение AADDS102: субъект-служба не найдена
**Оповещение.** *Субъект-служба, необходимая для правильной работы доменных служб Azure AD, удалена из каталога Azure AD. Эта конфигурация влияет на возможность корпорации Майкрософт наблюдать за управляемым доменом, управлять им, обновлять и синхронизировать его.*

[Субъекты-службы](../active-directory/develop/active-directory-application-objects.md) — приложения, которые корпорация Майкрософт использует для обновления и обслуживания управляемого домена, а также для управления им. При их удалении корпорация Майкрософт не сможет обслуживать домен. 


## <a name="check-for-missing-service-principals"></a>Проверка отсутствующих субъектов-служб
Выполните следующие действия, чтобы узнать, какие субъекты-службы нужно создать повторно.

1. Перейдите к странице [Корпоративные приложения — Все приложения](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps) на портале Azure.
2. В раскрывающемся списке **Показать** выберите **Все приложения** и нажмите кнопку **Применить**.
3. Используя следующую таблицу, выполните поиск каждого идентификатора приложения (вставьте код в поле поиска и нажмите клавишу ВВОД). Если результаты поиска пусты, необходимо повторно создать субъект-службы в соответствии с шагами, описанными в столбце "Устранение проблемы".

| Идентификатор приложения | Способы устранения: |
| :--- | :--- | :--- |
| 2565bd9d-da50-47d4-8b85-4c97f669dc36 | [Повторное создание отсутствующего субъекта-службы с помощью PowerShell](#recreate-a-missing-service-principal-with-powershell) |
| 443155a6-77f3-45e3-882b-22b3a8d431fb | [Повторная регистрация в пространстве имен Microsoft.AAD](#re-register-to-the-microsoft-aad-namespace-using-the-azure-portal) |
| abba844e-bc0e-44b0-947a-dc74e5d09022  | [Повторная регистрация в пространстве имен Microsoft.AAD](#re-register-to-the-microsoft-aad-namespace-using-the-azure-portal) |
| d87dcbc6-a371-462e-88e3-28ad15ec4e64 | [Субъекты-службы, которые являются самокорректирующимися](#service-principals-that-self-correct) |

## <a name="recreate-a-missing-service-principal-with-powershell"></a>Повторное создание отсутствующего субъекта-службы с помощью PowerShell
Выполните следующие действия, если субъект-служба с идентификатором ```2565bd9d-da50-47d4-8b85-4c97f669dc36``` отсутствует в каталоге Azure AD.

**Исправление**. Для выполнения этих инструкций требуется Azure AD PowerShell. Сведения об установке Azure AD PowerShell см. в [этой статье](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0.).

Чтобы устранить эту проблему, введите следующие команды в окне PowerShell:
1. Установите модуль Azure AD PowerShell и импортируйте его.
    
    ```powershell 
    Install-Module AzureAD
    Import-Module AzureAD
    ```
    
2. Проверьте, отсутствует ли в вашем каталоге субъект-служба, необходимая для доменных служб Azure AD, с помощью следующей команды PowerShell:
    
    ```powershell
    Get-AzureAdServicePrincipal -filter "AppId eq '2565bd9d-da50-47d4-8b85-4c97f669dc36'"
    ```
    
3. Создайте субъект-службу, введя следующую команду PowerShell:

    ```powershell
    New-AzureAdServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
    ```
    
4. После создания отсутствующего субъекта-службы подождите два часа, затем проверьте работоспособность управляемого домена.


## <a name="re-register-to-the-microsoft-aad-namespace-using-the-azure-portal"></a>Повторная регистрация в пространстве имен Microsoft.AAD с помощью портала Azure
Выполните следующие действия, если субъект-служба с идентификатором ```443155a6-77f3-45e3-882b-22b3a8d431fb``` или ```abba844e-bc0e-44b0-947a-dc74e5d09022``` отсутствует в каталоге Azure AD.

**Исправление**. Чтобы восстановить доменные службы в каталоге, сделайте следующее.

1. Перейдите к странице [Подписки](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) на портале Azure.
2. Из таблицы, связанной с управляемым доменом, выберите подписку.
3. На панели навигации слева выберите **Поставщики ресурсов**
4. Найдите Microsoft.AAD в таблице и нажмите кнопку **Повторная регистрация**.
5. Чтобы убедиться, что причина оповещения устранена, просмотрите страницу работоспособности для управляемого домена через два часа.


## <a name="service-principals-that-self-correct"></a>Субъекты-службы, которые являются самокорректирующимися
Выполните следующие действия, если субъект-служба с идентификатором ```d87dcbc6-a371-462e-88e3-28ad15ec4e64``` отсутствует в каталоге Azure AD.

**Исправление**. Доменные службы Azure AD могут обнаружить, если субъект-служба отсутствует, неправильно настроена или удалена. Служба автоматически восстанавливает этот субъект-службу. Чтобы убедиться, что субъект-служба создана повторно, проверьте работоспособность управляемого домена через два часа.


## <a name="contact-us"></a>Свяжитесь с нами
Чтобы [оставить отзыв или обратиться за помощью](active-directory-ds-contact-us.md), свяжитесь с командой разработки продукта, отвечающей за доменные службы Azure Active Directory.
