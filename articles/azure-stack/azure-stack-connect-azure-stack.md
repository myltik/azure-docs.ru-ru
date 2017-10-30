---
title: "Подключение к Azure Stack | Документация Майкрософт"
description: "Узнайте, как подключиться к Azure Stack."
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 3cebbfa6-819a-41e3-9f1b-14ca0a2aaba3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/25/2017
ms.author: sngun
ms.openlocfilehash: ba2359739b1d9cd265879227a499c94f93d8e4c6
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/18/2017
---
# <a name="connect-to-azure-stack"></a>Подключение к Azure Stack

*Область применения: пакет SDK для Azure Stack*

Для управления ресурсами сначала необходимо подключиться к пакету средств разработки Azure Stack. В этой статье описаны действия, выполнив которые вы сможете подключиться к пакету средств разработки. Вы можете использовать один из следующих вариантов подключения:

* [Подключение к удаленному рабочему столу.](#connect-with-remote-desktop) При подключении с помощью удаленного рабочего стола один пользователь может быстро выполнить подключение из пакета средств разработки.
* [Виртуальная частная сеть (VPN).](#connect-with-vpn) При подключении с помощью VPN несколько пользователей могут одновременно подключаться с использованием клиентов за пределами инфраструктуры Azure Stack (требует установки).

<a name="connect-to-azure-stack-with-remote-desktop"></a>
##  <a name="connect-to-azure-stack-by-using-remote-desktop-connection"></a>Подключение к Azure Stack с помощью удаленного рабочего стола
С помощью подключения удаленного рабочего стола один пользователь может управлять ресурсами на портале оператора или пользовательском портале.

1. Откройте подключение к удаленному рабочему столу и подключитесь к пакету средств разработки. В качестве имени пользователя введите **AzureStack\AzureStackAdmin**. Используйте пароль оператора, указанный при настройке Azure Stack.  

2. Откройте диспетчер сервера на компьютере с пакетом средств разработки. Выберите **Локальный сервер**, снимите флажок **Internet Explorer Enhanced Security** (Усиленная безопасность Internet Explorer), а затем закройте диспетчер сервера.

3. Чтобы открыть [пользовательский портал](azure-stack-key-features.md#portal), перейдите к сайту https://portal.local.azurestack.external/. Войдите, используя учетные данные пользователя. Чтобы открыть [портал оператора](azure-stack-key-features.md#portal) Azure Stack, перейдите на сайт https://adminportal.local.azurestack.external/. Войдите, используя учетные данные Azure Active Directory (Azure AD), указанные во время установки.

<a name="connect-to-azure-stack-with-vpn"></a>
## <a name="connect-to-azure-stack-by-using-vpn"></a>Подключение к Azure Stack с помощью VPN

Вы можете установить VPN-подключение с разделенным туннелем к пакету средств разработки Azure Stack. Через VPN-подключение можно получить доступ к порталу оператора Azure Stack, порталу пользователя и установленным локально средствам, таким как Visual Studio и PowerShell, для управления ресурсами Azure Stack. VPN-подключение поддерживается в развертывании Azure AD и служб федерации Active Directory (AD FS). VPN-подключения позволяют нескольким клиентам одновременно подключаться к Azure Stack. 

> [!NOTE] 
> VPN-подключение не предоставляет возможность подключения к виртуальным машинам инфраструктуры Azure Stack. 

### <a name="prerequisites"></a>Предварительные требования

1. Установите [Azure PowerShell, совместимый с Azure Stack](azure-stack-powershell-install.md), на своем локальном компьютере.  
2. Скачайте [средства, необходимые для работы с Azure Stack](azure-stack-powershell-download.md). 

### <a name="set-up-vpn-connectivity"></a>Настройка VPN-подключения

Чтобы создать VPN-подключение к пакету средств разработки, откройте Windows PowerShell с правами администратора на локальном компьютере под управлением Windows. Затем выполните следующий скрипт (обновите значения IP-адреса и пароля для своей среды):

```PowerShell 
# Configure Windows Remote Management (WinRM), if it's not already configured.
winrm quickconfig  

Set-ExecutionPolicy RemoteSigned

# Import the Connect module.
Import-Module .\Connect\AzureStack.Connect.psm1 

# Add the development kit computer’s host IP address and certificate authority (CA) to the list of trusted hosts. Make sure you update the IP address and password values for your environment. 

$hostIP = "<Azure Stack host IP address>"

$Password = ConvertTo-SecureString `
  "<operator's password provided when deploying Azure Stack>" `
  -AsPlainText `
  -Force

Set-Item wsman:\localhost\Client\TrustedHosts `
  -Value $hostIP `
  -Concatenate

# Create a VPN connection entry for the local user.
Add-AzsVpnConnection `
  -ServerAddress $hostIP `
  -Password $Password

```

Если программа установки завершается успешно, **azurestack** появится в списке VPN-подключений.

![Сетевые подключения](media/azure-stack-connect-azure-stack/image3.png)  

### <a name="connect-to-azure-stack"></a>Подключение к Azure Stack

Подключитесь к экземпляру Azure Stack, используя один из следующих двух способов:  

* Использование команды `Connect-AzsVpn `: 
    
  ```PowerShell
  Connect-AzsVpn `
    -Password $Password
  ```

  При появлении запроса обозначьте узел Azure Stack как доверенный и установите сертификат из **AzureStackCertificateAuthority** в хранилище сертификатов на локальном компьютере (Запрос может быть скрыт окном PowerShell.) 

* На локальном компьютере выберите **Параметры сети** > **VPN** > **azurestack** > **Подключиться**. При запросе на вход введите имя пользователя (**AzureStack\AzureStackAdmin**) и пароль.

### <a name="test-vpn-connectivity"></a>Проверка VPN-подключения

Чтобы проверить подключение портала, откройте веб-браузер и перейдите на портал пользователя (https://portal.local.azurestack.external/) или портал оператора (https://adminportal.local.azurestack.external/). Войдите и создайте ресурсы.  

## <a name="next-steps"></a>Дальнейшие действия

[Make virtual machines available to your Azure Stack users](azure-stack-tutorial-tenant-vm.md) (Создание предложения по виртуальным машинам для пользователей Azure Stack)

