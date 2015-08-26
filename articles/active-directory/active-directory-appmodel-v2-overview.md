<properties
	pageTitle="Модель приложений версии 2.0 | Microsoft Azure"
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
	ms.date="08/12/2015"
	ms.author="dastrock"/>

# Предварительная версия модели приложений 2.0. Вход для пользователей учетных записей Майкрософт и Azure AD в одном приложении

> [AZURE.NOTE]Эти сведения относятся к общедоступной предварительной версии модели приложений 2.0. Инструкции по интеграции с общедоступной службой Azure AD см. в статье [Руководство разработчика Azure Active Directory](active-directory-developers-guide.md).

Раньше разработчику приложения, поддерживающего как учетные записи Майкрософт, так и Azure Active Directory, необходимо было обеспечивать интеграцию с двумя отдельными системами. Модель приложений версии 2.0 поддерживает вход для пользователей обоих типов учетных записей. Одна простая интеграция позволяет охватить миллионы пользователей личных, рабочих и (или) учебных учетных записей.

Кроме того, приложения могут использовать [набор API REST Office 365](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2) на основе любого типа учетной записи. В настоящее время в число таких API входят API почтовой службы Outlook, контактов и календарей. В будущем будут добавлены и другие службы.
<!-- TODO: customer reference article -->
<!-- Several apps have already begun to bridge the gap between consumer and enterprise accounts, including: [Boomerang](), [TripIt](), & [Uber](). -->

Модель приложений 2.0 сейчас доступна в предварительной версии. В течение периода использования предварительной версии просим вас сообщать нам свое мнение и впечатления от работы с новой моделью приложения. С учетом ваших отзывов мы усовершенствуем это решение. До конца периода использования предварительной версии не используйте модель приложений 2.0 для выпуска производственных приложений.
<!-- TODO: Get approval on how it looks  -->

## Приступая к работе
Создать и запустить собственное приложение, используя модель приложений версии 2.0, можно одним из двух способов. Вы можете выбрать отправку сообщений протокола напрямую, используя [OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow) или [Open ID Connect](active-directory-v2-protocols.md#openid-connect-sign-in-flow). Второй вариант — возложить эту работу на наши библиотеки. Для этого выберите в приведенной ниже таблице желаемую платформу и приступайте к работе.
<!-- TODO: Finalize this table  -->

[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-v2-quickstart-table.md)]

## Новые возможности
Регулярно просматривайте указанные ниже страницы и узнавайте о будущих изменениях в общедоступной предварительной версии модели приложений 2.0. Кроме того, мы будет сообщать о нововведениях в Twitter под именем @AzureAD.

- Дополнительные сведения о [типах приложений, которые можно создавать с помощью модели приложений версии 2.0](active-directory-v2-flows.md).
- [Изменения в наших протоколах и отличия модели приложений версии 2.0](active-directory-v2-compare.md) для разработчиков, знакомых с Azure Active Directory.
- Действующие [пределы и ограничения предварительной версии](active-directory-v2-limitations.md).

## Справочные материалы
Следующие ссылки позволят глубже изучить платформу:

- Получите справку по переполнению стека, используя теги [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) или [adal](http://stackoverflow.com/questions/tagged/adal).
- Сообщите нам свое мнение о предварительной версии через сайт [пользовательских мнений](http://feedback.azure.com/forums/169401-azure-active-directory). В заголовке сообщения укажите AppModelv2, чтобы мы смогли его найти.
- [Справочник по протоколу модели приложений версии 2.0](active-directory-v2-protocols.md)
- [Справочник по маркеру модели приложений версии 2.0](active-directory-v2-tokens.md)
- [Справочник по API REST Office 365](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2)
- [Области и разрешения в конечной точке v2](active-directory-v2-scopes.md)

<!-- TODO: These articles
- [ADAL Library Reference]()
- [v2 Endpoint FAQs](active-directory-v2-faq.md)
-->

<!----HONumber=August15_HO7-->