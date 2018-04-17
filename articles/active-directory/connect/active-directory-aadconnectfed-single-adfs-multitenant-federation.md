---
title: Федерация нескольких экземпляров Azure AD с одним экземпляром AD FS | Документация Майкрософт
description: Из этого документа вы узнаете, как создать федерацию нескольких экземпляров Azure AD с одним экземпляром AD FS.
keywords: федерация, ADFS, AD FS, несколько клиентов, один клиент AD FS, один экземпляр ADFS, федерация нескольких клиентов, федерация ADFS с несколькими лесами, AAD Сonnect, федерация, федерация между клиентами
services: active-directory
documentationcenter: ''
author: anandyadavmsft
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/17/2017
ms.author: anandy; billmath
ms.openlocfilehash: c55a4232c54308c5d000cfefc2c7dca2800b462c
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/23/2018
---
# <a name="federate-multiple-instances-of-azure-ad-with-single-instance-of-ad-fs"></a>Федерация нескольких экземпляров Azure AD с одним экземпляром AD FS

Одна высокодоступная ферма AD FS может объединять несколько лесов при наличии взаимного доверия между ними. Эти несколько лесов могут соответствовать или не соответствовать одной службе Azure Active Directory. Эта статья содержит инструкции по настройке федерации между одним экземпляром AD FS и несколькими лесами, которые синхронизируются с другим экземпляром Azure AD.

![Федерация нескольких клиентов с одним экземпляром AD FS](media/active-directory-aadconnectfed-single-adfs-multitenant-federation/concept.png)
 
> [!NOTE]
> В этом сценарии не поддерживаются обратная запись устройств и автоматическое присоединение устройств.

> [!NOTE]
> Azure AD Connect нельзя использовать для настройки федерации в этом сценарии, так как Azure AD Connect может настраивать федерацию для доменов в одном экземпляре Azure AD.

## <a name="steps-for-federating-ad-fs-with-multiple-azure-ad"></a>Шаги для федерации AD FS и нескольких экземпляров Azure AD

Представьте, что домен contoso.com в Azure Active Directory (contoso.onmicrosoft.com) включен в федерацию локального экземпляра AD FS, установленного в локальной среде Active Directory contoso.com. Fabrikam.com является доменом в fabrikam.onmicrosoft.com Azure Active Directory.

## <a name="step-1-establish-a-two-way-trust"></a>Шаг 1. Установка взаимного доверия
 
Чтобы служба AD FS в contoso.com могла проверять подлинность пользователей в fabrikam.com, требуется взаимное доверие между contoso.com и fabrikam.com. Следуйте рекомендациям, приведенным в этой [статье](https://technet.microsoft.com/library/cc816590.aspx), чтобы создать отношения взаимного доверия.
 
## <a name="step-2-modify-contosocom-federation-settings"></a>Шаг 2. Изменение параметров федерации contoso.com 
 
Издателем отдельного домена, включенного в федерацию AD FS, по умолчанию является http://ADFSServiceFQDN/adfs/services/trust, например http://fs.contoso.com/adfs/services/trust. Служба Azure Active Directory требует отдельного издателя для каждого федеративного домена. Так как один и тот же экземпляр AD FS будет включать в федерацию два домена, значение издателя должно быть изменено, чтобы оно было уникальным для каждого домена AD FS, которое входит в федерацию с Azure Active Directory. 
 
На сервере AD FS откройте Azure AD PowerShell и выполните следующие действия.
 
Подключитесь к службе Azure Active Directory, которая содержит домен contoso.com (Connect-MsolService). Обновите параметры федерации для contoso.com (Update-MsolFederatedDomain - DomainName contoso.com — SupportMultipleDomain).
 
Издатель в параметре федерации домена будет изменен на http://contoso.com/adfs/services/trust, и для проверяющей стороны Azure AD будет добавлено правило утверждения выдачи, чтобы она выдавала правильный идентификатор издателя на основе суффикса имени участника-пользователя.
 
## <a name="step-3-federate-fabrikamcom-with-ad-fs"></a>Шаг 3. Федерация fabrikam.com с AD FS
 
В сеансе Azure AD PowerShell подключитесь к службе Azure Active Directory, которая содержит домен fabrikam.com.

    Connect-MsolService
Преобразуйте управляемый домен fabrikam.com в федеративный:

    Convert-MsolDomainToFederated -DomainName anandmsft.com -Verbose -SupportMultipleDomain
 
Вышеуказанная операция добавит домен fabrikam.com в федерацию с тем же экземпляром AD FS. Параметры обоих доменов можно проверить с помощью командлета Get-MsolDomainFederationSettings.

## <a name="next-steps"></a>Дополнительная информация
[Подключение Active Directory к Azure Active Directory](active-directory-aadconnect.md)
