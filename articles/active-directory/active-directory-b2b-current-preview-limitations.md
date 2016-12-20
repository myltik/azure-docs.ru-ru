---
title: "Ограничения текущей предварительной версии службы совместной работы Azure Active Directory B2B | Документация Майкрософт"
description: "Служба Azure Active Directory B2B поддерживает взаимодействие между компаниями, позволяя предоставлять бизнес-партнерам выборочный доступ к вашим корпоративным приложениям."
services: active-directory
documentationcenter: 
author: viv-liu
manager: cliffdi
editor: 
tags: 
ms.assetid: 1cc8440c-b74b-4789-9401-428105e8a00c
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 05/09/2016
ms.author: viviali
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 852bc6e8880525bf72e86f3a0e64e63086c0ad04


---
# <a name="azure-ad-b2b-collaboration-preview-current-preview-limitations"></a>Текущие ограничения предварительной версии службы Azure Active Directory (Azure AD) B2B Collaboration
* Служба Multi-Factor Authentication (MFA) для внешних пользователей не поддерживается. Например, если в организации Contoso используется служба MFA, а в организации Partner Org она не поддерживается, пользователи Partner Org не смогут получить доступ к MFA через службу B2B Collaboration.
* Приглашения можно отправлять только с помощью CSV-файла. Индивидуальные приглашения и доступ к API не поддерживаются.
* Только глобальные администраторы Azure AD могут отправлять CSV-файлы.
* Рассылка приглашений на адреса электронной почты пользователей (например, hotmail.com, Gmail.com или comcast.net) в настоящее время не поддерживается.
* Возможность доступа внешних пользователей к локальным приложениям не проверялась.
* Внешние пользователи не удаляются автоматически при удалении фактического пользователя из соответствующего каталога.
* Отправка приглашений на списки рассылки не поддерживается.
* Через CSV-файл можно отправить не более 2000 записей.

## <a name="related-articles"></a>Связанные статьи
Другие статьи о службе совместной работы Azure AD B2B:

* [Что такое служба совместной работы Azure AD B2B?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Принцип работы](active-directory-b2b-how-it-works.md)
* [Подробное пошаговое руководство](active-directory-b2b-detailed-walkthrough.md)
* [Справочник по формату файлов CSV](active-directory-b2b-references-csv-file-format.md)
* [Формат токена внешнего пользователя](active-directory-b2b-references-external-user-token-format.md)
* [Изменение атрибутов объекта внешнего пользователя](active-directory-b2b-references-external-user-object-attribute-changes.md)
* [Указатель статьей по управлению приложениями в Azure Active Directory](active-directory-apps-index.md)




<!--HONumber=Nov16_HO3-->


