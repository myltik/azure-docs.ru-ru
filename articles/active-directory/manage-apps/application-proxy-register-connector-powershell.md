---
title: Автоматическая установка соединителя прокси приложения Azure AD | Документация Майкрософт
description: Описывается, как выполнить автоматическую установку соединителя прокси приложения Azure AD для обеспечения безопасного удаленного доступа к локальным приложениям.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/17/2018
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 7cc51a3e16c476385fc360ea7f40826e21daaebc
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35292608"
---
# <a name="create-an-unattended-installation-script-for-the-azure-ad-application-proxy-connector"></a>Создание скрипта автоматической установки для соединителя прокси приложения Azure AD

Эта статья поможет вам создать скрипт Windows PowerShell, позволяющий выполнить автоматическую установку и регистрацию соединителя прокси приложения Azure AD.

Эта возможность полезна в тех случаях, когда требуется:

* установить соединитель на серверах Windows, на которых отключен пользовательский интерфейс или к которым не удается получить доступ через удаленный рабочий стол;
* установить и зарегистрировать много соединителей одновременно;
* интегрировать установку и регистрацию соединителя в другую процедуру;
* создать стандартный образ сервера, который содержит биты соединителя, но не зарегистрирован.

Для работы [соединителя прокси приложения](application-proxy-connectors.md) он должен быть зарегистрирован в вашем каталоге Azure AD с использованием пароля и имени глобального администратора. Обычно эти сведения вводятся во всплывающем окне во время установки соединителя, но для автоматизации этого процесса можно использовать PowerShell.

Этот процесс состоит из двух этапов. Сначала выполняется установка соединителя, а затем он регистрируется в Azure AD. 

## <a name="install-the-connector"></a>Установка соединителя
Чтобы установить соединитель без регистрации, сделайте следующее:

1. Откройте окно командной строки.
2. Выполните следующую команду, в которой параметр /q означает, что установка осуществляется в автоматическом режиме и вам не нужно принимать лицензионное соглашение.
   
        AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q

## <a name="register-the-connector-with-azure-ad"></a>Регистрация соединителя в Azure AD
Существуют два метода регистрации соединителя:

* регистрация соединителя с помощью объекта учетных данных Windows PowerShell;
* регистрация соединителя с помощью маркера, созданного в автономном режиме.

### <a name="register-the-connector-using-a-windows-powershell-credential-object"></a>Регистрация соединителя с помощью объекта учетных данных Windows PowerShell
1. Создайте объект учетных данных Windows PowerShell `$cred`, содержащий имя пользователя и пароль администратора для вашего каталога. Выполните следующую команду, заменив *\<username\>* и *\<password\>* на нужные имя пользователя и пароль:
   
        $User = "<username>"
        $PlainPassword = '<password>'
        $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword
2. Перейдите в каталог **C:\Program Files\Microsoft AAD App Proxy Connector** и запустите следующий скрипт с использованием созданного объекта `$cred`.
   
        .\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred -Feature ApplicationProxy

### <a name="register-the-connector-using-a-token-created-offline"></a>Регистрация соединителя с помощью маркера, созданного в автономном режиме
1. Создайте автономный маркер с помощью класса AuthenticationContext, используя значения, указанные во фрагменте кода или командлете PowerShell ниже:

    **Для C#:**

        using System;
        using System.Diagnostics;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;

        class Program
        {
        #region constants
        /// <summary>
        /// The AAD authentication endpoint uri
        /// </summary>
        static readonly Uri AadAuthenticationEndpoint = new Uri("https://login.microsoftonline.com/common/oauth2/token?api-version=1.0");

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

    **Для PowerShell:**

        # Locate AzureAD PowerShell Module
        # Change Name of Module to AzureAD after what you have installed
        $AADPoshPath = (Get-InstalledModule -Name AzureAD).InstalledLocation
        # Set Location for ADAL Helper Library
        $ADALPath = $(Get-ChildItem -Path $($AADPoshPath) -Filter Microsoft.IdentityModel.Clients.ActiveDirectory.dll -Recurse ).FullName | Select-Object -Last 1
        
        # Add ADAL Helper Library
        Add-Type -Path $ADALPath
        
        #region constants
        
        # The AAD authentication endpoint uri
        [uri]$AadAuthenticationEndpoint = "https://login.microsoftonline.com/common/oauth2/token?api-version=1.0/" 
        
        # The application ID of the connector in AAD
        [string]$ConnectorAppId = "55747057-9b5d-4bd4-b387-abf52a8bd489"
        
        # The reply address of the connector application in AAD
        [uri]$ConnectorRedirectAddress = "urn:ietf:wg:oauth:2.0:oob" 
        
        # The AppIdUri of the registration service in AAD
        [uri]$RegistrationServiceAppIdUri = "https://proxy.cloudwebappproxy.net/registerapp"
        
        #endregion
        
        #region GetAuthenticationToken
        
        # Set AuthN context
        $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $AadAuthenticationEndpoint
        
        # Build platform parameters
        $promptBehavior = [Microsoft.IdentityModel.Clients.ActiveDirectory.PromptBehavior]::Always
        $platformParam = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.PlatformParameters" -ArgumentList $promptBehavior
        
        # Do AuthN and get token
        $authResult = $authContext.AcquireTokenAsync($RegistrationServiceAppIdUri.AbsoluteUri, $ConnectorAppId, $ConnectorRedirectAddress, $platformParam).Result
        
        # Check AuthN result
        If (($authResult) -and ($authResult.AccessToken) -and ($authResult.TenantId) ) {
        $token = $authResult.AccessToken
        $tenantId = $authResult.TenantId
        }
        Else {
        Write-Output "Authentication result, token or tenant id returned are null"
        }
        
        #endregion

2. После создания маркера создайте с его помощью SecureString.

   `$SecureToken = $Token | ConvertTo-SecureString -AsPlainText -Force`

3. Выполните следующую команду Windows PowerShell, заменив \<tenant GUID\> идентификатором каталога.

   `.\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Token -Token $SecureToken -TenantId <tenant GUID> -Feature ApplicationProxy`

## <a name="next-steps"></a>Дополнительная информация 
* [Публикация приложений с помощью доменного имени](application-proxy-configure-custom-domain.md)
* [Включение единого входа](application-proxy-configure-single-sign-on-with-kcd.md)
* [Устранение неполадок с прокси приложения](application-proxy-troubleshoot.md)


