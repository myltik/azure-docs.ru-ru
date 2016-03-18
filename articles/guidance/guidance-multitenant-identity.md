<properties
   pageTitle="Управление удостоверениями для мультитенантных приложений | Microsoft Azure"
   description="Рекомендации по аутентификации, авторизации и управлению удостоверениями в мультитенантных приложениях."
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

# Управление удостоверениями мультитенантных приложений в Microsoft Azure

В этом цикле описываются рекомендации по архитектуре обслуживания одним экземпляром приложения нескольких развертываний, когда Azure AD используется для аутентификации и управления удостоверениями.

При создании мультитенантного приложения одной из первейших задач является управление удостоверениями пользователей, так как теперь каждый пользователь относится к клиенту. Например:

- Пользователи входят с использованием учетных данных организации.
- У пользователей должен быть доступ к их данным в организации, но не к данным, относящимся к другим клиентам.
- Организация может зарегистрироваться для использования приложения, а затем назначить роли приложения своим участникам.

В Azure Active Directory (Azure AD) имеется несколько удобных функций, которые делают возможными все эти сценарии.

В дополнение к этому циклу статей мы также создали полную, [комплексную реализацию][tailspin] мультитенантного приложения. Статьи отражают то, что мы узнали в процессе создания приложения. Чтобы приступить к работе с приложением, см. статью [Запуск приложения Surveys](https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md).


## Оглавление

- [Введение](guidance-multitenant-identity-intro.md)
- [Сведения о приложении Tailspin Surveys](guidance-multitenant-identity-tailspin.md)
- [Аутентификация с помощью Azure AD](guidance-multitenant-identity-authenticate.md)
- [Работа с удостоверениями на основе утверждений](guidance-multitenant-identity-claims.md)
- [Регистрация и адаптации клиента](guidance-multitenant-identity-signup.md)
- [Определение ролей приложения и управление ими](guidance-multitenant-identity-app-roles.md)
- [Авторизация](guidance-multitenant-identity-authorize.md)
- [Обеспечение безопасности серверного веб-API](guidance-multitenant-identity-web-api.md)
- [Кэширование маркеров доступа OAuth2](guidance-multitenant-identity-token-cache.md)
- [Федерация со службой AD FS клиента](guidance-multitenant-identity-adfs.md)
- [Получение маркеров доступа из Azure AD с помощью утверждений клиентов](guidance-multitenant-identity-client-assertion.md)
- [Использование хранилища ключей для защиты секретов приложения](guidance-multitenant-identity-keyvault.md)

[tailspin]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->