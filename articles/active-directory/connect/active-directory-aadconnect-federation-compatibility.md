---
title: "Список совместимости с федерацией Azure AD"
description: "На этой странице указаны сторонние поставщики, которые можно использовать для реализации единого входа."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: curtand
ms.assetid: 22c8693e-8915-446d-b383-27e9587988ec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2017
ms.author: billmath
ms.openlocfilehash: 86c247b6a9d197e89040109ac6b8686e3310dbf5
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/11/2017
---
# <a name="azure-ad-federation-compatibility-list"></a>Список совместимости с федерацией Azure AD
Azure Active Directory предоставляет возможность единого входа и обеспечивает повышенную безопасность доступа к приложениям для Office 365 и других служб Microsoft Online Services для гибридных и исключительно облачных реализаций без использования стороннего решения. Набор Office 365, как и большинство служб Microsoft Online Services, интегрируется с Azure Active Directory для служб каталогов, проверки подлинности и авторизации. Azure Active Directory также предоставляет возможность единого входа в тысячи приложений SaaS и локальные веб-приложения. Список поддерживаемых приложений SaaS см. в коллекции приложений Azure Active Directory.

Для организаций, которые вложили средства в сторонние решения федерации, в этом разделе содержатся рекомендации по настройке единого входа для их пользователей Windows Server Active Directory со службами Microsoft Online Services за счет использования сторонних поставщиков удостоверений, входящих в приведенный ниже список совместимости с федерацией Azure Active Directory. 

