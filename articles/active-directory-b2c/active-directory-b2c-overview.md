<properties
	pageTitle="Обзор Azure Active Directory B2C | Microsoft Azure"
	description="Разработка потребительских приложений с помощью Azure Active Directory B2C"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="bryanla"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="07/24/2016"
	ms.author="swkrish"/>

# Azure Active Directory B2C: регистрация и вход пользователей в приложения

Azure Active Directory B2C — это комплексное облачное решение, позволяющее управлять удостоверениями в веб-приложениях и мобильных приложениях, с которыми взаимодействуют клиенты. Это высокодоступная глобальная служба, которая масштабируется до сотен миллионов идентификаторов потребителей. Так как служба Azure Active Directory B2C разработана на основе безопасной платформы корпоративного уровня, она защищает приложения, бизнес-процессы и пользовательские данные.

Раньше разработчикам приложений, которым было необходимо регистрировать потребителей и выполнять их вход в своих приложениях, приходилось писать собственный код. Кроме того, для хранения имен пользователей и паролей приходилось использовать локальные базы данных или системы. Azure Active Directory B2C предлагает разработчикам лучший способ интеграции функции управления идентификацией потребителей в свои приложения с помощью безопасной платформы, основанной на стандартах, и широкого набора расширяемых политик. Azure Active Directory B2C позволяет клиентам регистрироваться для использования ваших приложений с помощью существующих учетных записей в социальных сетях (Facebook, Google, Amazon, LinkedIn). Кроме того, ваши клиенты могут создавать новые учетные данные (электронный адрес и пароль или имя пользователя и пароль), которые мы называем "локальными учетными записями".

## Приступая к работе

Чтобы создать приложение с поддержкой регистрации и входа пользователей, такое приложение сначала нужно зарегистрировать с использованием клиента Azure Active Directory B2C. Получите собственный клиент, следуя указаниям в статье о [создании клиента Azure AD B2C](active-directory-b2c-get-started.md).

Вы можете создать приложение для службы Azure Active Directory B2C, выбрав отправку сообщений протокола напрямую, с помощью [OAuth 2.0](active-directory-b2c-reference-protocols.md#oauth2-authorization-code-flow) или [Open ID Connect](active-directory-b2c-reference-protocols.md#openid-connect-sign-in-flow) либо с помощью наших библиотек. Чтобы приступить к работе, выберите в следующей таблице предпочитаемую платформу.

[AZURE.INCLUDE [active-directory-b2c-quickstart-table](../../includes/active-directory-b2c-quickstart-table.md)]

## Новые возможности

Регулярно просматривайте указанные ниже страницы и узнавайте о будущих изменениях в Azure Active Directory B2C. Кроме того, мы будет сообщать о нововведениях в Twitter (учетная запись @AzureAD).

- Узнайте о нашей [платформе расширяемых политик](active-directory-b2c-reference-policies.md), а также о типах политик, которые можно создать и использовать в приложениях.
- Добавьте в закладки наш [блог службы](https://blogs.msdn.microsoft.com/azureadb2c/), чтобы получать уведомления о незначительных проблемах, обновлениях, состоянии службы и устраненных рисках. Кроме того, продолжайте отслеживать [панель мониторинга состояния Azure](https://azure.microsoft.com/status/).
- Текущие [ограничения для службы](active-directory-b2c-limitations.md).
- Наконец, [пример кода](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore-b2c), где используется Azure AD B2C и ASP.NET Core.

## Статьи с инструкциями

Узнайте, как использовать отдельные функции Azure Active Directory B2C:

- Настройка учетных записей [Facebook](active-directory-b2c-setup-fb-app.md), [Google+](active-directory-b2c-setup-goog-app.md), [Майкрософт](active-directory-b2c-setup-msa-app.md), [Amazon](active-directory-b2c-setup-amzn-app.md) и [LinkedIn](active-directory-b2c-setup-li-app.md) для использования в потребительских приложениях.
- [Используйте настраиваемые атрибуты для сбора данных о потребителях](active-directory-b2c-reference-custom-attr.md).
- [Предварительная версия Azure Active Directory B2C: включение многофакторной проверки подлинности в пользовательских приложениях](active-directory-b2c-reference-mfa.md).
- [Предварительная версия Azure Active Directory B2C: настройка самостоятельного сброса пароля для потребителей](active-directory-b2c-reference-sspr.md).
- [Настройка красивого и удобного интерфейса для регистрации и входа, а также других страниц, с которыми взаимодействуют клиенты](active-directory-b2c-reference-ui-customization.md), с использованием Azure Active Directory B2C.
- [Использование API Graph Azure Active Directory для программного создания, чтения, обновления и удаления пользователей](active-directory-b2c-devquickstarts-graph-dotnet.md) в клиенте Azure Active Directory B2C.

## Дальнейшие действия

Следующие ссылки позволят глубже изучить службу:

- См. страницу [цен на Azure Active Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).
- См. материалы на сайте Stack Overflow (Переполнение стека) по тегам [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) и [adal](http://stackoverflow.com/questions/tagged/adal).
- Сообщите нам свое мнение через сайт [пользовательских мнений](https://feedback.azure.com/forums/169401-azure-active-directory/). В заголовке сообщения укажите «AzureADB2C:», чтобы мы смогли его найти.
- См. статью [Предварительная версия Azure AD B2C: протоколы проверки подлинности](active-directory-b2c-reference-protocols.md).
- См. статью [Предварительная версия Azure AD B2C: справочник по маркерам](active-directory-b2c-reference-tokens.md).
- См. статью [Предварительная версия Azure Active Directory B2C: часто задаваемые вопросы](active-directory-b2c-faqs.md).
- [Предварительная версия Azure Active Directory B2C: регистрация запросов в службу поддержки](active-directory-b2c-support.md).

## Получение обновлений системы безопасности для наших продуктов

Рекомендуем вам настроить уведомления о нарушениях безопасности. Это можно сделать, подписавшись на уведомления безопасности консультационных служб на [этой странице](https://technet.microsoft.com/security/dd252948).

<!---HONumber=AcomDC_0727_2016-->