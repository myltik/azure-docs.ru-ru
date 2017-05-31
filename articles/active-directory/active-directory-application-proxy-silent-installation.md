---
title: "Автоматическая установка соединителя прокси приложения Azure AD | Документация Майкрософт"
description: "Описывается, как выполнить автоматическую установку соединителя прокси приложения Azure AD для обеспечения безопасного удаленного доступа к локальным приложениям."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: harshja
ms.assetid: 3aa1c7f2-fb2a-4693-abd5-95bb53700cbb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2017
ms.author: kgremban
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: f4d72d4d11ee64e3431879f6ad1b5d8d091a0c87
ms.contentlocale: ru-ru
ms.lasthandoff: 05/15/2017


---
# <a name="silently-install-the-azure-ad-application-proxy-connector"></a>Автоматическая установка соединителя прокси приложения Azure AD
Необходимо иметь возможность отправки сценария установки на несколько серверов Windows или на серверы Windows, на которых отключен пользовательский интерфейс. Этот раздел поможет вам создать сценарий Windows PowerShell, позволяющий выполнить автоматическую установку и регистрацию соединитель прокси приложения Azure AD.

Эта возможность полезна в тех случаях, когда требуется:

* установить соединитель на компьютерах без уровня пользовательского интерфейса, или если не удается подключиться к компьютеру по протоколу RDP;
* установить и зарегистрировать много соединителей одновременно;
* интегрировать установку и регистрацию соединителя в другую процедуру;
* создать стандартный образ сервера, который содержит биты соединителя, но не зарегистрирован.

Прокси приложения работает путем установки в сети компактной службы Windows Server, называемой соединителем. Для работы соединителя прокси приложения он должен быть зарегистрирован в вашем каталоге Azure AD с использованием пароля и имени глобального администратора. Обычно эти сведения вводятся во всплывающем окне во время установки соединителя. Тем не менее с помощью Windows PowerShell можно создать объект учетных данных и указать в нем данные регистрации. Кроме того, вы можете создать собственный маркер и использовать его для ввода данных регистрации.

## <a name="install-the-connector"></a>Установка соединителя
Установите MSI-файлы соединителя без регистрации соединителя следующим образом.

1. Откройте окно командной строки.
2. Выполните следующую команду, в которой /q означает, что установка осуществляется в автоматическом режиме и не предлагает принять условия лицензионного соглашения.
   
        AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q

## <a name="register-the-connector-with-azure-ad"></a>Регистрация соединителя в Azure AD
Существуют два метода регистрации соединителя:

* регистрация соединителя с помощью объекта учетных данных Windows PowerShell;
* регистрация соединителя с помощью маркера, созданного в автономном режиме.

### <a name="register-the-connector-using-a-windows-powershell-credential-object"></a>Регистрация соединителя с помощью объекта учетных данных Windows PowerShell
1. Создайте объект учетных данных Windows PowerShell, выполнив следующую команду. Замените *\<username\>* и *\<password\>* именем пользователя и паролем для каталога.
   
        $User = "<username>"
        $PlainPassword = '<password>'
        $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword
2. Перейдите в каталог **C:\Program Files\Microsoft AAD App Proxy Connector** и запустите сценарий с использованием созданного объекта учетных данных PowerShell. Замените *$cred* именем созданного вами объекта учетных данных PowerShell.
   
        RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred

### <a name="register-the-connector-using-a-token-created-offline"></a>Регистрация соединителя с помощью маркера, созданного в автономном режиме
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


2. После создания маркера создайте с его помощью SecureString.

   `$SecureToken = $Token | ConvertTo-SecureString -AsPlainText -Force`

3. Выполните следующую команду Windows PowerShell, заменив \<tenant GUID\> идентификатором каталога.

   `RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Token -Token $SecureToken -TenantId <tenant GUID>`

## <a name="next-steps"></a>Дальнейшие действия 
* [Публикация приложений с помощью доменного имени](active-directory-application-proxy-custom-domains.md)
* [Включение единого входа](active-directory-application-proxy-sso-using-kcd.md)
* [Устранение неполадок с прокси приложения](active-directory-application-proxy-troubleshoot.md)



