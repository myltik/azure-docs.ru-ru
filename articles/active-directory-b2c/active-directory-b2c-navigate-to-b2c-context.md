---
title: Azure Active Directory B2C. Переключение на клиент B2C | Документация Майкрософт
description: Как переключиться в контекст клиента Active Directory B2C
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 4/13/2017
ms.author: davidmu
ms.openlocfilehash: 6469636ee05c2b28a24b1d559f0ee7569e784393
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/23/2018
---
# <a name="switching-to-your-azure-ad-b2c-tenant"></a>Переключение на клиент Azure AD B2C

Чтобы настроить Azure AD B2C, вам нужно быть в контексте вашего клиента Azure AD B2C.

## <a name="log-into-azure-ad-b2c-tenant"></a>Вход в клиент Azure AD B2C

Чтобы перейти к вашему клиенту Azure AD B2C, необходимо войти на портал Azure как глобальный администратор клиента Azure AD B2C.

1. Войдите на [портал Azure](http://portal.azure.com).
1. Щелкните адрес электронной почты или картинку в правом верхнем углу, чтобы переключить клиентов.
1. В списке `Directory` выберите клиента Azure AD B2C, которым вы хотите управлять.

Страница портала Azure обновится.  Теперь вы вошли на портал Azure в контексте своего клиента Azure AD B2C.

## <a name="navigate-to-the-b2c-features-pane"></a>Переход в область функций B2C

1. Нажмите кнопку **Обзор** в области навигации слева.
1. Щелкните **Все службы**, а затем в области навигации слева найдите `Azure AD B2C`.  (Щелкните звезду слева от Azure AD B2C, чтобы закрепить его на левой начальной панели.)
1. Для доступа к области функций B2C щелкните **Azure AD B2C**.
   
    ![Снимок экрана: переход в область функций B2C](./media/active-directory-b2c-get-started/b2c-browse.png)

> [!IMPORTANT]
> Чтобы получить доступ к области функций B2C, необходимо быть глобальным администратором выбранного клиента B2C. Доступ к ней невозможен для глобальных администраторов или пользователей из других клиентов.  Переключиться на клиент B2C можно с помощью переключателя клиента в правом верхнем углу портала Azure.
