---
title: "Предварительная версия службы совместной работы Azure Active Directory B2B: простая и надежная облачная интеграция партнеров | Документация Майкрософт"
description: "Служба совместной работы Azure Active Directory B2B поддерживает взаимодействие между компаниями, позволяя предоставлять бизнес-партнерам выборочный доступ к вашим корпоративным приложениям."
services: active-directory
documentationcenter: 
author: viv-liu
manager: femila
editor: 
tags: 
ms.assetid: 1464387b-ee8b-4c7c-94b3-2c5567224c27
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 09/27/2016
ms.author: femila
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 40c98146f1bbf1b1d53cd27044ce383c33098870


---
# <a name="azure-ad-b2b-collaboration-preview-simple-secure-cloud-partner-integration"></a>Предварительная версия службы совместной работы Azure Active Directory (Azure AD) B2B: простая и надежная облачная интеграция партнеров
По мере того как компании все больше внимания уделяют своему основному бизнесу, потребность в партнерских отношениях с другими компаниями растет. Компаниям нужно просто и безопасно обмениваться ресурсами (например, осуществлять доступ к корпоративным приложениям) со своими партнерами, чтобы наладить эффективную совместную работу. Служба совместной работы Azure Active Directory B2B поддерживает взаимодействие между компаниями, позволяя предоставлять партнерам выборочный доступ к вашим корпоративным приложениям и данным, используя их удостоверения с возможностью самостоятельного управления. Служба совместной работы Azure AD B2B отличается следующим.

* **Простота.** Каждый пользователь партнера может использовать имеющуюся учетную запись Azure AD или запись, которую легко создать, принимая приглашение. Такому пользователю можно предоставить прямой доступ к выбранному корпоративному приложению или набору приложений, воспользовавшись панелью доступа к приложениям.
* **Безопасность.** Администратор контролирует весь доступ к вашим корпоративным приложениям через каталог Azure AD. При завершении совместной работы пользователей партнера можно удалить из каталога Azure AD, после чего они немедленно потеряют доступ к вашим приложениям. Кроме того, если пользователь партнера покидает организацию партнера, он (-а) лишается доступа автоматически.
* **Непрерывность.** Партнерам, которым необходим доступ к вашим корпоративным приложениям, **не требуется иметь Azure AD**. Служба совместной работы Azure AD B2B позволяет легко и просто регистрировать пользователей, благодаря чему партнеры мгновенно получают доступ к вашим приложениям.

Ознакомьтесь с [записью в блоге о выходе общедоступной предварительной версии](http://blogs.technet.com/b/ad/archive/2015/09/15/learn-all-about-the-azure-ad-b2b-collaboration-preview.aspx) и [подробным видео](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-Active-Directory-B2B-collaboration-demo) об использовании службы совместной работы Azure AD B2B.

Чтобы сравнить варианты использования для службы совместной работы Azure AD B2B, Azure AD B2C и мультитенантные приложения с Azure AD, см. статью [Сравнение возможностей управления внешними удостоверениями с помощью Azure Active Directory](active-directory-b2b-compare-external-identities.md).

## <a name="related-articles"></a>Связанные статьи
Другие статьи о службе совместной работы Azure AD B2B:

* [Принцип работы](active-directory-b2b-how-it-works.md)
* [Подробное пошаговое руководство](active-directory-b2b-detailed-walkthrough.md)
* [Справочник по формату файлов CSV](active-directory-b2b-references-csv-file-format.md)
* [Формат токена внешнего пользователя](active-directory-b2b-references-external-user-token-format.md)
* [Изменение атрибутов объекта внешнего пользователя](active-directory-b2b-references-external-user-object-attribute-changes.md)
* [Текущие ограничения предварительной версии](active-directory-b2b-current-preview-limitations.md)
* [Указатель статьей по управлению приложениями в Azure Active Directory](active-directory-apps-index.md)




<!--HONumber=Nov16_HO3-->


