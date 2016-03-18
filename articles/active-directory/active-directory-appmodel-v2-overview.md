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
	ms.date="02/20/2016"
	ms.author="dastrock"/>

# Вход для пользователей учетных записей Майкрософт и Azure AD в одном приложении

Раньше разработчику приложения, поддерживающего как учетные записи Майкрософт, так и Azure Active Directory, необходимо было обеспечивать интеграцию с двумя отдельными системами. Мы представляем новую версию API проверки подлинности, которая позволяет выполнять вход пользователей с помощью обоих типов учетных записей в системе Azure AD. Эта объединенная система проверки подлинности называется **конечной точкой версии 2.0**. С конечной точкой версии 2.0 одна простая интеграция позволяет охватить миллионы пользователей с личными, рабочими или учебными учетными записями.

Приложения, использующие конечную точку версии 2.0, также могут использовать API REST из [Microsoft Graph](https://graph.microsoft.io) и [Office 365](https://msdn.microsoft.com/office/office365/howto/authenticate-Office-365-APIs-using-v2) с помощью любого типа учетной записи.

> [AZURE.NOTE]
	Не все сценарии и компоненты Azure Active Directory поддерживаются конечной точкой версии 2.0. Чтобы определить, следует ли вам использовать конечную точку 2.0, ознакомьтесь с [ограничениями версии 2.0](active-directory-v2-limitations.md).


## Приступая к работе
Выберите платформу ниже, чтобы создать приложение с помощью наших библиотек и платформ с открытым исходным кодом. Вы также можете воспользоваться документацией по нашему протоколу OAuth 2.0 и OpenID Connect для отправки и получения сообщений протокола напрямую без использования библиотеки проверки подлинности. <!-- TODO: Finalize this table  -->

[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-v2-quickstart-table.md)]

## Новые возможности	
Основные сведения, приведенные здесь, будут полезны в понимании того, что возможно и что нет в конечной версии 2.0.

- При создании приложения в предварительной версии конечной точки 2.0 2015 года обязательно ознакомьтесь [с существенными изменениями протокола](active-directory-v2-preview-oidc-changes.md), которые мы недавно сделали.
- Узнайте о [типах приложений, которые можно создавать с помощью конечной точки версии 2.0](active-directory-v2-flows.md).
- [Изменения в наших протоколах и отличия конечной точки версии 2.0](active-directory-v2-compare.md) для разработчиков, знакомых с Azure Active Directory.
- Сведения об [ограничения и пределах](active-directory-v2-limitations.md) конечной точки версии 2.0.

## Справочные материалы
Следующие ссылки позволят глубже изучить платформу:

- Получите справку по переполнению стека, используя теги [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) или [adal](http://stackoverflow.com/questions/tagged/adal).
- [Справочник по протоколу версии 2.0](active-directory-v2-protocols.md)
- [Справочник по маркерам версии 2.0](active-directory-v2-tokens.md)
- [Области и разрешения в конечной точке версии 2.0](active-directory-v2-scopes.md)
- [Портал регистрации приложений Майкрософт](https://apps.dev.microsoft.com)
- [Справочник по API REST Office 365](https://msdn.microsoft.com/office/office365/howto/authenticate-Office-365-APIs-using-v2)
- [Microsoft Graph](https://graph.microsoft.io)
- Ниже приведены клиентские библиотеки с открытым исходным кодом и примеры, которые были протестированы с конечной точкой версии 2.0. Обратите внимание, что такие возможности, как [динамическая регистрация клиента с OpenID Connect](https://openid.net/specs/openid-connect-registration-1_0.html) и конечные точки проверки маркеров, еще не поддерживаются и для работы с конечной точкой версии 2.0 может потребоваться отключить эти возможности в библиотеке:  

  - [Сервер удостоверений WSO2 Java](https://docs.wso2.com/display/IS500/Introducing+the+Identity+Server)
  - [Федерация Gluu Java](https://github.com/GluuFederation/oxAuth)
  - [Node.Js passport-openidconnect](https://www.npmjs.com/package/passport-openidconnect)
  - [Базовый клиент PHP для OpenID Connect](https://github.com/jumbojett/OpenID-Connect-PHP)
  - [Пример на Android для OpenID Connect](https://github.com/learning-layers/android-openid-connect)

<!-- TODO: These articles
- [ADAL Library Reference]()
- [v2 Endpoint FAQs](active-directory-v2-faq.md)
- Give us your thoughts on the preview using [User Voice](http://feedback.azure.com/forums/169401-azure-active-directory) - we want to hear them!  Use the phrase "AppModelv2:" in the title of your post so we can find it.
-->

<!---HONumber=AcomDC_0224_2016-->