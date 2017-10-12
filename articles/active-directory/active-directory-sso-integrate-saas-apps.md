---
title: "Интеграция единого входа Azure Active Directory с приложениями SaaS | Документация Майкрософт"
description: "Управляйте централизованным доступом к приложениям SaaS, реализовав проверку подлинности единого входа и подготовку пользователей в Azure Active Directory. Общие сведения об интеграции Azure Active Directory с приложениями SaaS."
services: active-directory
keywords: "Интеграция Azure AD с приложениями SaaS"
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 53b9d341-d1fc-4bbb-ac7c-3f4c68fcf00a
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/17/2017
ms.author: curtand
ms.reviewer: aaronsm
ms.openlocfilehash: fc0d297598c334ca8f6f8a2bd3ae948c87956342
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="integrate-azure-active-directory-single-sign-on-with-saas-apps"></a>Интеграция единого входа Azure Active Directory с приложениями SaaS
> [!div class="op_single_selector"]
> * [Портал Azure](active-directory-enterprise-apps-manage-sso.md)
> * [классическом портале Azure](active-directory-sso-integrate-saas-apps.md)
>
>

[!INCLUDE [active-directory-sso-use-case-intro](../../includes/active-directory-sso-use-case-intro.md)]

Для начальной настройки единого входа для приложения, которое впервые используется в организации, применяется существующий каталог в Azure Active Directory (Azure AD). Можно использовать каталог Azure AD, полученный через Microsoft Azure, Office 365 или Windows Intune. При наличии двух или более из этих служб обратитесь к разделу [Администрирование каталога Azure AD](active-directory-administer.md) , чтобы определить, какую службу следует использовать.

> [!IMPORTANT]
> Для управления службой Azure AD мы рекомендуем использовать [Центр администрирования Azure AD](https://aad.portal.azure.com) на портале Azure, а не классический портал Azure, который упоминается в этой статье. Сведения о назначении административных ролей в центре администрирования Azure AD см. в статье [Назначение ролей администратора в Azure Active Directory](active-directory-enterprise-apps-manage-sso.md).

## <a name="authentication"></a>Аутентификация
Для приложений, которые поддерживают протоколы SAML 2.0, WS-Federation или OpenID Connect, Azure Active Directory использует сертификаты подписи для установления отношений доверия. Дополнительные сведения об этом см. в статье [Управление сертификатами для федеративного единого входа в Azure Active Directory](active-directory-sso-certs.md).

Для приложений, поддерживающих только вход на основе форм HTML, Azure Active Directory использует "хранилище паролей" для установления отношений доверия. Это позволяет пользователям вашей организации автоматически входить в приложение SaaS с помощью Azure AD, используя сведения учетной записи пользователя из приложения SaaS. Azure AD собирает и безопасно хранит данные учетной записи пользователя и связанный с ней пароль. Дополнительные сведения см. в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md#password-based-single-sign-on)

## <a name="authorization"></a>Авторизация
Подготовленная учетная запись дает возможность авторизоваться для использования приложения после проверки подлинности посредством единого входа. Подготовку пользователей можно выполнять вручную, а в отдельных случаях можно добавлять и удалять сведения о пользователях в приложении SaaS с учетом изменений, внесенных в Azure Active Directory. Дополнительные сведения об использовании имеющихся соединителей Azure AD для автоматической подготовки см. в статье [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory](active-directory-saas-app-provisioning.md).

В противном случае можно вручную добавить сведения о пользователе в приложение, а также использовать другие решения для подготовки, доступные на рынке.

## <a name="access"></a>Access
Azure AD предоставляет несколько настраиваемых способов развертывания приложений для конечных пользователей в организации. Вы не привязаны к конкретному решению развертывания или доступа. Можно использовать то [решение, которое наилучшим образом соответствует вашим потребностям](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users).

## <a name="additional-considerations-for-applications-already-in-use"></a>Дополнительные замечания для приложений, которые уже используются
Настройка единого входа для приложения, которое уже используется вашей организацией, отличается от создания новых учетных записей для нового приложения. При этом необходимо выполнить несколько предварительных действий, в том числе сопоставить удостоверения пользователей в приложении с удостоверениями Azure AD, а также понять, как пользователи будут входить в приложение после его интеграции.

> [!NOTE]
> Чтобы настроить единый вход для существующего приложения, необходимо иметь права глобального администратора как в Azure AD, так и в приложении SaaS.
>
>

### <a name="mapping-user-accounts"></a>Сопоставление учетных записей пользователей
Удостоверение пользователя обычно имеет уникальный идентификатор, в качестве которого может выступать адрес электронной почты или имя участника-пользователя (UPN). Вам необходимо связать удостоверение приложения каждого пользователя с соответствующим ему удостоверением Azure AD. Это можно сделать несколькими способами в зависимости от требований проверки подлинности приложения.

Дополнительные сведения о сопоставлении удостоверений приложения с удостоверениями Azure AD см. в статьях о [настройке утверждений, выпущенных в токене SAML](http://social.technet.microsoft.com/wiki/contents/articles/31257.azure-active-directory-customizing-claims-issued-in-the-saml-token-for-pre-integrated-apps.aspx) и [настройке сопоставлений атрибутов](active-directory-saas-customizing-attribute-mappings.md).

### <a name="understanding-the-users-log-in-experience"></a>Понимание входа пользователей в систему
При интеграции единого входа для уже используемого приложения важно понимать, что это повлияет на работу пользователей. Пользователи начнут использовать для входа свои учетные данные Azure AD во всех приложениях. Они также могут использовать другой портал для доступа к приложениям.

В некоторых приложениях единый вход может осуществляться в интерфейсе такого приложения, а в других приложениях пользователю потребуется воспользоваться центральным порталом приложений ([Мои приложения](http://myapps.microsoft.com) или [Office 365](http://portal.office.com/myapps)). Дополнительные сведения о различных типах единого входа и о соответствующем взаимодействии с пользователем см. в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

См. также раздел о *подавлении согласия пользователей* в [руководстве для разработчиков](active-directory-applications-guiding-developers-for-lob-applications.md).

## <a name="next-steps"></a>Дальнейшие действия
Для приложений SaaS из коллекции приложений Azure AD предлагает ряд [руководств по интеграции приложений SaaS](active-directory-saas-tutorial-list.md).

Если приложение отсутствует в коллекции, можно [добавить его в коллекцию приложений Azure AD в качестве пользовательского](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx).

В библиотеке Azure.com представлены более подробные сведения обо всех этих аспектах, начиная с раздела [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

Кроме того, не пропустите [Указатель статьей по управлению приложениями в Azure Active Directory](active-directory-apps-index.md).
