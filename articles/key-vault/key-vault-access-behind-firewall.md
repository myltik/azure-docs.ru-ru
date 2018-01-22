---
title: "Доступ к хранилищу ключей под защитой брандмауэра | Документация Майкрософт"
description: "Сведения о получении доступа к хранилищу ключей Azure из приложения, защищенного брандмауэром."
services: key-vault
documentationcenter: 
author: amitbapat
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 50d21774-2ee1-4212-8995-570c9de603c5
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 01/07/2017
ms.author: ambapat
ms.openlocfilehash: ad31e869d998d29d403ff97c17150c5078ce856d
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/17/2018
---
# <a name="access-azure-key-vault-behind-a-firewall"></a>Доступ к хранилищу ключей Azure из-за брандмауэра
### <a name="q-my-key-vault-client-application-needs-to-be-behind-a-firewall-what-ports-hosts-or-ip-addresses-should-i-open-to-enable-access-to-a-key-vault"></a>Вопрос. Мое клиентское приложение хранилища ключей должно находиться за брандмауэром. Какие порты, узлы или IP-адреса следует открыть для получения доступа к хранилищу ключей?
Для доступа к хранилищу ключей необходимо, чтобы клиентское приложение получило доступ к нескольким конечным точкам, требуемым для различных функций:

* Проверка подлинности с помощью Azure Active Directory (Azure AD).
* Управление хранилищем ключей Azure. Это включает в себя создание, чтение, обновление, удаление и настройку политик доступа с помощью Azure Resource Manager.
* Доступ к объектам (ключи и секреты), хранящимся в хранилище ключей, и управление ими осуществляется через определенную конечную точку хранилища ключей (например, [https://имя_вашего_хранилища.vault.azure.net](https://yourvaultname.vault.azure.net)).  

Требования отличаются в зависимости от конфигурации и среды.   

## <a name="ports"></a>порты;
Весь трафик в хранилище ключей для всех трех функций (проверки подлинности, управления и доступа к данным) направляется по протоколу HTTPS через порт 443. Однако для списка отзыва сертификатов время от времени трафик будет поступать через HTTP (порт 80). Клиентам, поддерживающим протокол OCSP, не следует обращаться к списку отзыва сертификатов. Иногда они могут перейти по ссылке [http://cdp1.public-trust.com/CRL/Omniroot2025.crl](http://cdp1.public-trust.com/CRL/Omniroot2025.crl).  

## <a name="authentication"></a>Authentication
Для проверки подлинности клиентским приложениям хранилища ключей потребуется доступ к конечным точкам Azure Active Directory. Используемая конечная точка зависит от конфигурации клиента Azure AD, типа субъекта (субъекта-пользователя, субъекта-службы) и типа учетной записи (учетная запись Майкрософт либо рабочая или учебная учетная запись).  

| Тип субъекта | Конечная точка и порт |
| --- | --- |
| Пользователь, использующий учетную запись Майкрософт<br> (например, user@hotmail.com) |**Глобально:**<br> login.microsoftonline.com:443<br><br> **Azure для Китая:**<br> login.chinacloudapi.cn:443<br><br>**Azure для правительства США:**<br> login.microsoftonline.us:443<br><br>**Azure для Германии:**<br> login.microsoftonline.de:443<br><br> и <br>login.live.com:443 |
| Пользователь или субъект-служба, использующие рабочую или учебную учетную запись с помощью Azure AD (например, user@contoso.com) |**Глобально:**<br> login.microsoftonline.com:443<br><br> **Azure для Китая:**<br> login.chinacloudapi.cn:443<br><br>**Azure для правительства США:**<br> login.microsoftonline.us:443<br><br>**Azure для Германии:**<br> login.microsoftonline.de:443 |
| Пользователь или субъект-служба, использующие рабочую или учебную учетную запись, а также службы федерации Active Directory (AD FS) или другую федеративную конечную точку (например, user@contoso.com) |Все конечные точки для рабочей или учебной учетной записи, а также службы федерации Active Directory или другие федеративные конечные точки |

Есть и другие возможные сложные сценарии. Дополнительные сведения см. в статьях [Сценарии аутентификации в Azure Active Directory](/documentation/articles/active-directory-authentication-scenarios/), [Интеграция приложений с Azure Active Directory](/documentation/articles/active-directory-integrating-applications/) и [Руководство разработчика по Azure Active Directory](https://msdn.microsoft.com/library/azure/dn151124.aspx).  

## <a name="key-vault-management"></a>Управление хранилищем ключей
Для управления хранилищем ключей (операции CRUD и настройка политики доступа) клиентскому приложению хранилища ключей требуется доступ к конечной точке Azure Resource Manager.  

| Тип операции | Конечная точка и порт |
| --- | --- |
| Операции уровня управления хранилищем ключей<br> с использованием Azure Resource Manager |**Глобально:**<br> management.azure.com:443<br><br> **Azure для Китая:**<br> management.chinacloudapi.cn:443<br><br> **Azure для правительства США:**<br> management.usgovcloudapi.net:443<br><br> **Azure для Германии:**<br> management.microsoftazure.de:443 |
| API Graph Azure Active Directory |**Глобально:**<br> graph.windows.net:443<br><br> **Azure для Китая:**<br> graph.chinacloudapi.cn:443<br><br> **Azure для правительства США:**<br> graph.windows.net:443<br><br> **Azure для Германии:**<br> graph.cloudapi.de:443 |

## <a name="key-vault-operations"></a>Операции с хранилищем ключей
Для полного управления объектами хранилища ключей (ключами и секретами) и криптографическими операциями клиенту хранилища ключей требуется доступ к конечной точке. DNS-суффикс конечной точки отличается в зависимости от расположения хранилища ключей. Конечная точка хранилища ключей имеет формат — *<имя_хранилища>*.*<DNS-суффикс_конкретного_региона>*, как описано в таблице ниже.  

| Тип операции | Конечная точка и порт |
| --- | --- |
| Операции, в том числе криптографические операции для ключей; создание, чтение, обновление и удаление ключей и секретов; задание или получение тегов и других атрибутов для объектов хранилища ключей (ключи и секреты) |**Глобально:**<br> &lt;vault-name&gt;.vault.azure.net:443<br><br> **Azure для Китая:**<br> &lt;vault-name&gt;.vault.azure.cn:443<br><br> **Azure для правительства США:**<br> &lt;vault-name&gt;.vault.usgovcloudapi.net:443<br><br> **Azure для Германии:**<br> &lt;vault-name&gt;.vault.microsoftazure.de:443 |

## <a name="ip-address-ranges"></a>Диапазоны IP-адресов
В службе хранилища ключей используются другие ресурсы Azure, такие как инфраструктура PaaS. Поэтому невозможно предоставить определенный диапазон IP-адресов, которые будут иметь конечные точки службы хранилища ключей в любой определенный момент времени. Если брандмауэр поддерживает только диапазоны IP-адресов, ознакомьтесь с документом [Microsoft Azure Datacenter IP Ranges](https://www.microsoft.com/download/details.aspx?id=41653) (Диапазоны IP-адресов центра обработки данных Microsoft Azure). Для проверки подлинности и идентификации (в Azure Active Directory) приложение должно иметь доступ к конечным точкам, описанным в статье [URL-адреса и диапазоны IP-адресов Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

## <a name="next-steps"></a>Дополнительная информация
Если у вас возникли вопросы о хранилище ключей, посетите [форумы хранилища ключей Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault).

