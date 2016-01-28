<properties
   pageTitle="Примеры кода Azure Active Directory | Microsoft Azure"
   description="Список примеров кода Azure Active Directory, сгруппированных по сценарию."
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="msmbaldwin"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="01/08/2016"
   ms.author="mbaldwin"/>

# Примеры кода Azure Active Directory

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Для добавления проверки подлинности и авторизации в веб-приложения и веб-интерфейсы API можно использовать Microsoft Azure Active Directory (Azure AD). Этот раздел перенаправит вас к примерам кода, в которых показано, как это можно сделать, и фрагментам кода, которые можно использовать в приложениях. На странице примеров кода находятся разделы с подробными файлами сведений, которые предоставят справку по требованиям, установке и настройке. Код указан с комментариями, чтобы можно было понять важные разделы.

Чтобы понять основной сценарий для каждого типа примера, см. раздел «Сценарии проверки подлинности в Azure AD».

Поделитесь своими примерами на GitHub: [Примеры Microsoft Azure Active Directory и документация](https://github.com/AzureADSamples).

## Из веб-браузера в веб-приложение

В этих примерах показано, как создать веб-приложение, которое направляет браузер пользователя для выполнения входа в Azure AD.



| Язык или платформа | Образец | Описание
| ----------------- | ------ | -----------
| C#/.NET | [WebApp-OpenIDConnect-DotNet](http://github.com/AzureADSamples/WebApp-OpenIDConnect-DotNet) | Использование OpenID Connect (ПО промежуточного слоя ASP.Net OpenID Connect OWIN) для проверки подлинности пользователей клиента Azure AD.
| C#/.NET | [WebApp-MultiTenant-OpenIdConnect-DotNet](https://github.com/AzureADSamples/WebApp-MultiTenant-OpenIdConnect-DotNet) | Мультитенантное веб-приложение .NET MVC, которое использует OpenID Connect (ПО промежуточного слоя ASP.Net OpenID Connect OWIN) для проверки подлинности пользователей нескольких клиентов Azure AD.
| C#/.NET | [WebApp-WSFederation-DotNet](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet) | Использование OpenID Connect (ПО промежуточного слоя ASP.Net OpenID Connect OWIN) для проверки подлинности пользователей клиента Azure AD.






## Одностраничное приложение (SPA)

В этом примере показано, как создать одностраничное приложение, защищенное с помощью Azure AD.

| Язык или платформа | Образец | Описание
| ----------------- | ------ | -----------
| JavaScript, C#/.NET | [SinglePageApp-DotNet](https://github.com/AzureADSamples/SinglePageApp-DotNet) | Использование ADAL для JavaScript и Azure AD для обеспечения безопасности одностраничного приложения на AngularJS с серверным веб-интерфейсом API, реализованным на ASP.NET.


## Из собственного приложения к веб-интерфейсу API

В этих примерах кода показано, как создавать собственные клиентские приложения, которые вызывают веб-интерфейсы API, защищенные Azure AD. Они используют [библиотеку проверки подлинности Azure AD (ADAL)](http://go.microsoft.com/fwlink/?LinkID=258232) и [OAuth 2.0 в Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx).

| Язык или платформа | Образец | Описание
| ----------------- | ------ | -----------
| JavaScript | [NativeClient-MultiTarget-Cordova](https://github.com/AzureADSamples/NativeClient-MultiTarget-Cordova) | Использование подключаемого модуля ADAL для Apache Cordova для построения приложения Apache Cordova, которое вызывает веб-API и использует Azure AD для проверки подлинности.
| C#/.NET | [NativeClient-DotNet](http://github.com/AzureADSamples/NativeClient-DotNet) | Приложение .NET WPF, которое вызывает веб-интерфейс API, защищенный с помощью Azure AD.
| C#/.NET | [NativeClient-WindowsStore](http://github.com/AzureADSamples/NativeClient-WindowsStore) | Приложение Магазина Windows, которое вызывает веб-интерфейс API, защищенный с помощью Azure AD.
| C#/.NET | [NativeClient-WebAPI-MultiTenant-WindowsStore](https://github.com/AzureADSamples/NativeClient-WebAPI-MultiTenant-WindowsStore) | Приложение Магазина Windows, которое вызывает мультитенантный веб-интерфейс API, защищенный с помощью Azure AD.
| C#/.NET | [WebAPI-OnBehalfOf-DotNet](http://github.com/AzureADSamples/WebAPI-OnBehalfOf-DotNet) | Собственное клиентское приложение, вызывающее веб-интерфейс API, который получает маркер для действия от имени первоначального пользователя, а затем использует маркер для вызова другого веб-интерфейса API.
| C#/.NET | [NativeClient-WindowsPhone8.1](https://github.com/AzureADSamples/NativeClient-WindowsPhone8.1) | Приложение Магазина Windows для Windows Phone 8.1, которое вызывает веб-интерфейс API, защищенный с помощью Azure AD.
| ObjC | [NativeClient-iOS](http://github.com/AzureADSamples/NativeClient-iOS) | Приложение iOS, которое вызывает веб-интерфейс API, необходимый для проверки подлинности Azure AD.
| C#/.NET | [WebAPI-ManuallyValidateJwt-DotNet](https://github.com/AzureADSamples/WebAPI-ManuallyValidateJwt-DotNet) | Собственное клиентское приложение, которое использует логику для обработки маркера JWT в веб-интерфейсе API вместо ПО промежуточного слоя OWIN.
| C#/Xamarin | [NativeClient-Xamarin-Android](https://github.com/AzureADSamples/NativeClient-Xamarin-Android) | Привязка Xamarin к собственной библиотеке проверки подлинности Azure AD (ADAL) для библиотеки Android.
| C#/Xamarin | [NativeClient-Xamarin-iOS](http://github.com/AzureADSamples/NativeClient-Xamarin-iOS) | Привязка Xamarin к собственной библиотеке проверки подлинности Azure AD (ADAL) для iOS.
| C#/Xamarin | [NativeClient-MultiTarget-DotNet](http://github.com/AzureADSamples/NativeClient-MultiTarget-DotNet) | Проект Xamarin, имеющий пять целевых платформ и выполняющий вызовы веб-интерфейса API, защищенного с помощью Azure AD.
| C#/.NET | [NativeClient-Headless-DotNet](http://github.com/AzureADSamples/NativeClient-Headless-DotNet) | Собственное приложение, имеющее пять целевых платформ и выполняющее вызовы веб-интерфейса API, защищенного с помощью Azure AD.



## Из веб-приложения к веб-интерфейсу API

В этих примерах кода показано, как использовать [OAuth 2.0 в Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx) для создания веб-приложений, которые вызывают веб-интерфейсы API, защищенные Azure AD.

| Язык или платформа | Образец | Описание
| ----------------- | ------ | -----------
| C#/.NET | [WebApp-WebAPI-OpenIDConnect-DotNet](http://github.com/AzureADSamples/WebApp-WebAPI-OpenIDConnect-DotNet) | Вызовите веб-API с разрешениями выполнившего вход пользователя.
| C#/.NET | [WebApp-WebAPI-OAuth2-AppIdentity-DotNet](http://github.com/AzureADSamples/WebApp-WebAPI-OAuth2-AppIdentity-DotNet) | Вызовите веб-API с разрешениями приложения.
| C#/.NET | [WebApp-WebAPI-OAuth2-UserIdentity-Dotnet](http://github.com/AzureADSamples/WebApp-WebAPI-OAuth2-UserIdentity-Dotnet) | Добавление авторизации с помощью [OAuth 2.0 в Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx) в существующее приложение, чтобы оно могло выполнять вызовы веб-интерфейса API.
| JavaScript | [WebAPI-Nodejs](http://github.com/AzureADSamples/WebAPI-Nodejs) | Настройка службы API REST, интегрированной с Azure AD, для защиты API. Включает сервер Node.js с веб-интерфейсом API.
| C#/.NET | [WebApp-WebAPI-MultiTenant-OpenIdConnect-DotNet](https://github.com/AzureADSamples/WebApp-WebAPI-MultiTenant-OpenIdConnect-DotNet) | Мультитенантное веб-приложение MVC, которое использует OpenID Connect (ПО промежуточного слоя ASP.Net OpenID Connect OWIN) для проверки подлинности пользователей Azure AD.  

## Из серверного приложения или управляющей программы в веб-интерфейс API

В этих примерах кода показано, как создать управляющую программу или серверное приложение, которое получает ресурсы из веб-интерфейса API с помощью библиотеки проверки подлинности [Azure AD (ADAL)](http://go.microsoft.com/fwlink/?LinkID=258232) и [OAuth 2.0 в Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx).

| Язык или платформа | Образец | Описание
| ----------------- | ------ | -----------
| C#/.NET | [Daemon-DotNet](http://github.com/AzureADSamples/Daemon-DotNet) | Консольное приложение, выполняющее вызовы веб-интерфейса API. Учетные данные клиента являются паролем.
| C#/.NET | [Daemon-CertificateCredential-DotNet](http://github.com/AzureADSamples/Daemon-CertificateCredential-DotNet) | Консольное приложение, выполняющее вызовы веб-интерфейса API. Учетные данные клиента являются сертификатом.


## Вызов Graph API Azure AD

В этих примерах кода показано, как создавать приложения, которые вызывают Graph API Azure AD для чтения и записи данных каталога.

| Язык или платформа | Образец | Описание
| ----------------- | ------ | -----------
| Java | [WebApp-GraphAPI-Java](http://github.com/AzureADSamples/WebApp-GraphAPI-Java) | Веб-приложение, которое использует Graph API для доступа к данным каталога Azure AD.
| PHP | [WebApp-GraphAPI-PHP](http://github.com/AzureADSamples/WebApp-GraphAPI-PHP) | Веб-приложение, которое использует Graph API для доступа к данным каталога Azure AD.
| C#/.NET | [WebApp-GraphAPI-DotNet](http://github.com/AzureADSamples/WebApp-GraphAPI-DotNet) | Веб-приложение, которое использует Graph API для доступа к данным каталога Azure AD.
| C#/.NET | [ConsoleApp-GraphAPI-DotNet](https://github.com/AzureADSamples/ConsoleApp-GraphAPI-DotNet) | Это консольное приложение демонстрирует вызовы методов чтения и записи в Graph API, а также назначение пользовательских лицензий и обновление фото в профиле пользователя и ссылок.
| C#/.NET | [ConsoleApp-GraphAPI-DiffQuery-DotNet](https://github.com/AzureADSamples/ConsoleApp-GraphAPI-DiffQuery-DotNet) | Консольное приложение, которое использует дифференциальный запрос в Graph API для получения периодических изменений объектов пользователей в клиенте Azure AD.
| C#/.NET | [WebApp-GraphAPI-DirectoryExtensions-DotNet](https://github.com/AzureADSamples/WebApp-GraphAPI-DirectoryExtensions-DotNet) | Приложение MVC, запрашивающее Graph API для создания простой диаграммы организации.
| PHP | [WebApp-GraphAPI-DirectoryExtensions-PHP](https://github.com/AzureADSamples/WebApp-GraphAPI-DirectoryExtensions-PHP) | Приложение PHP, которое вызывает Graph API для регистрации расширения, а затем чтения, обновления и удаления значений в атрибуте расширения.


## Авторизация

В этих примерах кода показано, как использовать Azure AD для авторизации.

| Язык или платформа | Образец | Описание
| ----------------- | ------ | -----------
| C#/.NET | [WebApp-GroupClaims-DotNet](https://github.com/AzureADSamples/WebApp-GroupClaims-DotNet) | Выполнение управления доступом на основе ролей (RBAC) с помощью утверждений о группах Azure Active Directory в приложении, которое интегрировано с Azure AD.
| C#/.NET | [WebApp-RoleClaims-DotNet](https://github.com/AzureADSamples/WebApp-RoleClaims-DotNet) | Выполнение управления доступом на основе ролей (RBAC) с помощью ролей приложения Azure Active Directory в приложении, которое интегрировано с Azure AD.


## Предыдущие пошаговые руководства

В этих пошаговых руководствах используется более старая технология, но она также может представлять интерес.

| Язык или платформа | Образец | Описание
| ----------------- | ------ | -----------
| C#/.NET | [Авторизация на основе ролей и на основе ACL в приложении Microsoft Azure AD](http://go.microsoft.com/fwlink/?LinkId=331694) | Выполнение авторизации на основе роли (RBAC) и авторизации на основе ACL в приложении, интегрированным Azure AD.
| C#/.NET | [AAL — приложение магазина Windows для службы REST — проверка подлинности](http://go.microsoft.com/fwlink/?LinkId=330605) | Использование библиотеки проверки подлинности [Azure AD (ADAL)](http://go.microsoft.com/fwlink/?LinkID=258232) (ранее AAL) для бета-версии Магазина Windows, чтобы добавить возможности проверки подлинности пользователя в приложение Магазина Windows.
| C#/.NET | [ADAL — собственное приложение для службы REST — проверка подлинности с помощью диалогового окна обозревателя AAD](http://go.microsoft.com/fwlink/?LinkId=259814) | Использование [библиотеки проверки подлинности Azure AD (ADAL)](http://go.microsoft.com/fwlink/?LinkID=258232) для добавления возможности проверки подлинности пользователя в клиент WPF.
| C#/.NET | [ADAL — собственное приложение для службы REST — проверка подлинности с помощью диалогового окна обозревателя ACS](http://code.msdn.microsoft.com/AAL-Native-App-to-REST-de57f2cc) | Использование [библиотеки проверки подлинности Azure AD (ADAL)](http://go.microsoft.com/fwlink/?LinkID=258232) и [2\.0 службы контроля доступа (ACS)](http://msdn.microsoft.com/library/azure/hh147631.aspx) для добавления возможности проверки подлинности пользователя в клиент WPF.
| C#/.NET | [ADAL — проверка подлинности между серверами](http://go.microsoft.com/fwlink/?LinkId=259816) | Использование библиотеки проверки подлинности [Azure AD (ADAL)](http://go.microsoft.com/fwlink/?LinkID=258232) для обеспечения безопасности вызовов из серверного процесса службы веб-интерфейса API REST MVC4.
| C#/.NET | [Добавление единого входа в веб-приложение с помощью Azure AD](https://msdn.microsoft.com/library/azure/dn151790.aspx) | Настройка приложения .NET в каталоге организации Azure AD для выполнения единого входа через Интернет.
| C#/.NET | [Разработка мультитенантных веб-приложений с помощью Azure AD](https://msdn.microsoft.com/library/azure/dn151789.aspx) | Использование Azure AD для добавления единого входа и возможности доступа к каталогу одного приложения .NET для работы в нескольких организациях.
JAVA | [Образец приложения Java для API Graph Azure AD](http://go.microsoft.com/fwlink/?LinkId=263969) | Использование API Graph для доступа к данным каталога из Azure AD.
PHP | [Образец приложения PHP для API Graph Azure AD](http://code.msdn.microsoft.com/PHP-Sample-App-For-Windows-228c6ddb) | Использование API Graph для доступа к данным каталога из Azure AD.
| C#/.NET | [Образец приложения для API Graph Azure AD](http://go.microsoft.com/fwlink/?LinkID=262648) | Использование API Graph для доступа к данным каталога из Azure AD.
| C#/.NET | [Образец приложения для дифференциального запроса Azure AD Graph](http://go.microsoft.com/fwlink/?LinkId=275398) | Использование дифференциального запроса в Graph API для получения периодических изменений объектов пользователей в клиенте Azure AD.
| C#/.NET | [Образец приложения для интеграции мультитенантного облачного приложения с Azure AD](http://go.microsoft.com/fwlink/?LinkId=275397) | Интеграция мультитенантного приложения в Azure AD.
| C#/.NET | [Обеспечение безопасности приложения Магазина Windows и веб-службы REST с помощью Azure AD (предварительная версия)](https://msdn.microsoft.com/library/azure/dn169448.aspx) | Создание простого ресурса веб-интерфейса API и приложения клиента Магазина Windows с помощью [Azure AD и библиотеки проверки подлинности Azure AD (ADAL](http://go.microsoft.com/fwlink/?LinkID=258232)).
| C#/.NET| [Использование API Graph для запроса Azure AD](https://msdn.microsoft.com/library/azure/dn151791.aspx) | Настройка приложения .NET для использования Graph API Azure AD для доступа к данным из каталога клиента Azure AD.

## См. также

##### Другие ресурсы

[Руководство разработчика по Azure Active Directory](active-directory-developers-guide.md)

[Библиотека вспомогательных методов для Azure AD Graph API](http://code.msdn.microsoft.com/Windows-Azure-AD-Graph-API-a8c72e18)

[Разработка современных приложений с помощью OAuth и служб федерации Active Directory](http://msdn.microsoft.com/library/dn633593.aspx)

<!----HONumber=AcomDC_0114_2016-->