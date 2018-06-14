---
title: Предоставление разрешений для специально разработанного приложения | Документы Майкрософт
description: Предоставление разрешений для специально разработанного приложения с помощью портала Azure AD или параметра URL-адреса
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
ms.openlocfilehash: 0f13efd5e629bde592a3ab3a3e2db4a22180b5bb
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/11/2017
ms.locfileid: "26733770"
---
# <a name="how-to-grant-permissions-to-a-custom-developed-application"></a>Как предоставить разрешения для специально разработанного приложения

Если требуется дать согласие в приложении заранее или если возникло сообщение об ошибке в связи с тем, что не предоставлено согласие для приложения, попробуйте выполнить следующие действия.

## <a name="how-to-perform-admin-consent-for-your-application"></a>Предоставление согласия администратора для вашего приложения

Это приводит к тому, что согласие предоставляется для всех пользователей в организации.

1. Перейдите к колонке **Регистрация приложений** в качестве **глобального администратора**, а затем выберите приложение.

2. Выберите **Необходимые разрешения** и, наконец, нажмите кнопку **Предоставить разрешения** в верхней части колонки.

Кроме того, можно создать запрос на адрес *login.microsoftonline.com*, указав конфигурацию приложения и добавив *&prompt=admin\_consent*. После входа с учетными данными администратора приложению были предоставлены разрешения для всех пользователей.

## <a name="how-to-force-user-consent-for-your-application"></a>Принудительное предоставление согласия пользователя для приложения

* Добавьте *&prompt=consent* к запросу на аутентификацию, при этом пользователю необходимо будет давать согласие всякий раз, как он проходит аутентификацию.

## <a name="next-steps"></a>Дополнительная информация

[Согласие и интеграция приложений с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)

[Согласие и разрешения для конвергированных приложений в Azure AD версии 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[StackOverflow в AzureAD](http://stackoverflow.com/questions/tagged/azure-active-directory)
