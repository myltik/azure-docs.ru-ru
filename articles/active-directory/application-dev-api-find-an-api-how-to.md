---
title: "Как найти интерфейс API, необходимый для пользовательского приложения | Документы Майкрософт"
description: "Как настроить разрешения, необходимые для доступа к определенному интерфейсу API, в пользовательском приложении Azure AD"
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
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: 98c247a03132ad1f5bf158d257cd5ba764162112
ms.lasthandoff: 04/17/2017


---

# <a name="how-to-find-a-specific-api-needed-for-a-custom-developed-application"></a>Как найти интерфейс API, необходимый для пользовательского приложения

Для доступа к интерфейсам API необходимо настроить области доступа и роли. Если вы хотите предоставить веб-API приложения-ресурса клиентским приложениям, необходимо настроить области доступа и роли для интерфейсов API. Если вы хотите, чтобы клиентское приложение получило доступ к веб-API, необходимо настроить разрешения на доступ к API-интерфейсу при регистрации приложения.

## <a name="configuring-a-resource-application-to-expose-web-apis"></a>Настройка приложения ресурсов для предоставления веб-API

При предоставлении веб-API этот интерфейс API будет отображаться в списке **Выберите API** при добавлении разрешений для регистрации приложения. Чтобы добавить области доступа, выполните действия, описанные в разделе [Добавление областей доступа для приложения-ресурса](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#adding-access-scopes-to-your-resource-application).

## <a name="configuring-a-client-application-to-access-web-apis"></a>Настройка клиентского приложения для доступа к веб-API

При добавлении разрешений во время регистрации вашего приложения можно **добавить доступ к API** к предоставляемым веб-API. Для доступа к веб-API выполните действия, описанные в разделе [Добавление учетных данных или разрешений для доступа к веб-API](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#to-add-credentials-or-permissions-to-access-web-apis).

## <a name="next-steps"></a>Дальнейшие действия

-   [Интеграция приложений с Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)

-   [Основные сведения о манифесте приложения Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest)



