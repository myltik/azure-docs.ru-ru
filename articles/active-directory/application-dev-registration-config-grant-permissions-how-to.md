---
title: "Предоставление разрешений для специально разработанного приложения | Документы Майкрософт"
description: "Предоставление разрешений для специально разработанного приложения с помощью портала Azure AD или параметра URL-адреса"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2017
ms.author: asteen
translationtype: Human Translation
ms.sourcegitcommit: c300ba45cd530e5a606786aa7b2b254c2ed32fcd
ms.openlocfilehash: 922774c2482737537b64787ae473231ec1fbb68e
ms.lasthandoff: 04/14/2017


---

# <a name="how-to-grant-permissions-to-a-custom-developed-application"></a>Как предоставить разрешения для специально разработанного приложения

Если требуется дать согласие в приложении заранее или возникла ошибка, указывающая на то, что вы не предоставили согласие для приложения, попробуйте выполнить приведенные ниже действия.

## <a name="how-to-perform-admin-consent-for-your-application"></a>Предоставление согласия администратора для вашего приложения

Это приводит к предоставлению согласия для приложения для всех пользователей в организации.

1. Перейдите к колонке **Регистрация приложений** в качестве **глобального администратора**, а затем выберите приложение.

2. Выберите **Необходимые разрешения** и, наконец, нажмите кнопку **Предоставить разрешения** в верхней части колонки.

Кроме того, можно создать запрос на адрес *login.microsoftonline.com*, указав конфигурацию приложения и добавив *&prompt=admin\_consent*. После входа с учетными данными администратора приложению были предоставлены разрешения для всех пользователей.

## <a name="how-to-force-user-consent-for-your-application"></a>Принудительное предоставление согласия пользователя для приложения

* Добавьте *&prompt=consent* к запросам проверки подлинности, чтобы пользователю требовалось давать согласие при каждой проверке.

## <a name="next-steps"></a>Дальнейшие действия

[Согласие и интеграция приложений с Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-integrating-applications)

[Согласие и разрешения для конвергированных приложений в Azure AD версии 2.0](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-v2-scopes)<br>

[StackOverflow в AzureAD](http://stackoverflow.com/questions/tagged/azure-active-directory)

