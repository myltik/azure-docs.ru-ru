---
title: "Определение используемого метода единого входа | Документация Майкрософт"
description: "Общие сведения о режимах единого входа, поддерживаемых в Azure AD, и о выборе подходящего режима для интересующего приложения."
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
ms.sourcegitcommit: cc9e81de9bf8a3312da834502fa6ca25e2b5834a
ms.openlocfilehash: 6aba9206ab0634e9379d63520ad9e2d0256b7e94
ms.lasthandoff: 04/11/2017


---

# <a name="how-to-determine-what-single-sign-on-method-to-use"></a>Определение используемого метода единого входа

В этой статье представлены общие сведения о режимах единого входа, поддерживаемых в Azure AD, и о выборе подходящего режима для интересующего приложения.

## <a name="single-sign-on-and-provisioning-modes-supported-by-specific-application-types"></a>Режимы единого входа и подготовки, поддерживаемые конкретными типами приложений

В следующей таблице описываются различные режимы единого входа и подготовки, поддерживаемые каждым из приведенных выше типов приложений. С помощью этой таблицы вы узнаете, какое приложение необходимо добавить для достижения конкретной цели.

  ![Таблица типов приложений](./media/application-tables/table1.png)

## <a name="how-to-choose-a-single-sign-on-mode"></a>Выбор режима единого входа

Ниже приведены поддерживаемые режимы **единого входа** для приложений Azure AD.

-   **Единый вход Azure AD отключен.** Выберите **режим** "Единый вход Azure AD отключен", если вы еще не готовы к интеграции этого приложения с единым входом в Azure AD или просто его тестируете.

-   **Вход по ссылке.** Выберите **режим** [Вход по ссылке](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) при наличии приложения, уже подключенного с решением имеющегося единого входа, или если требуется опубликовать простую ссылку для пользователей на [панели доступа к приложениям](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) или в [средстве запуска приложений Office 365](https://login.microsoftonline.com/common/oauth2/authorize?response_mode=form_post&response_type=id_token&scope=openid&nonce=d508a995-f6d6-4b8a-81b8-825c71f1be46.636253878097046923&state=https%3a%2f%2fsupport.office.com%2farticle%2fMeet-the-Office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a%3fui%3den-US%26rs%3den-US%26ad%3dUS&client_id=4b233688-031c-404b-9a80-a4f3f2351f90&redirect_uri=https%3a%2f%2fsupport.office.com%2fauth%2fsignin&login_hint=asteen%40microsoft.com&prompt=none).

-   **Вход по паролю.** Выберите **режим единого входа** [Вход по паролю](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work), если приложение отображает HTML-поле имени пользователя и пароля, а также если необходимо безопасно сохранить имя пользователя и пароль для их повторного использования в приложении.

-   **Вход на основе SAML.** Выберите режим единого входа [Вход на основе SAML](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work), если приложение поддерживает протоколы SAML или OpenID Connect или если вам нужно сопоставить пользователей с конкретными ролями приложений на основе правил, определенных в утверждениях SAML. *(**Примечание.** Этот параметр недоступен при настройке прокси приложения для приложения.)*

-   **Вход на основе заголовков.** Выберите режим единого входа [Вход на основе заголовков](https://docs.microsoft.com/azure/active-directory/application-proxy-ping-access#what-is-pingaccess-for-azure-ad) при наличии приложения, использующего PingAccess, поддерживающего аутентификацию на основе HTTP-заголовка, для которой необходимо выполнить единый вход. *(**Примечание.** Этот параметр доступен только при настройке прокси приложения и PingAccess для приложения.)*

-   **Интегрированная проверка подлинности Windows.** Выберите режим единого входа [Интегрированная проверка подлинности Windows](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-sso-using-kcd) при использовании локального приложения WIA, для которого необходимо выполнить единый вход. *(**Примечание.** Этот параметр доступен только при настройке прокси приложения для приложения.)*

## <a name="single-sign-on-modes-for-custom-developed-applications"></a>Режимы единого входа для специально разработанных приложений

Приложения, настроенные с помощью возможности [специально разработанного приложения](#_Custom-Developed_Applications), также поддерживают дополнительные режимы единого входа, не указанные в списке выше. В частности, описаны такие возможности:

-   [OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code) на основе единого входа;

-   [OpenID Connect 1.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code) на основе единого входа;

-   [WS-Federation 1.2](http://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html) на основе единого входа;

-   [SAML 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-protocol-reference) на основе единого входа.

Ознакомьтесь с [руководством разработчика по Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide), чтобы узнать больше о создании специально разработанного приложения, поддерживающего данные режимы единого входа.

## <a name="how-to-set-an-applications-single-sign-on-mode"></a>Настройка режима единого входа для приложения

Для **этого** выполните приведенные далее инструкции.

1.  Откройте [**портал Azure**](https://portal.azure.com/) и войдите в систему как **глобальный администратор** или **соадминистратор**.

2.  Откройте **расширение Azure Active Directory**, щелкнув **Больше служб** в нижней части расположенного слева главного меню навигации.

3.  В поле фильтра поиска введите **Azure Active Directory** и выберите элемент **Azure Active Directory**.

4.  В меню навигации Azure Active Directory слева щелкните **Корпоративные приложения**.

5.  Щелкните **Все приложения**, чтобы открыть полный список приложений.

   * Если нужное приложение отсутствует в списке, в верхней части списка **Все приложения** воспользуйтесь элементом управления **Фильтр**, указав в нем для параметра **Показать** значение **Все приложения**.

6.  Выберите приложение, для которого необходимо настроить единый вход.

7.  После загрузки приложения выберите для этого приложения пункт **Единый вход** в меню навигации слева.

## <a name="next-steps"></a>Дальнейшие действия
[Реализация единого входа в приложения с помощью прокси приложения](active-directory-application-proxy-sso-using-kcd.md)


