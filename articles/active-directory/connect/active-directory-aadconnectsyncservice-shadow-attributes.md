---
title: Теневые атрибуты службы синхронизации Azure AD Connect | Документация Майкрософт
description: Описание работы теневых атрибутов в службе синхронизации Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: bd1ede2bf8ff642b7be0869e54a6f037b01dd262
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34593418"
---
# <a name="azure-ad-connect-sync-service-shadow-attributes"></a>Теневые атрибуты службы синхронизации Azure AD Connect
Большинство атрибутов представляется в Azure AD так же, как и в локальной службе Active Directory. Однако некоторые атрибуты требуют особого обращения, их значение в Azure AD может отличаться от того, что синхронизирует Azure AD Connect.

## <a name="introducing-shadow-attributes"></a>Общие сведения о теневых атрибутах
Для некоторых атрибутов в Azure AD используются два представления. Для них сохраняются локальное и вычисляемое значения. Эти дополнительные атрибуты называются теневыми. Двумя наиболее распространенными атрибутами из этой категории являются **userPrincipalName** и **proxyAddress**. Изменение значений атрибутов происходит, они представляют непроверенные домены. Но модуль синхронизации в Connect считывает значение в теневом атрибуте, поэтому с его точки зрения атрибут подтвержден Azure AD.

Теневые атрибуты невозможно просмотреть, используя портал Azure или PowerShell. Но понимание принципа помогает при устранении неполадок в определенных сценариях, когда атрибут имеет разные значения в локальной среде и в облаке.

Чтобы лучше понять, как это работает, рассмотрим следующий пример от компании Fabrikam.  
![Домены](./media/active-directory-aadconnectsyncservice-shadow-attributes/domains.png)  
Они используют несколько суффиксов имени участника-пользователя в своей локальной службе Active Directory, но проверен только один из них.

### <a name="userprincipalname"></a>userPrincipalName
У пользователя имеются следующие значения атрибутов в непроверенном домене.

| Атрибут | Значение |
| --- | --- |
| userPrincipalName для локальной среды | lee.sperry@fabrikam.com |
| shadowUserPrincipalName для Azure AD | lee.sperry@fabrikam.com |
| userPrincipalName для Azure AD | lee.sperry@fabrikam.onmicrosoft.com |

Атрибут userPrincipalName — это значение, отображаемое при использовании PowerShell.

Так как реальное локальное значение атрибута хранится в Azure AD, после проверки домена fabrikam.com служба Azure AD обновляет атрибут userPrincipalName значением из shadowUserPrincipalName. Нет необходимости синхронизировать все изменения из Azure AD Connect, чтобы обновлять эти значения.

### <a name="proxyaddresses"></a>proxyAddresses
То же самое, только с учетом проверенных доменов, происходит с proxyAddresses, но с добавлением некоторой логики. Проверка проверенных доменов происходит только для пользователей почтовых ящиков. Пользователь, поддерживающий почту, или контакт представляет пользователя в другой организации Exchange, и вы можете добавить в эти объекты любые значения в proxyAddresses.

Для пользователя почтового ящика, размещенного в локальной среде или Exchange Online, отображаются только значения для проверенных доменов. Это выглядит следующим образом.

| Атрибут | Значение |
| --- | --- |
| proxyAddresses для локальной среды | SMTP:abbie.spencer@fabrikamonline.com</br>smtp:abbie.spencer@fabrikam.com</br>smtp:abbie@fabrikamonline.com |
| proxyAddresses для Exchange Online | SMTP:abbie.spencer@fabrikamonline.com</br>smtp:abbie@fabrikamonline.com</br>SIP:abbie.spencer@fabrikamonline.com |

В этом случае **smtp:abbie.spencer@fabrikam.com** был удален, так как этот домен не проверен. Но служба Exchange также добавила **SIP:abbie.spencer@fabrikamonline.com**. Fabrikam не использовал Lync или Skype локально, но Azure AD и Exchange Online готовы к этому.

Эта логика proxyAddresses называется **ProxyCalc**. ProxyCalc вызывается при каждом изменении пользователя, когда:

- Пользователь назначил план обслуживания, который включает в себя Exchange Online, даже если у пользователя отсутствует лицензия на Exchange. Например, если пользователь назначил номер SKU Office E3, но ему был назначен только номер SKU SharePoint Online. Это верно, даже если ваш почтовый ящик является локальным.
- Атрибут msExchRecipientTypeDetails имеет значение.
- Пользователь изменил proxyAddresses или userPrincipalName.

ProxyCalc может потратить некоторое время на обработку изменения пользователя, не синхронизируясь с процессом экспорта Azure AD Connect.

> [!NOTE]
> Логика ProxyCalc содержит дополнительные режимы для более сложных сценариев, которые не описаны в этом разделе. Этот раздел предоставлен для понимания поведения и не содержит описания всей внутренней логики.

### <a name="quarantined-attribute-values"></a>Значения атрибутов, помещенных в карантин
Теневые атрибуты также используются при наличии повторяющихся значений атрибутов. Дополнительные сведения см. в разделе [Синхронизация удостоверений и устойчивость повторяющихся атрибутов](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md).

## <a name="see-also"></a>См. также
* [Службы синхронизации Azure AD Connect](active-directory-aadconnectsync-whatis.md)
* [Интеграция локальных удостоверений с Azure Active Directory](active-directory-aadconnect.md).
