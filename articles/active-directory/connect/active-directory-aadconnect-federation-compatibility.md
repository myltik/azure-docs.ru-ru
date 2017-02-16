---
title: "Список совместимости с федерацией Azure AD"
description: "На этой странице указаны сторонние поставщики, которые можно использовать для реализации единого входа."
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: curtand
ms.assetid: 22c8693e-8915-446d-b383-27e9587988ec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: cb06c5bd74816777ec7c5836d362d509a265bd9c
ms.openlocfilehash: 9e16fd3112b093f22ed99829c223a3a80e008f28


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
* [Службы федерации Optimal IDM Virtual Identity Server](#optimal-idm-virtual-identity-server-federation-services) 
* [PingFederate 6.11](#pingfederate-611) 
* [PingFederate 7.2](#pingfederate-72) 
* [PingFederate 8.x](#pingfederate-8x)
* [Centrify](#centrify) 
* [IBM Tivoli Federated Identity Manager 6.2.2](#ibm-tivoli-federated-identity-manager-622) 
* [SecureAuth IdP 7.2.0](#secureauth-idp-720) 
* [CA SiteMinder 12.52](#ca-siteminder-1252-sp1-cumulative-release-4) 
* [RadiantOne CFS 3.0](#radiantone-cfs-30) 
* [Okta](#okta) 
* [OneLogin](#onelogin) 
* [NetIQ Access Manager 4.0.1](#netiq-access-manager-401) 
* [BIG-IP с Access Policy Manager BIG-IP версий 11.3x–11.6x](#big-ip-with-access-policy-manager-big-ip-ver-113x-116x) 
* [Портал VMware Workspace версии 2.1](#vmware-workspace-portal-version-21) 
* [Sign&go 5.3](#signampgo-53) 
* [IceWall Federation версии 3.0](#icewall-federation-version-30) 
* [CA Secure Cloud](#ca-secure-cloud) 
* [Dell One Identity Cloud Access Manager версии&7;.1](#dell-one-identity-cloud-access-manager-v71) 
* [AuthAnvil Single Sign On 4.5](#authavil-single-sign-on-45)
* [Sailpoint IdentityNow](#sailpoint-identitynow) 

> [!IMPORTANT]
> Поскольку это сторонние продукты, Майкрософт не предоставляет поддержку по развертыванию, конфигурации, устранению неполадок, рекомендациям, а также другим проблемам и вопросам, касающимся этих поставщиков удостоверений. Для получения поддержки и по вопросам, касающимся этих поставщиков удостоверений, обращайтесь непосредственно к ним.
> 
> Эти сторонние поставщики удостоверений тестировались на взаимодействие с облачными службами Майкрософт только с использованием протоколов WS-Federation и WS-Trust. При тестировании не применялся протокол SAML.
> 
> 

## <a name="azure-active-directory"></a>Azure Active Directory
Azure Active Directory может проверять подлинность пользователей путем федерации локального сервера Active Directory или без локального севера федерации путем использования синхронизации паролей. 

Далее приводится таблица поддержки сценария реализации единого входа: 

| Клиент | Поддержка | Исключения |
| --- | --- | --- |
| Веб-клиенты, такие как Exchange Web Access и SharePoint Online |Поддерживаются |None |
| Полнофункциональные клиентские приложения, такие как Lync, подписка Office Subscription, CRM |Поддерживаются |None |
| Полнофункциональные почтовые клиенты, такие как Outlook и ActiveSync |Поддерживаются |None |
| Современные приложения, использующие ADAL, такие как Office 2016 |Поддерживаются |None |

Дополнительные сведения об использовании Azure Active Directory с AD FS см. в разделе [Настройка федерации с AD FS](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs).

Дополнительные сведения об использовании Azure Active Directory с синхронизацией паролей см. в статье [Интеграция локальных удостоверений с Azure Active Directory](active-directory-aadconnect.md).

## <a name="optimal-idm-virtual-identity-server-federation-services"></a>Службы федерации Optimal IDM Virtual Identity Server
Службы федерации Optimal IDM Virtual Identity Server могут выполнять проверку подлинности пользователей, размещенных в локальных службах Active Directory клиентов.

Далее приводится таблица поддержки сценария реализации единого входа.

| Клиент | Поддержка | Исключения |
| --- | --- | --- |
| Веб-клиенты, такие как Exchange Web Access и SharePoint Online |Поддерживаются |None |
| Полнофункциональные клиентские приложения, такие как Lync, подписка Office Subscription, CRM |Поддерживаются |Встроенная проверка подлинности Windows |
| Полнофункциональные почтовые клиенты, такие как Outlook и ActiveSync |Поддерживаются |Дополнительные сведения о политиках клиентского доступа см. в статье [Limiting Access to Office 365 Services Based on the Location of the Client](https://technet.microsoft.com/library/hh526961.aspx) (Ограниченный доступ к службам Office 365 на основе расположения клиента). |

## <a name="pingfederate-611"></a>PingFederate 6.11
PingFederate 6.11 реализует широко использующийся стандарт удостоверений WS Federation для предоставления инфраструктуры единого входа и платформы обмена атрибутами.

Далее приводится таблица поддержки сценария реализации единого входа:

| Клиент | Поддержка | Исключения |
| --- | --- | --- |
| Веб-клиенты, такие как Exchange Web Access и SharePoint Online |Поддерживаются |None |
| Полнофункциональные клиентские приложения, такие как Lync, подписка Office Subscription, CRM |Поддерживаются |Нет (необходимо обновить более ранние версии до 6.11) |
| Полнофункциональные почтовые клиенты, такие как Outlook и ActiveSync |Поддерживаются |None |

Чтобы получить инструкции PingFederate по настройке службы маркеров безопасности для предоставления интерфейса единого входа пользователям Active Directory, скачайте PDF-файл [здесь](http://go.microsoft.com/fwlink/?LinkID=266321)

## <a name="pingfederate-72"></a>PingFederate 7.2
PingFederate 7.2 реализует широко использующийся стандарт удостоверений WS Federation/WS-Trust для предоставления инфраструктуры единого входа и платформы обмена атрибутами.

Далее приводится таблица поддержки сценария реализации единого входа:

| Клиент | Поддержка | Исключения |
| --- | --- | --- |
| Веб-клиенты, такие как Exchange Web Access и SharePoint Online |Поддерживаются |None |
| Полнофункциональные клиентские приложения, такие как Lync, подписка Office Subscription, CRM |Поддерживаются |None |
| Полнофункциональные почтовые клиенты, такие как Outlook и ActiveSync |Поддерживаются |None |

Инструкции PingFederate по настройке службы маркеров безопасности для предоставления интерфейса единого входа пользователям Active Directory см. [здесь](http://documentation.pingidentity.com/display/PF72/PingFederate+7.2).

## <a name="pingfederate-8x"></a>PingFederate 8.x
PingFederate 8.x реализует широко используемый стандарт удостоверений WS Federation/WS-Trust для предоставления инфраструктуры единого входа и платформы обмена атрибутами.

Далее приводится таблица поддержки сценария реализации единого входа:

| Клиент | Поддержка | Исключения |
| --- | --- | --- |
| Веб-клиенты, такие как Exchange Web Access и SharePoint Online |Поддерживаются |None |
| Полнофункциональные клиентские приложения, такие как Lync, подписка Office Subscription, CRM |Поддерживаются |None |
| Полнофункциональные почтовые клиенты, такие как Outlook и ActiveSync |Поддерживаются |None |

Инструкции PingFederate по настройке службы маркеров безопасности для предоставления интерфейса единого входа пользователям Active Directory см. [здесь](http://documentation.pingidentity.com/display/PFS/SSO+to+Office+365+Introduction).

## <a name="centrify"></a>Centrify
Centrify помогает обеспечить интерфейс единого входа для Office 365 без требования размещения локального сервера федерации.

Далее приводится таблица поддержки сценария реализации единого входа:

| Клиент | Поддержка | Исключения |
| --- | --- | --- |
| Веб-клиенты, такие как Exchange Web Access и SharePoint Online |Поддерживаются |None |
| Полнофункциональные клиентские приложения, такие как Lync, подписка Office Subscription, CRM |Поддерживаются |None |
| Полнофункциональные почтовые клиенты, такие как Outlook и ActiveSync |Поддерживаются |Контроль доступа клиентов не поддерживается. |

Дополнительные сведения о Centrify см. [здесь](http://www.centrify.com/cloud/apps/single-sign-on-for-office-365.asp).|

## <a name="ibm-tivoli-federated-identity-manager-622"></a>IBM Tivoli Federated Identity Manager 6.2.2
IBM Tivoli Federated Identity Manager 6.2.2 с IBM Security Access Manager для Microsoft Applications 1.4 реализует широко использующийся стандарт удостоверений WS Federation/WS-Trust для предоставления инфраструктуры единого входа и платформы обмена атрибутами.

Далее приводится таблица поддержки сценария реализации единого входа: 

| Клиент | Поддержка | Исключения |
| --- | --- | --- |
| Веб-клиенты, такие как Exchange Web Access и SharePoint Online |Поддерживаются |None |
| Полнофункциональные клиентские приложения, такие как Lync, подписка Office Subscription, CRM |Поддерживаются |None |
| Полнофункциональные почтовые клиенты, такие как Outlook и ActiveSync |Поддерживаются |None |

Дополнительные сведения об IBM Tivoli Federated Identity Manager см. на сайте IBM в разделе [IBM Security Access Manager for Microsoft Applications](http://www-01.ibm.com/support/docview.wss?uid=swg24029517) (IBM Security Access Manager для приложений Майкрософт).

## <a name="secureauth-idp-720"></a>SecureAuth IdP 7.2.0
SecureAuth IdP 7.2.0 реализует широко использующийся стандарт удостоверений WS Federation/WS-Trust для предоставления инфраструктуры единого входа и платформы обмена атрибутами.

Далее приводится таблица поддержки сценария реализации единого входа: 

| Клиент | Поддержка | Исключения |
| --- | --- | --- |
| Веб-клиенты, такие как Exchange Web Access и SharePoint Online |Поддерживаются |None |
| Полнофункциональные клиентские приложения, такие как Lync, подписка Office Subscription, CRM |Поддерживаются |None |
| Полнофункциональные почтовые клиенты, такие как Outlook и ActiveSync |Поддерживаются |None |

Дополнительные сведения о SecureAuth см. на [сайте компании SecureAuth](http://go.microsoft.com/?linkid=9845293).

## <a name="ca-siteminder-1252-sp1-cumulative-release-4"></a>CA SiteMinder 12.52 с пакетом обновления 1 (SP1), накопительный выпуск 4
CA SiteMinder Federation 12.52 реализует широко использующийся стандарт удостоверений WS Federation/WS-Trust для предоставления инфраструктуры единого входа и платформы обмена атрибутами.

Далее приводится таблица поддержки сценария реализации единого входа: 

| Клиент | Поддержка | Исключения |
| --- | --- | --- |
| Веб-клиенты, такие как Exchange Web Access и SharePoint Online |Поддерживаются |None |
| Полнофункциональные клиентские приложения, такие как Lync, подписка Office Subscription, CRM |Поддерживаются |None |
| Полнофункциональные почтовые клиенты, такие как Outlook и ActiveSync |Поддерживаются |None |

Дополнительные сведения о CA SiteMinder см. на [сайте компании CA](http://www.ca.com/us/products/ca-single-sign-on.html). 

## <a name="radiantone-cfs-30"></a>RadiantOne CFS 3.0
RadiantOne Cloud Federation Service (CFS) 3.0 реализует широко использующийся стандарт удостоверений WS Federation/WS-Trust для предоставления инфраструктуры единого входа и платформы обмена атрибутами.

Далее приводится таблица поддержки сценария реализации единого входа: 

| Клиент | Поддержка | Исключения |
| --- | --- | --- |
| Веб-клиенты, такие как Exchange Web Access и SharePoint Online |Поддерживаются |None |
| Полнофункциональные клиентские приложения, такие как Lync, подписка Office Subscription, CRM |Поддерживаются |Встроенная проверка подлинности Windows |
| Полнофункциональные почтовые клиенты, такие как Outlook и ActiveSync |Поддерживаются |None |

Дополнительные сведения о RadiantOne CFS см. на [сайте компании Radiant Logic](http://www.radiantlogic.com/products/radiantone-cfs/).

## <a name="okta"></a>Okta
Okta реализует широко использующийся стандарт удостоверений WS Federation/WS-Trust для предоставления инфраструктуры единого входа и платформы обмена атрибутами.

Далее приводится таблица поддержки сценария реализации единого входа: 

| Клиент | Поддержка | Исключения |
| --- | --- | --- |
| Веб-клиенты, такие как Exchange Web Access и SharePoint Online |Поддерживаются |Для встроенной проверки подлинности Windows требуется настройка дополнительного веб-сервера и приложения Okta. |
| Полнофункциональные клиентские приложения, такие как Lync, подписка Office Subscription, CRM |Поддерживаются |Встроенная проверка подлинности Windows |
| Полнофункциональные почтовые клиенты, такие как Outlook и ActiveSync |Поддерживаются |None |

Дополнительные сведения об Okta см. на [сайте компании Okta](https://www.okta.com/).

## <a name="onelogin"></a>OneLogin
OneLogin, согласно тестированию в мае 2014 г., реализует широко использующийся стандарт удостоверений WS Federation/WS-Trust для предоставления инфраструктуры единого входа и платформы обмена атрибутами.

Далее приводится таблица поддержки сценария реализации единого входа: 

| Клиент | Поддержка | Исключения |
| --- | --- | --- |
| Веб-клиенты, такие как Exchange Web Access и SharePoint Online |Поддерживаются |Встроенная проверка подлинности Windows |
| Полнофункциональные клиентские приложения, такие как Lync, подписка Office Subscription, CRM |Поддерживаются |Встроенная проверка подлинности Windows |
| Полнофункциональные почтовые клиенты, такие как Outlook и ActiveSync |Поддерживаются |None |

Дополнительные сведения о OneLogin см. на [сайте компании OneLogin](https://www.onelogin.com/).

## <a name="netiq-access-manager-401"></a>NetIQ Access Manager 4.0.1
NetIQ Access Manager 4.0.1 реализует широко использующийся стандарт удостоверений WS Federation/WS-Trust для предоставления инфраструктуры единого входа и платформы обмена атрибутами.

Далее приводится таблица поддержки сценария реализации единого входа:

| Клиент | Поддержка | Исключения |
| --- | --- | --- |
| Веб-клиенты, такие как Exchange Web Access и SharePoint Online |Поддерживаются |*Поддерживаются контракты Kerberos |
| Полнофункциональные клиентские приложения, такие как Lync, подписка Office Subscription, CRM |Поддерживаются |Встроенная проверка подлинности Windows не поддерживается. |
| Полнофункциональные почтовые клиенты, такие как Outlook и ActiveSync |Поддерживаются |None |

*NetIQ поддерживает проверку подлинности Kerberos в конфигурации контракта Kerberos.  За помощью в настройке обратитесь к NetIQ или просмотрите руководство по настройке. Дополнительные сведения о NetIQ Access Manager см. на сайте NetIQ в разделе [NetIQ Access Manager](https://www.netiq.com/documentation/netiqaccessmanager4/identityserverhelp/data/b12iqp0m.html).

## <a name="big-ip-with-access-policy-manager-big-ip-ver-113x--116x"></a>BIG-IP с Access Policy Manager BIG-IP версий 11.3x–11.6x
BIG-IP с Access Policy Manager (APM) BIG-IP версий 11.3x–11.6x реализует широко использующийся стандарт удостоверений SAML для предоставления инфраструктуры единого входа и платформы обмена атрибутами.

Далее приводится таблица поддержки сценария реализации единого входа: 

| Клиент | Поддержка | Исключения |
| --- | --- | --- |
| Веб-клиенты, такие как Exchange Web Access и SharePoint Online |Поддерживаются |None |
| Полнофункциональные клиентские приложения, такие как Lync, подписка Office Subscription, CRM |Не поддерживается |Не поддерживается |
| Полнофункциональные почтовые клиенты, такие как Outlook и ActiveSync |Поддерживаются |None |

Дополнительные сведения о BIG-IP Access Policy Manager см. на сайте компании F5 Networks в разделе [BIG-IP Access Policy Manager](https://f5.com/products/modules/access-policy-manager). 

Чтобы получить инструкции BIG-IP Access Policy Manager по настройке службы маркеров безопасности для предоставления функции единого входа пользователям Active Directory, скачайте PDF-файл [здесь](http://www.f5.com/pdf/deployment-guides/microsoft-office-365-idp-dg.pdf)

## <a name="vmware--workspace-portal-version-21"></a>Портал VMware Workspace версии 2.1
Портал VMware Workspace версии 2.1 реализует широко использующийся стандарт удостоверений WS Federation/WS-Trust для предоставления инфраструктуры единого входа и платформы обмена атрибутами.

Далее приводится таблица поддержки сценария реализации единого входа:

| Клиент | Поддержка | Исключения |
| --- | --- | --- |
| Веб-клиенты, такие как Exchange Web Access и SharePoint Online |Поддерживаются |Встроенная проверка подлинности Windows не поддерживается. |
| Полнофункциональные клиентские приложения, такие как Lync, подписка Office Subscription, CRM |Поддерживаются |Встроенная проверка подлинности Windows не поддерживается. |
| Полнофункциональные почтовые клиенты, такие как Outlook и ActiveSync |Поддерживаются |None |

Чтобы получить дополнительные сведения о портале VMware Workspace версии 2.1, скачайте PDF-файл [здесь](http://pubs.vmware.com/workspace-portal-21/topic/com.vmware.ICbase/PDF/workspace-portal-21-resource.pdf).

## <a name="signgo-53"></a>Sign&go 5.3
Sign&go 5.3 реализует широко использующийся стандарт удостоверений WS Federation/WS-Trust для предоставления инфраструктуры единого входа и платформы обмена атрибутами.

Далее приводится таблица поддержки сценария реализации единого входа:

| Клиент | Поддержка | Исключения |
| --- | --- | --- |
| Веб-клиенты, такие как Exchange Web Access и SharePoint Online |Поддерживаются |Поддерживаются контракты Kerberos |
| Полнофункциональные клиентские приложения, такие как Lync, подписка Office Subscription, CRM |Поддерживаются |None |
| Полнофункциональные почтовые клиенты, такие как Outlook и ActiveSync |Поддерживаются |None |

*Sign&go 5.3 поддерживает проверку подлинности Kerberos в конфигурации контракта Kerberos.  За помощью в настройке обратитесь к Ilex или просмотрите руководство по настройке [здесь](http://www.ilex-international.com/docs/sign&go_wsfederation_en.pdf)

## <a name="icewall-federation-version-30"></a>IceWall Federation версии 3.0
IceWall Federation версии 3.0 реализует широко использующийся стандарт удостоверений WS Federation/WS-Trust для предоставления инфраструктуры единого входа и платформы обмена атрибутами.

Далее приводится таблица поддержки сценария реализации единого входа:

| Клиент | Поддержка | Исключения |
| --- | --- | --- |
| Веб-клиенты, такие как Exchange Web Access и SharePoint Online |Поддерживаются |Встроенная проверка подлинности Windows не поддерживается. |
| Полнофункциональные клиентские приложения, такие как Lync, подписка Office Subscription, CRM |Поддерживаются |Встроенная проверка подлинности Windows не поддерживается. |
| Полнофункциональные почтовые клиенты, такие как Outlook и ActiveSync |Поддерживаются |None |

Дополнительные сведения об IceWall Federation см. [здесь](http://h50146.www5.hp.com/products/software/security/icewall/eng/federation/) и [здесь](http://h50146.www5.hp.com/products/software/security/icewall/federation/office365.html).

## <a name="ca-secure-cloud"></a>CA Secure Cloud
CA Secure Cloud реализует широко использующийся стандарт удостоверений WS Federation/WS-Trust для предоставления инфраструктуры единого входа и платформы обмена атрибутами.

Далее приводится таблица поддержки сценария реализации единого входа:

| Клиент | Поддержка | Исключения |
| --- | --- | --- |
| Веб-клиенты, такие как Exchange Web Access и SharePoint Online |Поддерживаются |Встроенная проверка подлинности Windows не поддерживается. |
| Полнофункциональные клиентские приложения, такие как Lync, подписка Office Subscription, CRM |Поддерживаются |Встроенная проверка подлинности Windows не поддерживается. |
| Полнофункциональные почтовые клиенты, такие как Outlook и ActiveSync |Поддерживаются |None |

Дополнительные сведения о CA Secure Cloud см. на [сайте компании CA](http://www.ca.com/us/products/security-as-a-service.aspx).

## <a name="dell-one-identity-cloud-access-manager-v71"></a>Dell One Identity Cloud Access Manager версии&7;.1
Dell One Identity Cloud Access Manager реализует широко использующийся стандарт удостоверений WS Federation/WS-Trust для предоставления инфраструктуры единого входа и платформы обмена атрибутами.

Далее приводится таблица поддержки сценария реализации единого входа:

| Клиент | Поддержка | Исключения |
| --- | --- | --- |
| Веб-клиенты, такие как Exchange Web Access и SharePoint Online |Поддерживаются |None |
| Полнофункциональные клиентские приложения, такие как Lync, подписка Office Subscription, CRM |Поддерживаются |None |
| Полнофункциональные почтовые клиенты, такие как Outlook и ActiveSync |Поддерживаются |None |

Дополнительные сведения о Dell One Identity Cloud Access Manager см. на сайте компании Dell в разделе [Cloud Access Manager](http://software.dell.com/products/cloud-access-manager).

 Инструкции по настройке службы маркеров безопасности для предоставления интерфейса единого входа пользователям Office 365 см. на сайте компании Dell в разделе [How To Configure Microsoft Office 365](http://documents.software.dell.com/dell-one-identity-cloud-access-manager/7.1/how-to-configure-microsoft-office-365) (Настройка Microsoft Office 365). 

## <a name="authanvil-single-sign-on-45"></a>AuthAnvil Single Sign On 4.5
AuthAnvil Single Sign On 4.5 реализует широко использующийся стандарт удостоверений WS Federation/WS-Trust для предоставления инфраструктуры единого входа и платформы обмена атрибутами.

Далее приводится таблица поддержки сценария реализации единого входа:

| Клиент | Поддержка | Исключения |
| --- | --- | --- |
| Веб-клиенты, такие как Exchange Web Access и SharePoint Online |Поддерживаются |Встроенная проверка подлинности Windows не поддерживается. |
| Полнофункциональные клиентские приложения, такие как Lync, подписка Office Subscription, CRM |Поддерживаются |Встроенная проверка подлинности Windows не поддерживается. |
| Полнофункциональные почтовые клиенты, такие как Outlook и ActiveSync |Поддерживаются |None |

Дополнительные сведения см. на сайте AuthAnvil в [разделе, посвященном настройке единого входа для Office&365;](https://help.scorpionsoft.com/entries/26538603-How-can-I-Configure-Single-Sign-On-for-Office-365-).

## <a name="sailpoint-identitynow"></a>Sailpoint IdentityNow
Sailpoint IdentityNow реализует широко используемый стандарт удостоверений WS Federation/WS-Trust для предоставления инфраструктуры единого входа и платформы обмена атрибутами.

Далее приводится таблица поддержки сценария реализации единого входа:

| Клиент | Поддержка | Исключения |
| --- | --- | --- |
| Веб-клиенты, такие как Exchange Web Access и SharePoint Online |Поддерживаются |Встроенная проверка подлинности Windows не поддерживается. |
| Полнофункциональные клиентские приложения, такие как Lync, подписка Office Subscription, CRM |Поддерживаются |Встроенная проверка подлинности Windows не поддерживается. |
| Полнофункциональные почтовые клиенты, такие как Outlook и ActiveSync |Поддерживаются |None |

Дополнительные сведения см. [на сайте Sailpoint IdentityNow.](https://www.sailpoint.com/idaas-identity-as-a-service-identitynow/)





<!--HONumber=Jan17_HO3-->


