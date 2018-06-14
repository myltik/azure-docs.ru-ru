---
services: virtual-machines
title: Настройка PowerShell
author: JoeDavies-MSFT
solutions: ''
manager: timlt
editor: tysonn
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 05/12/2015
ms.author: rasquill
ms.openlocfilehash: 19c704d965ff3e2fc9ac8c5b623aeb386cb0b974
ms.sourcegitcommit: e19f6a1709b0fe0f898386118fbef858d430e19d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/16/2018
ms.locfileid: "26730521"
---
## <a name="setting-up-powershell"></a>Настройка PowerShell
Прежде чем использовать Azure PowerShell, выполните следующие действия.

### <a name="verify-powershell-versions"></a>Проверка версий PowerShell
Чтобы использовать Windows PowerShell, необходимо установить Windows PowerShell версии 3.0 или 4.0. Чтобы узнать версию Windows PowerShell, введите в командной строке Windows PowerShell такую команду:

    $PSVersionTable

Вы увидите нечто вроде этого:

    Name                           Value
    ----                           -----
    PSVersion                      3.0
    WSManStackVersion              3.0
    SerializationVersion           1.1.0.1
    CLRVersion                     4.0.30319.18444
    BuildVersion                   6.2.9200.16481
    PSCompatibleVersions           {1.0, 2.0, 3.0}
    PSRemotingProtocolVersion      2.2

Убедитесь, что в строке **PSVersion** указано значение 3.0 или 4.0. Сведения об установке совместимой версии см. в разделе [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) или [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855).

Также необходимо установить Azure PowerShell версии 0.8.0 или выше. Установленную версию Azure PowerShell можно узнать в командной строке Azure PowerShell с помощью такой команды:

    Get-Module azure | format-table version

Вы увидите нечто вроде этого:

    Version
    -------
    0.8.16.1

Инструкции и ссылку на последнюю версию см. в статье [Установка и настройка служб Azure PowerShell](/powershell/azureps-cmdlets-docs).

### <a name="set-your-azure-account-and-subscription"></a>Настройка учетной записи и подписки Azure
Если у вас нет подписки Azure, вы можете активировать [преимущества для подписчиков MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) или зарегистрироваться в [бесплатной пробной версии](https://azure.microsoft.com/pricing/free-trial/).

Откройте окно командной строки Azure PowerShell и войдите в систему Azure с помощью такой команды:

    Add-AzureAccount

Если у вас есть несколько подписок Azure, их список можно получить с помощью такой команды:

    Get-AzureSubscription

Появятся такие сведения:

    SubscriptionId            : fd22919d-eaca-4f2b-841a-e4ac6770g92e
    SubscriptionName          : Visual Studio Ultimate with MSDN
    Environment               : AzureCloud
    SupportedModes            : AzureServiceManagement,AzureResourceManager
    DefaultAccount            : johndoe@contoso.com
    Accounts                  : {johndoe@contoso.com}
    IsDefault                 : True
    IsCurrent                 : True
    CurrentStorageAccountName : 
    TenantId                  : 32fa88b4-86f1-419f-93ab-2d7ce016dba7

Укажите текущую подписку Azure, выполнив в командной строке Azure PowerShell такие команды: Замените все содержимое внутри кавычек, включая знаки < и >, правильным именем.

    $subscr="<SubscriptionName from the display of Get-AzureSubscription>"
    Select-AzureSubscription -SubscriptionName $subscr -Current    

Дополнительные сведения о подписках и учетных записях Azure см. в разделе [Шаг 3. Подключение](/powershell/azureps-cmdlets-docs#Connect).

