---
title: Управление Azure CDN с помощью PowerShell | Документация Майкрософт
description: Узнайте, как использовать командлеты Azure PowerShell для управления Azure CDN.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: fb6f57a5-6e26-4847-8fd9-b51fb05a79eb
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 5634ecdec04f023d9eb901c4ad0fb21b13bcfdc1
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/19/2018
ms.locfileid: "31592440"
---
# <a name="manage-azure-cdn-with-powershell"></a>Управление Azure CDN с помощью PowerShell
PowerShell — это одно из самых гибких средств управления профилями и конечными точками Azure CDN.  PowerShell можно использовать интерактивно или подготовить скрипты для автоматизации задач управления.  В этом руководстве описано несколько распространенных задач по управлению профилями и конечными точками Azure CDN, которые можно выполнять с помощью PowerShell.

## <a name="prerequisites"></a>предварительным требованиям
Чтобы использовать PowerShell для управления профилями и конечными точками Azure CDN, прежде всего нужно установить модуль Azure PowerShell.  Сведения об установке Azure PowerShell и подключении к Azure с помощью командлета `Connect-AzureRmAccount` см. в статье [How to install and configure Azure PowerShell](/powershell/azure/overview) (Установка и настройка Azure PowerShell).

> [!IMPORTANT]
> Чтобы выполнять командлеты Azure PowerShell, необходимо сначала войти в систему с помощью `Connect-AzureRmAccount`.
> 
> 

## <a name="listing-the-azure-cdn-cmdlets"></a>Получение списка командлетов Azure CDN
Полный список командлетов Azure CDN можно получить с помощью `Get-Command` .

```text
PS C:\> Get-Command -Module AzureRM.Cdn

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Get-AzureRmCdnCustomDomain                         2.0.0      AzureRm.Cdn
Cmdlet          Get-AzureRmCdnEndpoint                             2.0.0      AzureRm.Cdn
Cmdlet          Get-AzureRmCdnEndpointNameAvailability             2.0.0      AzureRm.Cdn
Cmdlet          Get-AzureRmCdnOrigin                               2.0.0      AzureRm.Cdn
Cmdlet          Get-AzureRmCdnProfile                              2.0.0      AzureRm.Cdn
Cmdlet          Get-AzureRmCdnProfileSsoUrl                        2.0.0      AzureRm.Cdn
Cmdlet          New-AzureRmCdnCustomDomain                         2.0.0      AzureRm.Cdn
Cmdlet          New-AzureRmCdnEndpoint                             2.0.0      AzureRm.Cdn
Cmdlet          New-AzureRmCdnProfile                              2.0.0      AzureRm.Cdn
Cmdlet          Publish-AzureRmCdnEndpointContent                  2.0.0      AzureRm.Cdn
Cmdlet          Remove-AzureRmCdnCustomDomain                      2.0.0      AzureRm.Cdn
Cmdlet          Remove-AzureRmCdnEndpoint                          2.0.0      AzureRm.Cdn
Cmdlet          Remove-AzureRmCdnProfile                           2.0.0      AzureRm.Cdn
Cmdlet          Set-AzureRmCdnEndpoint                             2.0.0      AzureRm.Cdn
Cmdlet          Set-AzureRmCdnOrigin                               2.0.0      AzureRm.Cdn
Cmdlet          Set-AzureRmCdnProfile                              2.0.0      AzureRm.Cdn
Cmdlet          Start-AzureRmCdnEndpoint                           2.0.0      AzureRm.Cdn
Cmdlet          Stop-AzureRmCdnEndpoint                            2.0.0      AzureRm.Cdn
Cmdlet          Test-AzureRmCdnCustomDomain                        2.0.0      AzureRm.Cdn
Cmdlet          Unpublish-AzureRmCdnEndpointContent                2.0.0      AzureRm.Cdn
```

## <a name="getting-help"></a>Получение справки
Справку по любому из этих командлетов можно получить с помощью `Get-Help` .  `Get-Help` описывает назначение и синтаксис командлетов, а для некоторых из них приводит примеры.

