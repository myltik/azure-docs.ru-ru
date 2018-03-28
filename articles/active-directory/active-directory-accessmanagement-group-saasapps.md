---
title: Использование группы для управления доступом к приложениям SaaS | Документация Майкрософт
description: Использование групп в Azure Active Directory Premium или Basic для предоставления доступа к приложениям SaaS, интегрированным с Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.assetid: ab8dee63-bedc-46ca-8852-234f5c16ae98
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: curtand
ms.reviewer: piotrci
ms.custom: it-pro
ms.openlocfilehash: 4aee5b28fd42be98fac8f9c7d61538319e3efd9a
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/16/2018
---
# <a name="using-a-group-to-manage-access-to-saas-applications"></a>Использование группы для управления доступом к приложениям SaaS
В Azure Active Directory (Azure AD) с лицензией Azure AD Premium или Azure AD Basic можно использовать группы для предоставления доступа к приложению SaaS, интегрированному с Azure AD. Например, если вы хотите предоставить отделу маркетинга доступ к пяти разным приложениям SaaS, можно создать группу, содержащую пользователей из отдела маркетинга, и затем предоставить этой группе доступ к пяти приложениям SaaS, необходимым этому отделу. Таким образом можно сэкономить время, управляя членством пользователей отдела маркетинга в одном месте. Пользователи приложения назначаются при добавлении в группу маркетинга, и их назначения будут удалены из приложения, когда они будут удалены из группы. Эту возможность можно использовать для сотен приложений, которые можно добавлять из коллекции приложений Azure AD.

> [!IMPORTANT]
> Эту функцию можно использовать только в том случае, если вы запустите пробную версию Azure AD Premium или приобретете лицензию Azure AD Premium или Azure AD Basic. Назначение на основе группы поддерживается только для групп безопасности. Сейчас членство во вложенных группах не поддерживается в рамках назначения приложений на основе групп.

**Назначение пользователю или группе доступа к приложению SaaS**

1. В [центре администрирования Azure AD](https://aad.portal.azure.com) выберите **Корпоративные приложения**.
2. Выберите приложение, которое добавили из коллекции приложений, чтобы открыть его.
3. Выберите **Пользователи и группы**, затем — **Добавить пользователя**.
4. На странице **Добавление назначения** выберите **Пользователи и группы**, чтобы открыть список выбора **Пользователи и группы**.
6. Выберите любое количество групп или пользователей, затем щелкните или коснитесь **Выбрать**, чтобы добавить их в список **Добавление назначения**. На этом этапе можно также назначить роль пользователю.
7. Щелкните **Назначить**, чтобы назначить пользователей или группы для выбранного корпоративного приложения.

### <a name="next-steps"></a>Дополнительная информация
В следующих статьях содержатся дополнительные сведения об Azure Active Directory.

* [Управление доступом к ресурсам с помощью групп Azure Active Directory](active-directory-manage-groups.md)
* [Указатель статьей по управлению приложениями в Azure Active Directory](active-directory-apps-index.md)
* [Настройка параметров групп с помощью командлетов Azure Active Directory](active-directory-accessmanagement-groups-settings-cmdlets.md)
* [Что такое Microsoft Azure Active Directory](active-directory-whatis.md)
* [Интеграция локальных удостоверений с Azure Active Directory](active-directory-aadconnect.md)
