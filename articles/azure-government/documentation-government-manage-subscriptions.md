---
title: "Подписки Azure для государственных организаций | Документация Майкрософт"
description: "Сведения об управлении подпиской в Azure для государственных организаций"
services: Azure-Government
cloud: gov
documentationcenter: 
author: zakramer
manager: liki
editor: 
ms.assetid: d3375e84-a37d-4e44-9040-70dbe08eabfc
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 10/21/2016
ms.author: zakramer
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 398abc29328adc179b860ab2cde5e6122f81779d


---
# <a name="managing-and-connecting-to-your-subscription-in-azure-government"></a>Управление подпиской в Azure для государственных организаций и подключение к ней
Azure для государственных организаций имеет уникальные URL-адреса и конечные точки для управления средой. Важно использовать надежные подключения для управления средой на портале или с помощью PowerShell. После подключения к среде Azure для государственных организаций обычные задачи по управлению службой выполняются, если компонент развернут.

## <a name="connecting-via-the-portal"></a>Подключение через портал
Портал — основной способ, который большинство людей используют для подключения к Azure для государственных организаций.  Для подключения перейдите на портал по адресу [https://portal.azure.us](https://portal.azure.us).  Для доступа к старой версии портала Azure можно перейти по адресу [https://manage.windowsazure.us](https://manage.windowsazure.us).

Чтобы создать подписку для учетной записи, подключитесь по адресу [https://account.windowsazure.us](https://account.windowsazure.us).

## <a name="connecting-via-powershell"></a>Подключение через PowerShell
Независимо от того, используете ли вы Azure PowerShell для управления большой подпиской с помощью сценария или для доступа к функциям, которые в настоящее время недоступны на портале Azure, нужно подключиться к Azure для государственных организаций, а не к общедоступной версии Azure.  Если вы использовали PowerShell в общедоступной версии Azure, вы не почувствуете разницы.  В Azure для государственных организаций имеются такие отличия:

* подключение к учетной записи;
* имена регионов.

> [!NOTE]
> Если вы еще не использовали PowerShell, см. статью [Introduction to Azure PowerShell](/powershell/azureps-cmdlets-docs) (Введение в Azure PowerShell).
> 
> 

При запуске PowerShell нужно сообщить Azure PowerShell о подключении к Azure для государственных организаций, указав параметр среды.  Параметр гарантирует подключение PowerShell к нужным конечным точкам.  Коллекция конечных точек определяется при входе в учетную запись.  Различным интерфейсам API требуются разные версии параметров среды:

| Тип подключения | Команда |
| --- | --- |
| Команды [управления службами](https://msdn.microsoft.com/library/dn708504.aspx) |`Add-AzureAccount -Environment AzureUSGovernment` |
| Команды [управления ресурсами](https://msdn.microsoft.com/library/mt125356.aspx) |`Login-AzureRmAccount -EnvironmentName AzureUSGovernment` |
| Команды [Azure Active Directory](https://msdn.microsoft.com/library/azure/jj151815.aspx) |`Connect-MsolService -AzureEnvironment UsGovernment` |
| Команды версии 2 [Azure Active Directory](https://msdn.microsoft.com/library/azure/mt757189.aspx) |`Connect-AzureAD -AzureEnvironmentName AzureUSGovernment` |
| [Командная строка CLI Azure](../xplat-cli-install.md) |`azure login –environment "AzureUSGovernment"` |

Параметр среды можно также использовать при подключении к учетной записи хранения, используя New-AzureStorageContext и указав AzureUSGovernment.

### <a name="determining-region"></a>Определение региона
После подключения есть одно дополнительное различие — регионы, используемые для выбора службы.  Каждое облако Azure имеет разные регионы.  Они указаны на странице доступности службы.  Как правило, регион используется в параметре расположения для команды.

Однако здесь есть один подвох.  Формат регионов Azure для государственных организаций отличается от формата общих имен регионов:

| Общее имя | Команда |
| --- | --- |
| Правительство штата Вирджиния |Правительство штата Вирджиния |
| Правительство штата Айова |Правительство штата Айова |

> [!NOTE]
> При использовании параметра расположения части US и Gov указываются слитно.
> 
> 

Если вам когда-нибудь понадобится проверить доступные регионы в Azure для государственных организаций, выполните следующие команды и выведите текущий список:

    Get-AzureLocation

Если вы хотите узнать, какие среды доступны в Azure, выполните следующую команду:

    Get-AzureEnvironment

## <a name="connecting-via-visual-studio"></a>Подключение с помощью Visual Studio
Разработчики используют Visual Studio, чтобы легко управлять своими подписками Azure при создании решений.  Сейчас в Visual Studio невозможно настроить подключение к Azure для государственных организаций через пользовательский интерфейс.  

### <a name="updating-visual-studio-for-azure-government"></a>Обновление Visual Studio для Azure для государственных организаций
Чтобы подключить Visual Studio к Azure для государственных организаций, необходимо обновить реестр.

1. Закройте Visual Studio.
2. Создайте текстовый файл с именем **VisualStudioForAzureGov.reg**.
3. Скопируйте и вставьте в файл **VisualStudioForAzureGov.reg** следующий текст:
   
        Windows Registry Editor Version 5.00
   
        [HKEY_CURRENT_USER\Software\Microsoft\VSCommon\ConnectedUser]
        "AadInstance"="https://login-us.microsoftonline.com/"
        "adaluri"="https://management.core.usgovcloudapi.net"
        "AzureRMEndpoint"="https://management.usgovcloudapi.net"
        "AzureRMAudienceEndpoint"="https://management.core.usgovcloudapi.net"
        "EnableAzureRMIdentity"="true"
        "GraphUrl"="graph.windows.net"
4. Сохраните и запустите файл, дважды щелкнув его.  Вам будет предложено объединить файл с реестром.
5. Запустите Visual Studio и начните использовать [Cloud Explorer](../vs-azure-tools-resources-managing-with-cloud-explorer.md).

> [!NOTE]
> После установки этого ключа реестра будут доступны только подписки Azure для государственных организаций.  Настроенные ранее подписки будут по-прежнему отображаться, но не будут работать, так как Visual Studio теперь подключен к Azure для государственных организаций вместо Azure Public.  Инструкции по отмене изменений см. в следующем разделе.
> 
> 

### <a name="reverting-visual-studio-connection-to-azure-government"></a>Отмена подключения Visual Studio к Azure для государственных организаций
Чтобы подключить Visual Studio к Azure Public, необходимо удалить параметры реестра, обеспечивающие подключение к Azure для государственных организаций.

1. Закройте Visual Studio.
2. Создайте текстовый файл с именем **VisualStudioForAzureGov_Remove.reg**.
3. Скопируйте и вставьте в файл **VisualStudioForAzureGov_Remove.reg** следующий текст:
   
        Windows Registry Editor Version 5.00
   
        [HKEY_CURRENT_USER\Software\Microsoft\VSCommon\ConnectedUser]
        "AadInstance"=-
        "adaluri"=-
        "AzureRMEndpoint"=-
        "AzureRMAudienceEndpoint"=-
        "EnableAzureRMIdentity"=-
        "GraphUrl"=-
4. Сохраните и запустите файл, дважды щелкнув его.  Вам будет предложено объединить файл с реестром.
5. Запустите Visual Studio.

> [!NOTE]
> После отмены изменения этого ключа реестра подписки Azure для государственных организаций будут отображаться, но будут недоступны.  Их можно безопасно удалить.
> 
> 

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения см. в этих статьях:

* [Документы PowerShell на GitHub](https://github.com/Azure/azure-powershell)
* [Step-by-step instruction on connecting to Resource Management](https://blogs.msdn.microsoft.com/azuregov/2015/10/08/configuring-arm-on-azure-gc/) (Пошаговые инструкции по подключению к управлению ресурсами)
* [Документы Azure PowerShell на портале MSDN](https://msdn.microsoft.com/library/mt619274.aspx)

Чтобы получать дополнительные сведения и обновления, подпишитесь на [блог Microsoft Azure для государственных организаций](https://blogs.msdn.microsoft.com/azuregov/).




<!--HONumber=Dec16_HO2-->


