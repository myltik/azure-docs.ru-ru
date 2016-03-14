<properties
   pageTitle="Сведения о приложении Tailspin Surveys | Microsoft Azure"
   description="Обзор приложения Tailspin Surveys"
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="mwasson"/>

# Сведения о приложении Tailspin Surveys

Этот материал входит в [цикл статей]. К статьям прилагается полный [пример приложения].

Tailspin — это вымышленная компания, которая разрабатывает приложение SaaS с именем Surveys. Это приложение позволяет организациям создавать и публиковать интерактивные опросы.

- Организация может зарегистрироваться для использования приложения.
- После регистрации пользователи могут входить в приложение с помощью учетных данных организации.
- Они могут создавать, изменять и публиковать опросы.

> [AZURE.NOTE] Сведения о начале работы с приложением см. в статье [Запуск приложения Surveys].

На этом снимке экрана показана страница редактирования опроса:

![Изменение опроса](media/guidance-multitenant-identity/edit-survey.png)

Обратите внимание, что пользователь выполнил вход с помощью своего удостоверения организации — `bob@contoso.com`.

Пользователи могут просматривать опросы, созданные другими пользователями, в рамках одного клиента.

![Опросы клиентов](media/guidance-multitenant-identity/tenant-surveys.png)

Когда пользователь создает опрос, он может приглашать других пользователей в качестве участников опроса. Участники могут изменять опрос, но не могут удалять или публиковать его.

![Добавление участника](media/guidance-multitenant-identity/add-contributor.png)

Пользователь может добавлять участников из других клиентов, что позволяет совместно использовать ресурсы между клиентами. На этом снимке экрана Боб (`bob@contoso.com`) добавляет Элис (`alice@fabrikam.com`) в качестве участника в созданный им опрос.

При входе в систему Элис видит опрос в разделе "Опросы, участником которых я могу стать".

![Участник опроса](media/guidance-multitenant-identity/contributor.png)

Обратите внимание, что Элис входит в собственный клиент не как гость клиента Contoso. Элис имеет разрешения участника только для этого опроса — она не может просматривать другие опросы из клиента Contoso.

## Архитектура

Приложение Surveys состоит из веб-интерфейса и серверной части веб-API. Оба компонента реализованы с помощью [ASP.NET Core 1.0].

Для проверки подлинности веб-приложение использует Azure Active Directory (Azure AD). Веб-приложение также вызывает Azure AD для получения маркеров доступа OAuth 2 для веб-API. Маркеры доступа кэшируются в кэше Redis для Azure. Кэш позволяет нескольким экземплярам совместно использовать один кэш маркеров (например, в ферме серверов).

![Архитектура](media/guidance-multitenant-identity/architecture.png)

<!-- Links -->
[цикл статей]: guidance-multitenant-identity.md
[Запуск приложения Surveys]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md
[ASP.NET Core 1.0]: https://docs.asp.net/en/latest/
[пример приложения]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->