![](./media/active-directory-aadconnect-federation-compatibility/oxford2.jpg)   
Компания [Oxford Computer Group](http://oxfordcomputergroup.com/) (сторонний подрядчик) от имени корпорация Майкрософт тестировала интерфейсы единого входа с помощью сторонних поставщиков удостоверений в ряде сценариев использования, типичных для Azure Active Directory.

Для получения сведений о том, как добавить в этот список своего стороннего поставщика удостоверений, свяжитесь с Oxford Computer Group по адресу [idp@oxfordcomputergroup.com](mailto:idp@oxfordcomputergroup.com).

> [!IMPORTANT]
> Oxford Computer Group тестировала только функциональность федерации этих сценариев единого входа. Тестирование синхронизации, двухфакторной проверки подлинности и других компонентов сценариев единого входа не проводилось.
> 
> Использование входа в систему с помощью альтернативного идентификатора в UPN также не было проверено в этой программе.
> 
> 

* [Azure Active Directory](#azure-active-directory)
* [AuthAnvil Single Sign On 4.5](#authanvil-single-sign-on-45)
* [BIG-IP с Access Policy Manager BIG-IP версий 11.3x–11.6x](#big-ip-with-access-policy-manager-big-ip-ver-113x--116x) 
* [BitGlass](#bitglass)
* [CA Secure Cloud](#ca-secure-cloud) 
* [CA SiteMinder 12.52](#ca-siteminder-1252-sp1-cumulative-release-4) 
* [Centrify](#centrify) 
* [Dell One Identity Cloud Access Manager версии 7.1](#dell-one-identity-cloud-access-manager-v71) 
* [Составная аутентификация DigitalPersona](#digitalpersona-composite-authentication)
* [ForgeRock Identity Platform Access Management версии 5.x](#forgerock-identity-platform-access-management-v5x)
* [IBM Tivoli Federated Identity Manager 6.2.2](#ibm-tivoli-federated-identity-manager-622) 
* [IceWall Federation версии 3.0](#icewall-federation-version-30) 
* [Memority](#memority)
* [NetIQ Access Manager 4.x](#netiq-access-manager-4x) 
* [Okta](#okta) 
* [OneLogin](#onelogin) 
* [Службы федерации Optimal IDM Virtual Identity Server](#optimal-idm-virtual-identity-server-federation-services) 
* [PingFederate 6.11, 7.2, 8.x](#pingfederate-611-72-8x)
* [RadiantOne CFS 3.0](#radiantone-cfs-30) 
* [Sailpoint IdentityNow](#sailpoint-identitynow)
* [SecureAuth IdP 7.2.0](#secureauth-idp-720) 
* [Sign&go 5.3](#signgo-53) 
* [Шлюз веб-службы "Технология SoftBank"](#softbank)
* [Рабочая область 1 VMware](#vmware-workspace-one)



> [!IMPORTANT]
> Поскольку это сторонние продукты, Майкрософт не предоставляет поддержку по развертыванию, конфигурации, устранению неполадок, рекомендациям, а также другим проблемам и вопросам, касающимся этих поставщиков удостоверений. Для получения поддержки и по вопросам, касающимся этих поставщиков удостоверений, обращайтесь непосредственно к ним.
> 
> Эти сторонние поставщики удостоверений тестировались на взаимодействие с облачными службами Майкрософт только с использованием протоколов WS-Federation и WS-Trust. При тестировании не применялся протокол SAML.
> 


## <a name="azure-active-directory"></a>Azure Active Directory

Далее приводится таблица поддержки сценария реализации единого входа: 

| Клиент | Поддержка | Исключения |
| --- | --- | --- |
| Веб-клиенты, такие как Exchange Web Access и SharePoint Online |Поддерживаются |None |
| Полнофункциональные клиентские приложения, такие как Lync, подписка Office Subscription, CRM |Поддерживаются |None |
| Полнофункциональные почтовые клиенты, такие как Outlook и ActiveSync |Поддерживаются |None |
| Современные приложения, использующие ADAL, такие как Office 2016 |Поддерживаются |Нет |

Дополнительные сведения об использовании Azure Active Directory с AD FS см. в разделе [Настройка федерации с AD FS](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs).

Дополнительные сведения об использовании Azure Active Directory с синхронизацией паролей см. в статье [Интеграция локальных удостоверений с Azure Active Directory](active-directory-aadconnect.md).

## <a name="authanvil-single-sign-on-45"></a>AuthAnvil Single Sign On 4.5

Далее приводится таблица поддержки сценария реализации единого входа:

| Клиент | Поддержка | Исключения |
| --- | --- | --- |
| Веб-клиенты, такие как Exchange Web Access и SharePoint Online |Поддерживаются |Встроенная проверка подлинности Windows не поддерживается. |
| Полнофункциональные клиентские приложения, такие как Lync, подписка Office Subscription, CRM |Поддерживаются |Встроенная проверка подлинности Windows не поддерживается. |
| Полнофункциональные почтовые клиенты, такие как Outlook и ActiveSync |Поддерживаются |Нет |

Дополнительные сведения см. на сайте AuthAnvil в [разделе, посвященном настройке единого входа](https://help.scorpionsoft.com/entries/26538603-How-can-I-Configure-Single-Sign-On-for-Office-365-).


## <a name="big-ip-with-access-policy-manager-big-ip-ver-113x--116x"></a>BIG-IP с Access Policy Manager BIG-IP версий 11.3x–11.6x

Далее приводится таблица поддержки сценария реализации единого входа: 

| Клиент | Поддержка | Исключения |
| --- | --- | --- |
| Веб-клиенты, такие как Exchange Web Access и SharePoint Online |Поддерживаются |Нет |
| Полнофункциональные клиентские приложения, такие как Lync, подписка Office Subscription, CRM |Не поддерживается |Не поддерживается |
| Полнофункциональные почтовые клиенты, такие как Outlook и ActiveSync |Поддерживаются |Нет |

Дополнительные сведения о BIG-IP Access Policy Manager см. на сайте компании F5 Networks в разделе [BIG-IP Access Policy Manager](https://f5.com/products/modules/access-policy-manager). 

Чтобы получить инструкции BIG-IP Access Policy Manager по настройке службы маркеров безопасности для предоставления функции единого входа пользователям Active Directory, скачайте PDF-файл [BIG-IP](http://www.f5.com/pdf/deployment-guides/microsoft-office-365-idp-dg.pdf).

## <a name="bitglass"></a>BitGlass

Далее приводится таблица поддержки сценария реализации единого входа:

| Клиент | Поддержка | Исключения |
| --- | --- | --- |
| Веб-клиенты, такие как Exchange Web Access и SharePoint Online |Поддерживаются |Встроенная проверка подлинности Windows не поддерживается. |
| Полнофункциональные клиентские приложения, такие как Lync, подписка Office Subscription, CRM |Поддерживаются |Встроенная проверка подлинности Windows не поддерживается. |
| Полнофункциональные почтовые клиенты, такие как Outlook и ActiveSync |Поддерживаются |None |

Дополнительные сведения о BitGlass см. на веб-сайте [BitGlass](http://www.bitglass.com).

## <a name="ca-secure-cloud"></a>CA Secure Cloud

Далее приводится таблица поддержки сценария реализации единого входа:

| Клиент | Поддержка | Исключения |
| --- | --- | --- |
| Веб-клиенты, такие как Exchange Web Access и SharePoint Online |Поддерживаются |Встроенная проверка подлинности Windows не поддерживается. |
| Полнофункциональные клиентские приложения, такие как Lync, подписка Office Subscription, CRM |Поддерживаются |Встроенная проверка подлинности Windows не поддерживается. |
| Полнофункциональные почтовые клиенты, такие как Outlook и ActiveSync |Поддерживаются |None |

Дополнительные сведения о CA Secure Cloud см. на [сайте компании CA](http://www.ca.com/us/products/security-as-a-service.aspx).

## <a name="ca-siteminder-1252-sp1-cumulative-release-4"></a>CA SiteMinder 12.52 с пакетом обновления 1 (SP1), накопительный выпуск 4

Далее приводится таблица поддержки сценария реализации единого входа: 

| Клиент | Поддержка | Исключения |
| --- | --- | --- |
| Веб-клиенты, такие как Exchange Web Access и SharePoint Online |Поддерживаются |None |
| Полнофункциональные клиентские приложения, такие как Lync, подписка Office Subscription, CRM |Поддерживаются |Нет |
| Полнофункциональные почтовые клиенты, такие как Outlook и ActiveSync |Поддерживаются |Нет |

Дополнительные сведения о CA SiteMinder см. на [сайте компании CA](http://www.ca.com/us/products/ca-single-sign-on.html). 

## <a name="centrify"></a>Centrify

Далее приводится таблица поддержки сценария реализации единого входа:

| Клиент | Поддержка | Исключения |
| --- | --- | --- |
| Веб-клиенты, такие как Exchange Web Access и SharePoint Online |Поддерживаются |Нет |
| Полнофункциональные клиентские приложения, такие как Lync, подписка Office Subscription, CRM |Поддерживаются |None |
| Полнофункциональные почтовые клиенты, такие как Outlook и ActiveSync |Поддерживаются |Контроль доступа клиентов не поддерживается. |

Дополнительные сведения о Centrify см. [на сайте Centrify](http://www.centrify.com/cloud/apps/single-sign-on-for-office-365.asp).

## <a name="dell-one-identity-cloud-access-manager-v71"></a>Dell One Identity Cloud Access Manager версии 7.1

Далее приводится таблица поддержки сценария реализации единого входа:

| Клиент | Поддержка | Исключения |
| --- | --- | --- |
| Веб-клиенты, такие как Exchange Web Access и SharePoint Online |Поддерживаются |Нет |
| Полнофункциональные клиентские приложения, такие как Lync, подписка Office Subscription, CRM |Поддерживаются |Нет |
| Полнофункциональные почтовые клиенты, такие как Outlook и ActiveSync |Поддерживаются |Нет |

Дополнительные сведения о Dell One Identity Cloud Access Manager см. на сайте компании Dell в разделе [Cloud Access Manager](http://software.dell.com/products/cloud-access-manager).

 Инструкции по настройке службы токенов безопасности для предоставления интерфейса единого входа пользователям Office 365 см. на сайте компании Dell в разделе, посвященном [настройке Microsoft Office 365](http://documents.software.dell.com/dell-one-identity-cloud-access-manager/7.1/how-to-configure-microsoft-office-365). 

## <a name="digitalpersona-composite-authentication"></a>Составная аутентификация DigitalPersona  

Далее приводится таблица поддержки сценария реализации единого входа:

| Клиент | Поддержка | Исключения |
| --- | --- | --- |
| Веб-клиенты, такие как Exchange Web Access и SharePoint Online |Поддерживаются |Встроенная проверка подлинности Windows не поддерживается.|
| Полнофункциональные клиентские приложения, такие как Lync, подписка Office Subscription, CRM |Поддерживаются |Встроенная проверка подлинности Windows не поддерживается.|
| Полнофункциональные почтовые клиенты, такие как Outlook и ActiveSync |Поддерживаются |None |

Дополнительные сведения см. в документе [DigitalPersona Composite Authentication](http://www.crossmatch.com/uploadedFiles/Support/Reference_Material/DigitalPersona-Office-365-Deployment-Guide.pdf) (Составная аутентификация DigitalPersona).

## <a name="forgerock-identity-platform-access-management-v5x"></a>ForgeRock Identity Platform Access Management версии 5.x

Далее приводится таблица поддержки сценария реализации единого входа:

| Клиент | Поддержка | Исключения |
| --- | --- | --- |
| Веб-клиенты, такие как Exchange Web Access и SharePoint Online |Поддерживаются |None|
| Полнофункциональные клиентские приложения, такие как Lync, подписка Office Subscription, CRM |Поддерживаются |Нет|
| Полнофункциональные почтовые клиенты, такие как Outlook и ActiveSync |Поддерживаются |Нет |

Дополнительные сведения см. на странице о [ForgeRock Identity Platform Access Management версии 5.x](https://backstage.forgerock.com/knowledge/kb/article/a98278517).

## <a name="ibm-tivoli-federated-identity-manager-622"></a>IBM Tivoli Federated Identity Manager 6.2.2

Далее приводится таблица поддержки сценария реализации единого входа: 

| Клиент | Поддержка | Исключения |
| --- | --- | --- |
| Веб-клиенты, такие как Exchange Web Access и SharePoint Online |Поддерживаются |Нет |
| Полнофункциональные клиентские приложения, такие как Lync, подписка Office Subscription, CRM |Поддерживаются |Нет |
| Полнофункциональные почтовые клиенты, такие как Outlook и ActiveSync |Поддерживаются |Нет |

Дополнительные сведения об IBM Tivoli Federated Identity Manager см. на сайте IBM в разделе, посвященном [IBM Security Access Manager для приложений Майкрософт](http://www-01.ibm.com/support/docview.wss?uid=swg24029517).

## <a name="icewall-federation-version-30"></a>IceWall Federation версии 3.0

Далее приводится таблица поддержки сценария реализации единого входа:

| Клиент | Поддержка | Исключения |
| --- | --- | --- |
| Веб-клиенты, такие как Exchange Web Access и SharePoint Online |Поддерживаются |Встроенная проверка подлинности Windows не поддерживается. |
| Полнофункциональные клиентские приложения, такие как Lync, подписка Office Subscription, CRM |Поддерживаются |Встроенная проверка подлинности Windows не поддерживается. |
| Полнофункциональные почтовые клиенты, такие как Outlook и ActiveSync |Поддерживаются |Нет |

Дополнительные сведения об IceWall Federation см. на сайте компании HPE в разделах, посвященных [IceWall Federation версии 3.0](http://h50146.www5.hp.com/products/software/security/icewall/eng/federation/) и [IceWall Federation с Office 365](http://h50146.www5.hp.com/products/software/security/icewall/federation/office365.html).

## <a name="memority"></a>Memority

Далее приводится таблица поддержки сценария реализации единого входа: 

| Клиент | Поддержка | Исключения |
| --- | --- | --- |
| Веб-клиенты, такие как Exchange Web Access и SharePoint Online |Поддерживаются |Нет |
| Полнофункциональные клиентские приложения, такие как Lync, подписка Office Subscription, CRM |Поддерживаются |Нет |
| Полнофункциональные почтовые клиенты, такие как Outlook и ActiveSync |Поддерживаются |Нет |

Дополнительные сведения об использовании Memority см. на веб-сайте [Memority](http://www.memority.com).


## <a name="netiq-access-manager-4x"></a>NetIQ Access Manager 4.x

Далее приводится таблица поддержки сценария реализации единого входа:

| Клиент | Поддержка | Исключения |
| --- | --- | --- |
| Веб-клиенты, такие как Exchange Web Access и SharePoint Online |Поддерживаются |None|
| Полнофункциональные клиентские приложения, такие как Lync, подписка Office Subscription, CRM |Поддерживаются |None|
| Полнофункциональные почтовые клиенты, такие как Outlook и ActiveSync |Поддерживаются |Нет |

Дополнительные сведения о NetIQ Access Manager см. в [соответствующем разделе на сайте NetIQ](https://www.netiq.com/documentation/access-manager-43/admin/data/b65ogn0.html#b12iqp0m).

## <a name="okta"></a>Okta

Далее приводится таблица поддержки сценария реализации единого входа: 

| Клиент | Поддержка | Исключения |
| --- | --- | --- |
| Веб-клиенты, такие как Exchange Web Access и SharePoint Online |Поддерживаются |Для встроенной проверки подлинности Windows требуется настройка дополнительного веб-сервера и приложения Okta. |
| Полнофункциональные клиентские приложения, такие как Lync, подписка Office Subscription, CRM |Поддерживаются |Встроенная проверка подлинности Windows |
| Полнофункциональные почтовые клиенты, такие как Outlook и ActiveSync |Поддерживаются |Нет |

Дополнительные сведения об Okta см. на веб-сайте [Okta](https://www.okta.com/).

## <a name="onelogin"></a>OneLogin

Далее приводится таблица поддержки сценария реализации единого входа: 

| Клиент | Поддержка | Исключения |
| --- | --- | --- |
| Веб-клиенты, такие как Exchange Web Access и SharePoint Online |Поддерживаются |Встроенная проверка подлинности Windows |
| Полнофункциональные клиентские приложения, такие как Lync, подписка Office Subscription, CRM |Поддерживаются |Встроенная проверка подлинности Windows |
| Полнофункциональные почтовые клиенты, такие как Outlook и ActiveSync |Поддерживаются |None |

Дополнительные сведения об OneLogin см. на веб-сайте [OneLogin](https://www.onelogin.com/).

## <a name="optimal-idm-virtual-identity-server-federation-services"></a>Службы федерации Optimal IDM Virtual Identity Server

Далее приводится таблица поддержки сценария реализации единого входа.

| Клиент | Поддержка | Исключения |
| --- | --- | --- |
| Веб-клиенты, такие как Exchange Web Access и SharePoint Online |Поддерживаются |Нет |
| Полнофункциональные клиентские приложения, такие как Lync, подписка Office Subscription, CRM |Поддерживаются |Встроенная проверка подлинности Windows |
| Полнофункциональные почтовые клиенты, такие как Outlook и ActiveSync |Поддерживаются |

Дополнительные сведения о политиках клиентского доступа см. в статье [Limiting Access to Office 365 Services Based on the Location of the Client](https://technet.microsoft.com/library/hh526961.aspx) (Ограниченный доступ к службам Office 365 на основе расположения клиента).





## <a name="pingfederate-611-72-8x"></a>PingFederate 6.11, 7.2, 8.x

Далее приводится таблица поддержки сценария реализации единого входа:

| Клиент | Поддержка | Исключения |
| --- | --- | --- |
| Веб-клиенты, такие как Exchange Web Access и SharePoint Online |Поддерживаются |Нет |
| Полнофункциональные клиентские приложения, такие как Lync, подписка Office Subscription, CRM |Поддерживаются |Нет |
| Полнофункциональные почтовые клиенты, такие как Outlook и ActiveSync |Поддерживаются |Нет |

Инструкции PingFederate по настройке службы токенов безопасности для предоставления интерфейса единого входа пользователям Active Directory см. в одной из следующих статей: 

- [PingFederate 6.11](http://go.microsoft.com/fwlink/?LinkID=266321)
- [PingFederate 7.2](http://documentation.pingidentity.com/display/PF72/PingFederate+7.2)
- [PingFederate 8.x](http://documentation.pingidentity.com/display/PFS/SSO+to+Office+365+Introduction)

## <a name="radiantone-cfs-30"></a>RadiantOne CFS 3.0

Далее приводится таблица поддержки сценария реализации единого входа: 

| Клиент | Поддержка | Исключения |
| --- | --- | --- |
| Веб-клиенты, такие как Exchange Web Access и SharePoint Online |Поддерживаются |Нет |
| Полнофункциональные клиентские приложения, такие как Lync, подписка Office Subscription, CRM |Поддерживаются |Встроенная проверка подлинности Windows |
| Полнофункциональные почтовые клиенты, такие как Outlook и ActiveSync |Поддерживаются |None |

Дополнительные сведения о RadiantOne CFS см. [в соответствующем разделе](http://www.radiantlogic.com/products/radiantone-cfs/) веб-сайта компании Radiant Logic .

## <a name="sailpoint-identitynow"></a>Sailpoint IdentityNow

Далее приводится таблица поддержки сценария реализации единого входа:

| Клиент | Поддержка | Исключения |
| --- | --- | --- |
| Веб-клиенты, такие как Exchange Web Access и SharePoint Online |Поддерживаются |Встроенная проверка подлинности Windows не поддерживается. |
| Полнофункциональные клиентские приложения, такие как Lync, подписка Office Subscription, CRM |Поддерживаются |Встроенная проверка подлинности Windows не поддерживается. |
| Полнофункциональные почтовые клиенты, такие как Outlook и ActiveSync |Поддерживаются |Нет |

Дополнительные сведения о Sailpoint IdentityNow см. [в соответствующем разделе](https://www.sailpoint.com/idaas-identity-as-a-service-identitynow/) веб-сайта компании Sailpoint.

## <a name="secureauth-idp-720"></a>SecureAuth IdP 7.2.0

Далее приводится таблица поддержки сценария реализации единого входа: 

| Клиент | Поддержка | Исключения |
| --- | --- | --- |
| Веб-клиенты, такие как Exchange Web Access и SharePoint Online |Поддерживаются |Нет |
| Полнофункциональные клиентские приложения, такие как Lync, подписка Office Subscription, CRM |Поддерживаются |Нет |
| Полнофункциональные почтовые клиенты, такие как Outlook и ActiveSync |Поддерживаются |Нет |

Дополнительные сведения о SecureAuth см. на [сайте компании SecureAuth](http://go.microsoft.com/?linkid=9845293).














## <a name="signgo-53"></a>Sign&go 5.3

Далее приводится таблица поддержки сценария реализации единого входа:

| Клиент | Поддержка | Исключения |
| --- | --- | --- |
| Веб-клиенты, такие как Exchange Web Access и SharePoint Online |Поддерживаются |Поддерживаются контракты Kerberos |
| Полнофункциональные клиентские приложения, такие как Lync, подписка Office Subscription, CRM |Поддерживаются |Нет |
| Полнофункциональные почтовые клиенты, такие как Outlook и ActiveSync |Поддерживаются |Нет |

Sign&go 5.3 поддерживает проверку подлинности Kerberos в конфигурации контракта Kerberos.  Если вам требуется помощь в настройке, обратитесь к Ilex или просмотрите руководство по настройке [Sign&go](http://www.ilex-international.com/docs/sign&go_wsfederation_en.pdf)

## <a name="softbank-technology-online-service-gate"></a>Шлюз веб-службы "Технология SoftBank"

Далее приводится таблица поддержки сценария реализации единого входа:

| Клиент | Поддержка | Исключения |
| --- | --- | --- |
| Веб-клиенты, такие как Exchange Web Access и SharePoint Online |Поддерживаются |Встроенная проверка подлинности Windows не поддерживается. |
| Полнофункциональные клиентские приложения, такие как Lync, подписка Office Subscription, CRM |Поддерживаются |Встроенная проверка подлинности Windows не поддерживается. |
| Полнофункциональные почтовые клиенты, такие как Outlook и ActiveSync |Поддерживаются |None |

Дополнительные сведения о шлюзе веб-службы "Технология SoftBank" см. на веб-сайте [Softbank](https://www.softbanktech.jp/service/list/osg-pro-ent/)

## <a name="vmware-workspace-one"></a>Рабочая область 1 VMware

Далее приводится таблица поддержки сценария реализации единого входа:

| Клиент | Поддержка | Исключения |
| --- | --- | --- |
| Веб-клиенты, такие как Exchange Web Access и SharePoint Online |Поддерживаются |Встроенная проверка подлинности Windows не поддерживается. |
| Полнофункциональные клиентские приложения, такие как Lync, подписка Office Subscription, CRM |Поддерживаются |Встроенная проверка подлинности Windows не поддерживается. |
| Полнофункциональные почтовые клиенты, такие как Outlook и ActiveSync |Поддерживаются |Нет |

Дополнительные сведения см. в документе [VMware Workspace One](http://www.vmware.com/pdf/vidm-office365-saml.pdf)

