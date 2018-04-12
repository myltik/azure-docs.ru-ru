---
title: Непредвиденная ошибка при предоставлении согласия для приложения | Документы Майкрософт
description: Здесь описаны ошибки, возникающие при предоставлении согласия для приложения, и способы их устранения.
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: bbc0cee8a44773c025c6174eaf7eccaba81b8d1b
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/05/2018
---
# <a name="unexpected-error-when-performing-consent-to-an-application"></a>Непредвиденная ошибка при предоставлении согласия для приложения

В этой статье описаны ошибки, возникающие при предоставлении согласия для приложения. Если вы получаете непредвиденные запросы согласия, не содержащие сообщения об ошибках, см. статью [Сценарии аутентификации в Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-scenarios).

Для правильной работы многим приложениям, интегрирующимся с Azure Active Directory, нужны разрешения на доступ к другим ресурсам. Если эти ресурсы также интегрированы с Azure Active Directory, доступ к ним часто запрашивается с помощью общей инфраструктуры согласия. 

Это приводит к выводу запроса согласия, что обычно происходит при первом использовании приложения, но может встречаться и при дальнейшем использовании.

Чтобы пользователь дал согласие на необходимые приложению разрешения, должны выполняться определенные условия. В противном случае могут возникать различные ошибки. в частности такие:

## <a name="requesting-not-authorized-permissions-error"></a>Ошибка при запросе неавторизованных разрешений
* **AADSTS90093:** &lt;clientAppDisplayName&gt; is requesting one or more permissions that you are not authorized to grant. Contact an administrator, who can consent to this application on your behalf. ("clientAppDisplayName" запрашивает одно или несколько разрешений, которые вы не имеете права предоставлять. Обратитесь к администратору, который может дать согласие для этого приложения от вашего имени.)

Эта ошибка возникает, когда пользователь, не являющийся администратором организации, пытается использовать приложение, которому нужны разрешения, предоставляемые лишь администратором. Администратор может устранить эту ошибку, предоставив доступ к приложению от имени своей организации.

## <a name="policy-prevents-granting-permissions-error"></a>Ошибка при блокировке назначения разрешений политикой
* **AADSTS90093:** An administrator of &lt;tenantDisplayName&gt; has set a policy that prevents you from granting &lt;name of app&gt; the permissions it is requesting. Contact an administrator of &lt;tenantDisplayName&gt;, who can grant permissions to this app on your behalf. (Администратор "tenantDisplayName" задал политику, которая запрещает предоставление разрешений, запрошенных "имя приложения". Обратитесь к администратору "tenantDisplayName", который может предоставить разрешения для этого приложения от вашего имени.)

Эта ошибка возникает, когда администратор организации отключает пользователям возможность давать согласие для приложений, после чего пользователь без прав администратора пытается использовать приложение, требующее согласие. Администратор может устранить эту ошибку, предоставив доступ к приложению от имени своей организации.

## <a name="intermittent-problem-error"></a>Ошибка, связанная с периодически возникающей проблемой
* **AADSTS90090:** It looks like we encountered an intermittent problem recording the permissions you attempted to grant to &lt;clientAppDisplayName&gt;. Try again later. (Похоже, мы столкнулись с периодически возникающей проблемой при записи разрешений, которые вы пытались предоставить "clientAppDisplayName". Повторите попытку позже.)

Эта ошибка указывает на наличие периодически возникающей проблемы на стороне службы. Для ее решения можно еще раз попытаться предоставить согласие для приложения.

## <a name="resource-not-available-error"></a>Ошибка при недоступном ресурсе
* **AADSTS65005:** The app &lt;clientAppDisplayName&gt; requested permissions to access a resource &lt;resourceAppDisplayName&gt; that is not available. (Приложение "clientAppDisplayName" запросило разрешения для обращения к недоступному ресурсу "resourceAppDisplayName".) 

Свяжитесь с разработчиком приложения.

##  <a name="resource-not-available-in-tenant-error"></a>Ошибка при недоступном ресурсе в клиенте
* **AADSTS65005:** &lt;clientAppDisplayName&gt; is requesting access to a resource &lt;resourceAppDisplayName&gt; that is not available in your organization &lt;tenantDisplayName&gt;. ("clientAppDisplayName" запрашивает доступ к ресурсу "resourceAppDisplayName", который недоступен в вашей организации "tenantDisplayName".) 

Убедитесь, что этот ресурс доступен, или обратитесь к администратору &lt;tenantDisplayName&gt;.

## <a name="permissions-mismatch-error"></a>Ошибка при несовпадении разрешений
* **AADSTS65005:** The app requested consent to access resource &lt;resourceAppDisplayName&gt;. This request failed because it does not match how the app was pre-configured during app registration. Contact the app vendor.** (Приложение запросило согласие на обращение к ресурсу "resourceAppDisplayName". Выполнить этот запрос не удалось, так как он не соответствует предварительной настройке приложения, заданной при регистрации. Обратитесь к поставщику.)

Эти ошибки всегда возникают, когда приложение, для которого пользователь пытается предоставить согласие, запрашивает разрешения на доступ к приложению-ресурсу, отсутствующее в каталоге организации (клиенте). Это может произойти по нескольким причинам.

-   Разработчик клиентского приложения неправильно настроил его, в результате чего оно запрашивает доступ к недопустимому ресурсу. В этом случае разработчику нужно обновить конфигурации клиентского приложения для устранения этой проблемы.

-   Субъект-служба, представляющий целевое приложение-ресурс, которое отсутствует в организации либо было удалено. Чтобы устранить эту проблему, в организации нужно подготовить субъект-службу для приложения-ресурса, после чего клиентское приложение сможет запросить для него права. В зависимости от типа приложения это можно осуществить несколькими способами, в том числе:

    -   Приобретение подписки для приложения-ресурса (приложения, опубликованные корпорацией Майкрософт)

    -   Предоставление согласия для приложения-ресурса

    -   Предоставление разрешений для приложения с помощью портала Azure

    -   Добавление приложения из коллекции приложений Azure AD

## <a name="next-steps"></a>Дополнительная информация 

[Приложения, разрешения и согласие в Azure Active Directory (конечная точка версии 1)](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent)<br>

[Области, разрешения и согласие в Azure Active Directory (конечная точка версии 2.0)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)