```text
PS C:\> Get-Help Get-AzureRmCdnProfile

NAME
    Get-AzureRmCdnProfile

SYNOPSIS
    Gets an Azure CDN profile.


SYNTAX
    Get-AzureRmCdnProfile [-ProfileName <String>] [-ResourceGroupName <String>] [-InformationAction
    <ActionPreference>] [-InformationVariable <String>] [<CommonParameters>]


DESCRIPTION
    Gets an Azure CDN profile and all related information.


RELATED LINKS

REMARKS
    To see the examples, type: "get-help Get-AzureRmCdnProfile -examples".
    For more information, type: "get-help Get-AzureRmCdnProfile -detailed".
    For technical information, type: "get-help Get-AzureRmCdnProfile -full".

```

## <a name="listing-existing-azure-cdn-profiles"></a>Получение списка существующих профилей Azure CDN
Командлет `Get-AzureRmCdnProfile` , запущенный без параметров, возвращает все существующие CDN профили.

```powershell
Get-AzureRmCdnProfile
```

Этот результат можно передавать в командлеты для перечисления.

```powershell
# Output the name of all profiles on this subscription.
Get-AzureRmCdnProfile | ForEach-Object { Write-Host $_.Name }

# Return only **Azure CDN from Verizon** profiles.
Get-AzureRmCdnProfile | Where-Object { $_.Sku.Name -eq "StandardVerizon" }
```

Также можно получить отдельный профиль, указав имя профиля и группу ресурсов.

```powershell
Get-AzureRmCdnProfile -ProfileName CdnDemo -ResourceGroupName CdnDemoRG
```

> [!TIP]
> Несколько профилей CDN могут иметь одинаковые имена, если они находятся в разных группах ресурсов.  Исключив параметр `ResourceGroupName` , вы получите все профили с указанным именем.
> 
> 

## <a name="listing-existing-cdn-endpoints"></a>Получение списка существующих конечных точек CDN
`Get-AzureRmCdnEndpoint` позволяет получить отдельную конечную точку или все конечные точки в профиле.  

```powershell
# Get a single endpoint.
Get-AzureRmCdnEndpoint -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo

# Get all of the endpoints on a given profile. 
Get-AzureRmCdnEndpoint -ProfileName CdnDemo -ResourceGroupName CdnDemoRG

# Return all of the endpoints on all of the profiles.
Get-AzureRmCdnProfile | Get-AzureRmCdnEndpoint

# Return all of the endpoints in this subscription that are currently running.
Get-AzureRmCdnProfile | Get-AzureRmCdnEndpoint | Where-Object { $_.ResourceState -eq "Running" }
```

## <a name="creating-cdn-profiles-and-endpoints"></a>Создание конечных точек и профилей CDN
Для создания конечных точек и профилей CDN используются командлеты `New-AzureRmCdnProfile` и `New-AzureRmCdnEndpoint`.

```powershell
# Create a new profile
New-AzureRmCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Sku StandardAkamai -Location "Central US"

# Create a new endpoint
New-AzureRmCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Location "Central US" -EndpointName cdnposhdoc -OriginName "Contoso" -OriginHostName "www.contoso.com"

# Create a new profile and endpoint (same as above) in one line
New-AzureRmCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Sku StandardAkamai -Location "Central US" | New-AzureRmCdnEndpoint -EndpointName cdnposhdoc -OriginName "Contoso" -OriginHostName "www.contoso.com"

```

## <a name="checking-endpoint-name-availability"></a>Проверка доступности имени конечной точки
`Get-AzureRmCdnEndpointNameAvailability` возвращает объект с информацией о том, свободно ли указанное имя конечной точки.

```powershell
# Retrieve availability
$availability = Get-AzureRmCdnEndpointNameAvailability -EndpointName "cdnposhdoc"

# If available, write a message to the console.
If($availability.NameAvailable) { Write-Host "Yes, that endpoint name is available." }
Else { Write-Host "No, that endpoint name is not available." }
```

