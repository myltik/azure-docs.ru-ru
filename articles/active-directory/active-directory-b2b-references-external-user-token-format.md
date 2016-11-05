---
title: Формат внешнего пользовательского токена для предварительной версии службы Azure Active Directory B2B Collaboration | Microsoft Docs
description: Служба Azure Active Directory B2B поддерживает взаимодействие между компаниями, позволяя предоставлять бизнес-партнерам выборочный доступ к вашим корпоративным приложениям.
services: active-directory
documentationcenter: ''
author: viv-liu
manager: cliffdi
editor: ''
tags: ''

ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 05/09/2016
ms.author: viviali

---
# Формат внешнего пользовательского токена для предварительной версии службы Azure Active Directory B2B Collaboration
Утверждения для стандартного токена Azure AD рассматриваются в статье [Поддерживаемые типы токенов и утверждений](active-directory-token-and-claims.md).

Утверждения, которые отличаются для внешнего пользователя службы совместной работы B2B, прошедшего проверку подлинности, приведены ниже.<br/>

* **OID** — идентификатор объекта из клиента ресурса;<br/>
* **TID** — идентификатор клиента из клиента ресурса;<br/>
* **Издатель** — клиент ресурса;<br/>
* **IDP** — домашний клиент пользователя;<br/>
* **AltSecId** — альтернативный идентификатор безопасности, являющийся непрозрачными для вас.<br/>

## Связанные статьи
Другие статьи о службе совместной работы Azure AD B2B:

* [Что такое служба совместной работы Azure AD B2B?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Принцип работы](active-directory-b2b-how-it-works.md)
* [Подробное пошаговое руководство](active-directory-b2b-detailed-walkthrough.md)
* [Справка в формате CSV-файла](active-directory-b2b-references-csv-file-format.md)
* [Изменение атрибутов объекта внешнего пользователя](active-directory-b2b-references-external-user-object-attribute-changes.md)
* [Текущие ограничения предварительной версии](active-directory-b2b-current-preview-limitations.md)
* [Указатель статьей по управлению приложениями в Azure Active Directory](active-directory-apps-index.md)

<!---HONumber=AcomDC_0511_2016-->