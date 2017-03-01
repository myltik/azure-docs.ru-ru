---
title: "Подключение к Azure для государственных организаций с помощью PowerShell | Документация Майкрософт"
description: "Сведения о подключении к подписке в Azure для государственных организаций с помощью PowerShell."
services: azure-government
cloud: gov
documentationcenter: 
author: zakramer
manager: liki
ms.assetid: 47e5e535-baa0-457e-8c41-f9fd65478b38
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 02/13/2017
ms.author: zakramer
translationtype: Human Translation
ms.sourcegitcommit: 6b29e98aa3458a76e186234c2e9800294902bea9
ms.openlocfilehash: 3a0594fa639a2ac34c53b2263dbf7dd2410cf16a
ms.lasthandoff: 02/21/2017


---



# <a name="connect-to-azure-government-with-powershell"></a>Подключение к Azure для государственных организаций с помощью PowerShell
Чтобы использовать интерфейс командной строки Azure (Azure CLI), необходимо подключиться к Azure для государственных организаций, а не к общедоступной версии Azure. Azure CLI можно использовать для управления большой подпиской с помощью сценария или для доступа к функциям, которые в настоящее время недоступны на портале Azure. Если вы использовали PowerShell в общедоступной версии Azure, вы не почувствуете разницы.  В Azure для государственных организаций имеются такие отличия:

* подключение к учетной записи;
* имена регионов.

> [!NOTE]
> Если вы еще не использовали PowerShell, см. статью [Introduction to Azure PowerShell](/powershell/azureps-cmdlets-docs) (Введение в Azure PowerShell).
> 
> 

При запуске PowerShell нужно сообщить Azure PowerShell о подключении к Azure для государственных организаций, указав параметр среды.  Параметр гарантирует подключение PowerShell к нужным конечным точкам.  Коллекция конечных точек определяется при входе в учетную запись.  Различным интерфейсам API требуются разные версии параметров среды:

| Тип подключения | Команда |
| --- | --- |
| Команды [Azure Active Directory (классическая модель развертывания)](https://msdn.microsoft.com/library/dn708504.aspx) |`Add-AzureAccount -Environment AzureUSGovernment` |
| Команды [управления ресурсами](https://msdn.microsoft.com/library/mt125356.aspx) |`Login-AzureRmAccount -EnvironmentName AzureUSGovernment` |
| Команды [Azure Active Directory](https://msdn.microsoft.com/library/azure/jj151815.aspx) |`Connect-MsolService -AzureEnvironment UsGovernment` |
| Команды [Azure Active Directory (модель развертывания с помощью Resource Manager)](https://msdn.microsoft.com/library/azure/mt757189.aspx) |`Connect-AzureAD -AzureEnvironmentName AzureUSGovernment` |
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

