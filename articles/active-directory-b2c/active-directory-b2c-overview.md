<properties
	pageTitle="Предварительная версия Azure Active Directory B2C: обзор | Microsoft Azure"
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
	ms.date="03/22/2016"
	ms.author="swkrish"/>

# Azure Active Directory B2C (предварительная версия): регистрация и вход пользователей в приложения

Azure Active Directory B2C — это комплексное облачное решение, позволяющее управлять удостоверениями в веб-приложениях и мобильных приложениях, с которыми взаимодействуют клиенты. Это высокодоступная глобальная служба, которая масштабируется до сотен миллионов идентификаторов потребителей. Так как служба Azure Active Directory B2C разработана на основе безопасной платформы корпоративного уровня, она защищает приложения, бизнес-процессы и пользовательские данные.

Раньше разработчикам приложений, которым было необходимо регистрировать потребителей и выполнять их вход в своих приложениях, приходилось писать собственный код. Кроме того, для хранения имен пользователей и паролей приходилось использовать локальные базы данных или системы. Azure Active Directory B2C предлагает разработчикам лучший способ интеграции функции управления идентификацией потребителей в свои приложения с помощью безопасной платформы, основанной на стандартах, и широкого набора расширяемых политик. Azure Active Directory B2C позволяет клиентам регистрироваться для использования ваших приложений с помощью существующих учетных записей в социальных сетях (Facebook, Google, Amazon, LinkedIn). Кроме того, ваши клиенты могут создавать новые учетные данные (электронный адрес и пароль или имя пользователя и пароль), которые мы называем "локальными учетными записями".

Сейчас доступна предварительная версия Azure Active Directory B2C. На этом этапе мы стремимся получить ваши предложения и отзывы о пробном использовании. С учетом ваших отзывов мы усовершенствуем это решение. До конца этого периода не выпускайте рабочие приложения, использующие предварительную версию. Сообщите нам свое мнение с помощью [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/).

## Приступая к работе

Чтобы создать приложение с поддержкой регистрации и входа пользователей, такое приложение сначала нужно зарегистрировать с использованием клиента Azure Active Directory B2C. Дополнительные сведения о получении собственного клиента см. в статье [Предварительная версия Azure Active Directory B2C: создание клиента Azure AD B2C](active-directory-b2c-get-started.md).

Вы можете создать приложение для службы Azure Active Directory B2C, выбрав отправку сообщений протокола напрямую, с помощью [OAuth 2.0](active-directory-b2c-reference-protocols.md#oauth2-authorization-code-flow) или [Open ID Connect](active-directory-b2c-reference-protocols.md#openid-connect-sign-in-flow) либо с помощью наших библиотек. Чтобы приступить к работе, выберите в следующей таблице предпочитаемую платформу.

[AZURE.INCLUDE [active-directory-b2c-quickstart-table](../../includes/active-directory-b2c-quickstart-table.md)]

## Новые возможности

Регулярно просматривайте указанные ниже страницы и узнавайте о будущих изменениях в предварительной версии Azure Active Directory B2C. Кроме того, мы будет сообщать о нововведениях в Twitter (учетная запись @AzureAD).

- Узнайте о нашей [платформе расширяемых политик](active-directory-b2c-reference-policies.md), а также о типах политик, которые можно создать и использовать в приложениях.
- Текущие [ограничения для предварительной версии](active-directory-b2c-limitations.md).

## Статьи с инструкциями

Узнайте, как использовать отдельные функции предварительной версии Azure Active Directory B2C:

- Настройка учетных записей [Facebook](active-directory-b2c-setup-fb-app.md), [Google+](active-directory-b2c-setup-goog-app.md), [Майкрософт](active-directory-b2c-setup-msa-app.md), [Amazon](active-directory-b2c-setup-amzn-app.md) и [LinkedIn](active-directory-b2c-setup-li-app.md) для использования в приложениях, с которыми взаимодействуют клиенты.
- [Используйте настраиваемые атрибуты для сбора данных о потребителях](active-directory-b2c-reference-custom-attr.md).
- [Предварительная версия Azure Active Directory B2C: включение многофакторной проверки подлинности в потребительских приложениях](active-directory-b2c-reference-mfa.md).
- [Предварительная версия Azure Active Directory B2C: настройка самостоятельного сброса пароля для пользователей](active-directory-b2c-reference-sspr.md).
- [Настройка красивого и удобного интерфейса для регистрации и входа, а также других страниц, с которыми взаимодействуют клиенты](active-directory-b2c-reference-ui-customization.md), с использованием Azure Active Directory B2C.
- [Использование API Graph Azure Active Directory для программного создания, чтения, обновления и удаления пользователей](active-directory-b2c-devquickstarts-graph-dotnet.md) в клиенте Azure Active Directory B2C.

## Дальнейшие действия

Следующие ссылки позволят глубже изучить службу:

- См. страницу [Цены на Azure Active Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).
- См. материалы на сайте Stack Overflow (Переполнение стека) [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) и [adal](http://stackoverflow.com/questions/tagged/adal).
- Сообщите нам свое мнение о предварительной версии на сайте [User Voice](https://feedback.azure.com/forums/169401-azure-active-directory/). Мы будем рады обратной связи. В заголовке сообщения укажите «AzureADB2C:», чтобы мы смогли его найти.
- Azure Active Directory B2C поддерживает стандартные отраслевые протоколы OpenID Connect и OAuth 2.0, используя модель регистрации приложений (модель приложений версии 2.0).
  - [Справочник по протоколу модели приложений версии 2.0](active-directory-b2c-reference-protocols.md)
  - [Справочник по маркеру модели приложений версии 2.0](active-directory-b2c-reference-tokens.md)
- См. статью [Предварительная версия Azure Active Directory B2C: часто задаваемые вопросы](active-directory-b2c-faqs.md).
- [Предварительная версия Azure Active Directory B2C: регистрация запросов на поддержку для Azure Active Directory B2C](active-directory-b2c-support.md).

<!---HONumber=AcomDC_0330_2016-->