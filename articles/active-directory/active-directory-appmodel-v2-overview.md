<properties
	pageTitle="Обзор модели приложений версии 2.0 | Microsoft Azure"
	description="Общие сведения о создании приложений с поддержкой входа как по учетной записи Майкрософт, так и через Azure Active Directory."
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/16/2016"
	ms.author="dastrock"/>

# Вход для пользователей учетных записей Майкрософт и Azure AD в одном приложении

Раньше разработчику приложения, поддерживающего как учетные записи Майкрософт, так и Azure Active Directory, необходимо было обеспечивать интеграцию с двумя отдельными системами. Мы представляем новую версию API проверки подлинности, которая позволяет выполнять вход пользователей с помощью обоих типов учетных записей в системе Azure AD. Эта объединенная система проверки подлинности называется **конечной точкой версии 2.0**. С конечной точкой версии 2.0 одна простая интеграция позволяет охватить миллионы пользователей с личными, рабочими или учебными учетными записями.

Приложения, использующие конечную точку версии 2.0, также могут использовать API REST из [Microsoft Graph](https://graph.microsoft.io) и [Office 365](https://msdn.microsoft.com/office/office365/howto/authenticate-Office-365-APIs-using-v2) с помощью любого типа учетной записи.

<!-- For a quick introduction to the v2.0 endpoint, please view the [Getting Started with Microsoft Identities: Enterprise Grade Sign In For Your Apps](https://azure.microsoft.com/documentation/videos/build-2016-getting-started-with-microsoft-identities-enterprise-grade-sign-in-for-your-apps/) video. -->

## Приступая к работе
[AZURE.VIDEO build-2016-getting-started-with-microsoft-identities-enterprise-grade-sign-in-for-your-apps]

Выберите платформу ниже, чтобы создать приложение с помощью наших библиотек и платформ с открытым исходным кодом. Вы также можете воспользоваться документацией по протоколу OAuth 2.0 и OpenID Connect для отправки и получения сообщений протокола напрямую, без использования библиотеки аутентификации.

<!-- TODO: Finalize this table  -->
[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-v2-quickstart-table.md)]

## Новые возможности
Основные сведения, приведенные здесь, будут полезны в понимании того, что возможно и что нет в конечной версии 2.0.

- В случае создания приложения на этапе предварительной версии конечной точки 2.0 в 2015 году обязательно ознакомьтесь [с существенными изменениями протокола](active-directory-v2-preview-oidc-changes.md), которые мы недавно внесли.
- Узнайте о [типах приложений, которые можно создавать с помощью конечной точки версии 2.0](active-directory-v2-flows.md).
- Разработчикам, знакомым с Azure Active Directory, следует изучить [изменения в протоколах и отличия конечной точки версии 2.0](active-directory-v2-compare.md).
- Ознакомьтесь с [ограничениями](active-directory-v2-limitations.md) конечной точки версии 2.0.

## Справочные материалы
Следующие ссылки позволят глубже изучить платформу:

- Build 2016: [Getting Started with Microsoft Identities: Enterprise Grade Sign In For Your Apps](https://azure.microsoft.com/documentation/videos/build-2016-getting-started-with-microsoft-identities-enterprise-grade-sign-in-for-your-apps/) (Приступая к работе с удостоверениями Майкрософт: вход корпоративного класса для ваших приложений).
- Получите справку по переполнению стека, используя теги [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) или [adal](http://stackoverflow.com/questions/tagged/adal).
- [Справочник по протоколу версии 2.0](active-directory-v2-protocols.md)
- [Справочник по маркерам версии 2.0](active-directory-v2-tokens.md)
- [Области и разрешения в конечной точке версии 2.0](active-directory-v2-scopes.md)
- [Портал регистрации приложений Майкрософт](https://apps.dev.microsoft.com)
- [Справочник по API REST Office 365](https://msdn.microsoft.com/office/office365/howto/authenticate-Office-365-APIs-using-v2)
- [Microsoft Graph](https://graph.microsoft.io)
- Ниже приведены клиентские библиотеки с открытым кодом и примеры, которые были протестированы с конечной точкой версии 2.0.

  - [Сервер удостоверений WSO2 Java](https://docs.wso2.com/display/IS500/Introducing+the+Identity+Server)
  - [Федерация Gluu Java](https://github.com/GluuFederation/oxAuth)
  - [Node.Js passport-openidconnect](https://www.npmjs.com/package/passport-openidconnect)
  - [Базовый клиент OpenID Connect для PHP](https://github.com/jumbojett/OpenID-Connect-PHP)
  - [Клиент OAuth2 для iOS](https://github.com/nxtbgthng/OAuth2Client)
  - [Клиент OAuth2 для Android](https://github.com/wuman/android-oauth-client)
  - [Клиент OpenID Connect для Android](https://github.com/kalemontes/OIDCAndroidLib)

<!---HONumber=AcomDC_0921_2016-->