---
title: "Azure Active Directory B2C. Переключение на клиент B2C | Документация Майкрософт"
description: "Как переключиться в контекст клиента Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: mtillman
editor: parakhj
ms.assetid: 0eb1b198-44d3-4065-9fae-16591a8d3eae
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 4/13/2017
ms.author: parakhj
ms.openlocfilehash: 6c1fd08c52f33a062d06e0593cbbe00346bb44f1
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/11/2017
---
# <a name="switching-to-your-azure-ad-b2c-tenant"></a>Переключение на клиент Azure AD B2C

Чтобы настроить Azure AD B2C, вам нужно быть в контексте вашего клиента Azure AD B2C.

## <a name="log-into-azure-ad-b2c-tenant"></a>Вход в клиент Azure AD B2C

Чтобы перейти к вашему клиенту Azure AD B2C, необходимо войти на портал Azure как глобальный администратор клиента Azure AD B2C.

1. Войдите на [портал Azure](http://portal.azure.com).
1. Щелкните адрес электронной почты или картинку в правом верхнем углу, чтобы переключить клиентов.
1. В списке `Directory` выберите клиента Azure AD B2C, которым вы хотите управлять.

Портал Azure обновится.  Теперь вы вошли на портал Azure в контексте вашего клиента Azure AD B2C.

## <a name="navigate-to-the-b2c-features-blade"></a>Переход в колонку функций B2C

1. Нажмите кнопку **Обзор** в области навигации слева.
1. Щелкните **Больше служб**, а затем в области навигации слева найдите `Azure AD B2C`.  (Щелкните звезду слева от Azure AD B2C, чтобы закрепить его на левой начальной панели.)
1. Для доступа к колонке с функциями B2C щелкните ярлык **Azure AD B2C** .
   
    ![Снимок экрана: переход к колонке функций B2C](./media/active-directory-b2c-get-started/b2c-browse.png)

> [!IMPORTANT]
> Вам необходимо быть глобальным администратором данного клиента B2C, чтобы получить доступ к колонке функций B2C. Доступ к ней невозможен для глобальных администраторов или пользователей из других клиентов.  Переключиться на клиент B2C можно с помощью переключателя клиента в правом верхнем углу портала Azure.
