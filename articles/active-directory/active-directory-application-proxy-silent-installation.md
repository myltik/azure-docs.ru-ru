<properties
	pageTitle="Автоматическая установка соединителя прокси приложения Azure AD | Microsoft Azure"
	description="Описывается, как выполнить автоматическую установку соединителя прокси приложения Azure AD для обеспечения безопасного удаленного доступа к локальным приложениям."
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/06/2016"
	ms.author="kgremban"/>

# Автоматическая установка соединителя прокси приложения Azure AD

> [AZURE.NOTE]Прокси приложения — это функция, которая доступна только после обновления до выпуска Premium или Basic службы Azure Active Directory. Дополнительные сведения см. в статье [Выпуски Azure Active Directory](active-directory-editions.md).

Необходимо иметь возможность отправки сценария установки на несколько серверов Windows или на серверы Windows, на которых отключен пользовательский интерфейс. Этот раздел описывает создание сценария Windows PowerShell, позволяющего реализовать автоматическую установку, а также устанавливающего и регистрирующего соединитель прокси приложения Azure AD.

## Включение доступа
Прокси приложения работает путем установки в сети компактной службы Windows Server, называемой соединителем. Для работы соединителя прокси приложения он должен быть зарегистрирован в вашем каталоге Azure AD с использованием пароля и имени глобального администратора. Обычно эти сведения вводятся во всплывающем окне во время установки соединителя. Вместо этого можно использовать Windows PowerShell для создания объекта учетных данных для ввода сведений о регистрации; кроме того, можно создать собственный маркер и использовать его для ввода сведений о регистрации.

## Шаг 1. Установка соединителя без регистрации


Установите MSI-файлы соединителя без регистрации соединителя следующим образом.


1. Откройте окно командной строки.
2. Выполните следующую команду, в которой /q означает, что установка осуществляется в автоматическом режиме и не предлагает принять лицензионное соглашение.

        AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q

## Шаг 2. Регистрация соединителя в Azure Active Directory
Регистрацию соединителя можно выполнить с помощью любого из следующих методов.


- Регистрация соединителя с помощью объекта учетных данных Windows PowerShell
- Регистрация соединителя с помощью токена, созданного в автономном режиме

### Регистрация соединителя с помощью объекта учетных данных Windows PowerShell


1. Создайте объект учетных данных Windows PowerShell, выполнив следующую команду, где <username> и <password> следует заменить на имя пользователя и пароль для вашего каталога:

        $User = "<username>"
        $PlainPassword = '<password>'
        $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword

2. Перейдите в каталог **C:\\Program Files\\Microsoft AAD App Proxy Connector** и запустите сценарий с использованием созданного объекта учетных данных PowerShell: здесь $cred — имя созданного объекта учетных данных:

        RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred


### Регистрация соединителя с помощью токена, созданного в автономном режиме

1. Создайте автономный маркер с помощью класса AuthenticationContext, используя значения, указанные во фрагменте кода:


        using System;
        using System.Diagnostics;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;

        class Program
        {
        #region constants
        /// <summary>
        /// The AAD authentication endpoint uri
        /// </summary>
        static readonly Uri AadAuthenticationEndpoint = new Uri("https://login.windows.net/common/oauth2/token?api-version=1.0");

        /// <summary>
        /// The application ID of the connector in AAD
        /// </summary>
        static readonly string ConnectorAppId = "55747057-9b5d-4bd4-b387-abf52a8bd489";

        /// <summary>
        /// The reply address of the connector application in AAD
        /// </summary>
        static readonly Uri ConnectorRedirectAddress = new Uri("urn:ietf:wg:oauth:2.0:oob");

        /// <summary>
        /// The AppIdUri of the registration service in AAD
        /// </summary>
        static readonly Uri RegistrationServiceAppIdUri = new Uri("https://proxy.cloudwebappproxy.net/registerapp");

        #endregion

        #region private members
        private string token;
        private string tenantID;
        #endregion

        public void GetAuthenticationToken()
        {
            AuthenticationContext authContext = new AuthenticationContext(AadAuthenticationEndpoint.AbsoluteUri);

            AuthenticationResult authResult = authContext.AcquireToken(RegistrationServiceAppIdUri.AbsoluteUri,
                ConnectorAppId,
                ConnectorRedirectAddress,
                PromptBehavior.Always);

            if (authResult == null || string.IsNullOrEmpty(authResult.AccessToken) || string.IsNullOrEmpty(authResult.TenantId))
            {
                Trace.TraceError("Authentication result, token or tenant id returned are null");
                throw new InvalidOperationException("Authentication result, token or tenant id returned are null");
            }

            token = authResult.AccessToken;
            tenantID = authResult.TenantId;
        }





2. После создания токена создайте с его помощью SecureString: <br> `$SecureToken = $Token | ConvertTo-SecureString -AsPlainText -Force`
3. Выполните следующую команду Windows PowerShell, в которой SecureToken — это имя созданного выше маркера, а tenantID — идентификатор GUID клиента: <br> `RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules" -moduleName "AppProxyPSModule" -Authenticationmode Token -Token $SecureToken -TenantId <tenant GUID>`



## Что дальше?
У прокси приложения гораздо больше возможностей:


- [Публикация приложений с помощью доменного имени](active-directory-application-proxy-custom-domains.md)
- [Включение единого входа](active-directory-application-proxy-sso-using-kcd.md)
- [Работа с приложениями, поддерживающими утверждения](active-directory-application-proxy-claims-aware-apps.md)
- [Включение условного доступа](active-directory-application-proxy-conditional-access.md)


### Узнайте больше о прокси приложения
- [Просмотрите нашу справку в Интернете](active-directory-application-proxy-enable.md)
- [Ознакомьтесь с блогом о прокси приложения](http://blogs.technet.com/b/applicationproxyblog/)
- [Смотрите наши видео на Channel 9!](http://channel9.msdn.com/events/Ignite/2015/BRK3864)

## Дополнительные ресурсы
* [Регистрация организации в Azure](sign-up-organization.md)
* [Удостоверение Azure](fundamentals-identity.md)

<!---HONumber=AcomDC_0114_2016-->