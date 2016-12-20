---
title: "Изменения в атрибутах объекта внешнего пользователя в предварительной версии службы совместной работы Azure Active Directory B2B | Документация Майкрософт"
description: "Служба Azure Active Directory B2B поддерживает взаимодействие между компаниями, позволяя предоставлять бизнес-партнерам выборочный доступ к вашим корпоративным приложениям."
services: active-directory
documentationcenter: 
author: viv-liu
manager: cliffdi
editor: 
tags: 
ms.assetid: 4ede9c7b-3830-42f2-b73d-cbecbcc7db64
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 05/09/2016
ms.author: viviali
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: d57fd7d6dcd186d1e79528c716d8bf81ad868ae5


---
# <a name="azure-ad-b2b-collaboration-preview-external-user-object-attribute-changes"></a>Изменения в атрибутах объекта внешнего пользователя в предварительной версии службы Azure Active Directory (Azure AD) B2B Collaboration
Каждый пользователь в каталоге Azure AD представлен объектом пользователя. Атрибуты объекта пользователя в Azure AD претерпевают ряд изменений на разных этапах процесса приглашения и активации в службе B2B Collaboration. Объект пользователя, представляющий пользователя-партнера в каталоге, имеет атрибуты, которые изменяются при активации приглашения, т. е. когда пользователь- партнер переходит по ссылке в электронном письме с приглашением. В частности:

* Присваиваются значения атрибутам **SignInName** и **AltSecId**.
* Значение атрибута **DisplayName** изменяется: вместо имени участника-пользователя (user_fabrikam.com#EXT#@contoso.com) присваивается учетное имя (user@fabrikam.com).

Вы можете проверить значения этих атрибутов в Azure AD, чтобы понять, активировал ли пользователь-партнер свое приглашение в службу B2B Collaboration.

## <a name="related-articles"></a>Связанные статьи
Другие статьи о службе совместной работы Azure AD B2B:

* [Что такое служба совместной работы Azure AD B2B?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Принцип работы](active-directory-b2b-how-it-works.md)
* [Подробное пошаговое руководство](active-directory-b2b-detailed-walkthrough.md)
* [Справочник по формату файлов CSV](active-directory-b2b-references-csv-file-format.md)
* [Формат маркера внешнего пользователя](active-directory-b2b-references-external-user-token-format.md)
* [Текущие ограничения предварительной версии](active-directory-b2b-current-preview-limitations.md)
* [Указатель статьей по управлению приложениями в Azure Active Directory](active-directory-apps-index.md)




<!--HONumber=Nov16_HO3-->