## <a name="adding-a-custom-domain"></a>Добавление пользовательского домена
`New-AzureRmCdnCustomDomain` добавляет пользовательское имя домена для существующей конечной точки.

> [!IMPORTANT]
> Необходимо настроить запись CNAME у вашего поставщика DNS, как описано в статье [Как сопоставить личный домен с конечной точкой сети доставки содержимого (CDN)](cdn-map-content-to-custom-domain.md).  Это сопоставление можно проверить с помощью `Test-AzureRmCdnCustomDomain`до изменения конечной точки.
> 
> 

```powershell
# Get an existing endpoint
$endpoint = Get-AzureRmCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Check the mapping
$result = Test-AzureRmCdnCustomDomain -CdnEndpoint $endpoint -CustomDomainHostName "cdn.contoso.com"

# Create the custom domain on the endpoint
If($result.CustomDomainValidated){ New-AzureRmCdnCustomDomain -CustomDomainName Contoso -HostName "cdn.contoso.com" -CdnEndpoint $endpoint }
```

## <a name="modifying-an-endpoint"></a>Изменение конечной точки
`Set-AzureRmCdnEndpoint` изменяет существующую конечную точку.

```powershell
# Get an existing endpoint
$endpoint = Get-AzureRmCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Set up content compression
$endpoint.IsCompressionEnabled = $true
$endpoint.ContentTypesToCompress = "text/javascript","text/css","application/json"

# Save the changed endpoint and apply the changes
Set-AzureRmCdnEndpoint -CdnEndpoint $endpoint
```

## <a name="purgingpre-loading-cdn-assets"></a>Очистка и предварительная загрузка ресурсов CDN
`Unpublish-AzureRmCdnEndpointContent` очищает все кэшированные ресурсы, а `Publish-AzureRmCdnEndpointContent` предварительно загружает ресурсы на всех поддерживаемых конечных точках.

```powershell
# Purge some assets.
Unpublish-AzureRmCdnEndpointContent -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo -PurgeContent "/images/kitten.png","/video/rickroll.mp4"

# Pre-load some assets.
Publish-AzureRmCdnEndpointContent -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo -LoadContent "/images/kitten.png","/video/rickroll.mp4"

# Purge everything in /images/ on all endpoints.
Get-AzureRmCdnProfile | Get-AzureRmCdnEndpoint | Unpublish-AzureRmCdnEndpointContent -PurgeContent "/images/*"
```

## <a name="startingstopping-cdn-endpoints"></a>Запуск и остановка конечных точек CDN
`Start-AzureRmCdnEndpoint` и `Stop-AzureRmCdnEndpoint` позволяют запускать и останавливать отдельные конечные точки или группы конечных точек.

```powershell
# Stop the cdndocdemo endpoint
Stop-AzureRmCdnEndpoint -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo

# Stop all endpoints
Get-AzureRmCdnProfile | Get-AzureRmCdnEndpoint | Stop-AzureRmCdnEndpoint

# Start all endpoints
Get-AzureRmCdnProfile | Get-AzureRmCdnEndpoint | Start-AzureRmCdnEndpoint
```

## <a name="deleting-cdn-resources"></a>Удаление ресурсов CDN
`Remove-AzureRmCdnProfile` и `Remove-AzureRmCdnEndpoint` позволяют удалять профили и конечные точки.

```powershell
# Remove a single endpoint
Remove-AzureRmCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Remove all the endpoints on a profile and skip confirmation (-Force)
Get-AzureRmCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG | Get-AzureRmCdnEndpoint | Remove-AzureRmCdnEndpoint -Force

# Remove a single profile
Remove-AzureRmCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG
```

## <a name="next-steps"></a>Дальнейшие действия
Узнайте об автоматизации Azure CDN с помощью [.NET](cdn-app-dev-net.md) или [Node.js](cdn-app-dev-node.md).

Сведения о функциях CDN см. в статье [Общие сведения о сети доставки содержимого (CDN) Azure](cdn-overview.md).

