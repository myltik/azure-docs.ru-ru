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
	ms.topic="article"
	ms.date="01/06/2016"
	ms.author="swkrish"/>

# Предварительная версия Azure Active Directory B2C: регистрация и вход потребителей в приложения

**Azure Active Directory B2C** — комплексное облачное решение для управления идентификацией в потребительских веб-приложениях и мобильных приложениях. Это высокодоступная глобальная служба, которая масштабируется до сотен миллионов идентификаторов потребителей. Основанная на платформе безопасности уровня предприятия, служба Azure Active Directory B2C защищает ваши приложения, бизнес и потребителей.

Раньше разработчикам приложений, которым было необходимо регистрировать потребителей и выполнять их вход в своих приложениях, приходилось писать собственный код. Кроме того, для хранения имен пользователей и паролей приходилось использовать локальные базы данных или системы. Azure Active Directory B2C предлагает разработчикам лучший способ интеграции функции управления идентификацией потребителей в свои приложения с помощью безопасной платформы, основанной на стандартах, и широкого набора расширяемых политик. Azure Active Directory B2C позволяет потребителям регистрироваться для использования ваших приложений с помощью существующих учетных записей в социальных сетях (Facebook, Google, Amazon, LinkedIn) или создавая новые учетные данные (электронный адрес и пароль или имя пользователя и пароль). Последние мы называем «локальными учетными записями».

Сейчас доступна предварительная версия Azure Active Directory B2C. На этом этапе мы стремимся получить ваши предложения и отзывы о пробном использовании. С учетом ваших отзывов мы усовершенствуем это решение. До конца этого периода не используйте предварительную версию для выпуска рабочих приложений. Сообщите нам свое мнение с помощью [UserVoice](http://feedback.azure.com/forums/169401-azure-active-directory).

## Приступая к работе

Чтобы создать приложение, в котором можно выполнять регистрацию и вход потребителей, сначала необходимо зарегистрировать его для использования клиента Azure Active Directory B2C. Получите собственный клиент, как описано в этой [статье](active-directory-b2c-get-started.md).

Вы можете создать приложение для службы Azure Active Directory B2C, выбрав отправку сообщений протокола напрямую, с помощью [OAuth 2.0](active-directory-b2c-reference-protocols.md#oauth2-authorization-code-flow), [Open ID Connect](active-directory-b2c-reference-protocols.md#openid-connect-sign-in-flow) или наших библиотек. Выберите ниже избранную платформу и приступите к работе.

[AZURE.INCLUDE [active-directory-b2c-quickstart-table](../../includes/active-directory-b2c-quickstart-table.md)]

## Новые возможности

Регулярно просматривайте указанные ниже страницы и узнавайте о будущих изменениях в предварительной версии Azure Active Directory B2C. Кроме того, мы будет сообщать о нововведениях в Twitter под именем @AzureAD.

- Узнайте о нашей [платформе расширяемых политик](active-directory-b2c-reference-policies.md) и типах политик, которые можно создать и использовать в приложениях.
- Действующие [пределы и ограничения предварительной версии](active-directory-b2c-limitations.md).

## Практические руководства

Узнайте, как использовать отдельные функции предварительной версии Azure Active Directory B2C:

- Настройте учетные записи ([Facebook](active-directory-b2c-setup-fb-app.md), [Google+](active-directory-b2c-setup-goog-app.md), [учетная запись Майкрософт](active-directory-b2c-setup-msa-app.md), [Amazon](active-directory-b2c-setup-amzn-app.md) и [LinkedIn](active-directory-b2c-setup-li-app.md)) для использования в потребительских приложениях.
- [Используйте настраиваемые атрибуты для сбора данных о потребителях](active-directory-b2c-reference-custom-attr.md).
- [Включите многофакторную проверку подлинности в потребительских приложениях](active-directory-b2c-reference-mfa.md).
- [Настройте самостоятельный сброс паролей для потребителей](active-directory-b2c-reference-sspr.md).
- [Настройте внешний вид и удобство использования страницы регистрации, страницы входа и других страниц, доступных потребителям](active-directory-b2c-reference-ui-customization.md), которые обслуживает Azure Active Directory B2C.
- [Используйте API Graph Azure Active Directory для программного создания, чтения, обновления и удаления потребителей](active-directory-b2c-devquickstarts-graph-dotnet.md) в клиенте Azure Active Directory B2C.

## Справочные материалы

Следующие ссылки позволят глубже изучить службу:

- См. [сведения о ценах на Active Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c).
- Получите справку по переполнению стека, используя теги [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) или [adal](http://stackoverflow.com/questions/tagged/adal).
- Сообщите нам свое мнение о предварительной версии через сайт [пользовательских мнений](http://feedback.azure.com/forums/169401-azure-active-directory). В заголовке сообщения укажите «AzureADB2C:», чтобы мы смогли его найти.
- Azure Active Directory B2C поддерживает стандартные отраслевые протоколы, OpenID Connect и OAuth 2.0, с помощью модели регистрации приложений «Модель приложений 2.0».
  - [Справочник по протоколу модели приложений версии 2.0](active-directory-b2c-reference-protocols.md)
  - [Справочник по маркеру модели приложений версии 2.0](active-directory-b2c-reference-tokens.md)
- [Часто задаваемые вопросы о Azure Active Directory B2C](active-directory-b2c-faqs.md)
- [Регистрация запросов на поддержку для Azure Active Directory B2C](active-directory-b2c-support.md)

<!---HONumber=AcomDC_0107_2016-->