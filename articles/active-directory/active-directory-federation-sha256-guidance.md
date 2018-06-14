---
title: Изменение хэш-алгоритма подписи для отношения доверия с проверяющей стороной Office 365 | Документация Майкрософт
description: Эта страница содержит рекомендации по изменению алгоритма SHA для доверия федерации с Office 365
keywords: SHA1,SHA256,O365,федерация,aadconnect,adfs,ad fs,изменение sha,доверие федерации,отношение доверия с проверяющей стороной
services: active-directory
documentationcenter: ''
author: anandyadavmsft
manager: mtillman
editor: ''
ms.assetid: cf6880e2-af78-4cc9-91bc-b64de4428bbd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: anandy
ms.openlocfilehash: ec7eee36888d825d65335db590731103aabbf5c2
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/11/2017
ms.locfileid: "26731151"
---
# <a name="change-signature-hash-algorithm-for-office-365-relying-party-trust"></a>Изменение хэш-алгоритма подписи для отношения доверия с проверяющей стороной Office 365
## <a name="overview"></a>Обзор
Службы федерации Active Directory (AD FS) подписывают маркеры для Microsoft Azure Active Directory, чтобы защитить их от незаконного изменения. Подпись основывается на алгоритме SHA1 или SHA256. Теперь Azure Active Directory поддерживает маркеры, подписанные с помощью алгоритма SHA256. Мы рекомендуем выбирать этот алгоритм подписи маркера для обеспечения максимальной защиты. В этой статье описывается процедура замены алгоритма подписи маркера на алгоритм с более высоким уровнем защиты — SHA256.

>[!NOTE]
>Корпорация Майкрософт рекомендует использовать алгоритм SHA256 для подписания маркеров, так как он защищеннее, чем SHA1, но SHA1 по-прежнему поддерживается.

## <a name="change-the-token-signing-algorithm"></a>Изменение алгоритма подписи маркера
После того как алгоритм подписи будет выбран с помощью одного из указанных ниже способов, службы AD FS будут подписывать маркеры для отношения доверия с проверяющей стороной Office 365 с помощью SHA256. В конфигурацию не требуется вносить никакие дополнительные изменения. Кроме того, это изменение никак не влияет на возможность доступа к Office 365 или другим приложениям Azure AD.

### <a name="ad-fs-management-console"></a>Консоль управления AD FS
1. Откройте консоль управления AD FS на сервере-источнике AD FS.
2. Разверните узел AD FS и выберите **Relying party trusts**(Отношения доверия с проверяющей стороной).
3. Щелкните правой кнопкой мыши нужный объект отношений доверия с проверяющей стороной Office 365 или Azure и выберите пункт **Свойства**.
4. Перейдите на вкладку **Дополнительно** и в поле "Secure hash algorithm" (Алгоритм SHA) выберите значение SHA256.
5. Последовательно выберите **ОК**.

![Алгоритм подписи SHA256 — MMC](./media/active-directory-aadconnectfed-sha256guidance/mmc.png)

### <a name="ad-fs-powershell-cmdlets"></a>Командлеты AD FS PowerShell
1. На любом сервере AD FS откройте PowerShell с правами администратора.
2. Задайте алгоритм SHA с помощью командлета **Set-AdfsRelyingPartyTrust** .
   
   <code>Set-AdfsRelyingPartyTrust -TargetName 'Microsoft Office 365 Identity Platform' -SignatureAlgorithm 'http://www.w3.org/2001/04/xmldsig-more#rsa-sha256'</code>

## <a name="also-read"></a>Также ознакомьтесь
* [Управление службами федерации Active Directory и их настройка с помощью Azure AD Connect](connect/active-directory-aadconnect-federation-management.md#repairthetrust)

