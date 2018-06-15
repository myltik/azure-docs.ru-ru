---
title: Использование PowerShell для управления диспетчером трафика в Azure | Документация Майкрософт
description: Использование PowerShell для диспетчера трафика в Azure Resource Manager
services: traffic-manager
documentationcenter: na
author: kumudd
manager: timlt
ms.assetid: bc247448-1d2e-4104-ac03-42b59ebde065
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: kumud
ms.openlocfilehash: 951e845e23a1ed0cbdc83fc24a97a545f00c52ad
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2018
ms.locfileid: "31526647"
---
# <a name="using-powershell-to-manage-traffic-manager"></a>Использование PowerShell для управления диспетчером трафика

Azure Resource Manager представляет собой предпочтительный интерфейс управления для служб в Azure. Профилями диспетчера трафика Azure можно управлять с помощью интерфейсов API и инструментов на основе Azure Resource Manager.

## <a name="resource-model"></a>Модель ресурсов

Для настройки диспетчера трафика используется набор параметров, которые называются профилем. Профиль содержит параметры DNS, параметры маршрутизации трафика, параметры отслеживания конечных точек, а также список конечных точек службы, на которые выполняется маршрутизация трафика.

Каждый профиль диспетчера трафика представлен ресурсом типа TrafficManagerProfiles. На уровне API REST каждый профиль имеет следующий URI:

    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/trafficManagerProfiles/{profile-name}?api-version={api-version}

## <a name="setting-up-azure-powershell"></a>Настройка Azure PowerShell

В данных инструкциях используется Microsoft Azure PowerShell. В следующей статье объясняется, как установить и настроить Azure PowerShell:

* [Как установить и настроить Azure PowerShell](/powershell/azure/overview)

Для приведенных в этой статье примеров предполагается, что у вас уже есть группа ресурсов. Чтобы создать группу ресурсов, выполните следующую команду:

```powershell
New-AzureRmResourceGroup -Name MyRG -Location "West US"
```

> [!NOTE]
> Для Azure Resource Manager необходимо, чтобы все группы ресурсов имели расположение. Это расположение используется в качестве расположения по умолчанию для ресурсов, созданных в этой группе ресурсов. Но так как ресурсы профиля диспетчера трафика являются глобальными, а не региональными, выбор расположения группы ресурсов никак не повлияет на работу диспетчера трафика Azure.

## <a name="create-a-traffic-manager-profile"></a>Создание профиля диспетчера трафика

Чтобы создать профиль диспетчера трафика, используйте командлет `New-AzureRmTrafficManagerProfile`:

```powershell
$profile = New-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName contoso -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
```

В следующей таблице описаны параметры.

| Параметр | ОПИСАНИЕ |
| --- | --- |
| ИМЯ |Имя ресурса профиля диспетчера трафика. У профилей в одной группе ресурсов должны быть уникальные имена. Это имя отличается от имени DNS, которое используется в запросах DNS. |
| ResourceGroupName |Имя группы ресурсов, которая содержит ресурс профиля. |
| TrafficRoutingMethod |Задает метод маршрутизации трафика, который определяет конечную точку, возвращаемую в ответ на запрос DNS. Возможные значения: Performance (производительность), Weighted (взвешенный) и Priority (приоритетный). |
| RelativeDnsName |Задает часть DNS-имени с именем узла, которое предоставляется этим профилем диспетчера трафика. Это значение объединяется с DNS-именем домена, с помощью которого диспетчер трафика Azure формирует полное доменное имя профиля. Например, если задать значение contoso, полное доменное имя будет contoso.trafficmanager.net. |
| Срок жизни |Задает срок жизни запроса DNS в секундах. Это значение срока жизни дает локальным сопоставителям DNS и DNS-клиентам понять, в течение какого срока нужно хранить в кэше ответы DNS для этого профиля диспетчера трафика. |
| MonitorProtocol |Задает протокол, который следует использовать для контроля работоспособности конечных точек. Допустимые значения: HTTP и HTTPS. |
| MonitorPort |Задает порт TCP, который следует использовать для контроля работоспособности конечных точек. |
| MonitorPath |Задает путь относительно доменного имени конечной точки, который используется для проверки ее работоспособности. |

Командлет создает профиль диспетчера трафика в Azure и возвращает соответствующий объект профиля в PowerShell. На этом этапе профиль не содержит конечные точки. Дополнительные сведения о добавлении конечных точек в профиль диспетчера трафика см. в разделе [о добавлении конечных точек диспетчера трафика](#adding-traffic-manager-endpoints).

## <a name="get-a-traffic-manager-profile"></a>Получение профиля диспетчера трафика

Получить существующий объект профиля диспетчера трафика можно с помощью командлета `Get-AzureRmTrafficManagerProfle`:

```powershell
$profile = Get-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
```

Этот командлет возвращает профиль объекта трафика.

## <a name="update-a-traffic-manager-profile"></a>Изменение профиля диспетчера трафика

Изменение профилей диспетчера трафика выполняется в три этапа.

1. Получите профиль с помощью командлета `Get-AzureRmTrafficManagerProfile` или используйте профиль, возвращенный командлетом `New-AzureRmTrafficManagerProfile`.
2. Измените профиль. Можно добавить и удалить конечные точки или изменить параметры профиля или конечной точки. Эти изменения выполняются в автономном режиме. Изменяется только локальный объект в памяти, представляющий профиль.
3. Зафиксируйте изменения с помощью командлета `Set-AzureRmTrafficManagerProfile`.

Можно изменить все свойства профиля, кроме RelativeDnsName. Чтобы изменить свойство RelativeDnsName, необходимо удалить профиль и создать новый профиль с новым именем.

В следующем примере показано, как изменить в профиле срок жизни.

```powershell
$profile = Get-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
$profile.Ttl = 300
Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
```

Существует три типа конечных точек диспетчера трафика.

1. **Конечные точки Azure** — это службы, размещенные в Azure.
2. **Внешние конечные точки** — службы, размещенные за пределами Azure.
3. **Вложенные конечные точки** используются для создания вложенных иерархий профилей диспетчера трафика. Вложенные конечные точки позволяют создавать расширенные конфигурации маршрутизации трафика для сложных приложений.

Конечные точки всех трех типов можно добавлять двумя способами.

1. С помощью описанных выше трех шагов. Этот метод удобен тем, что позволяет в ходе одного обновления внести несколько изменений в настройки конечной точки.
2. С помощью командлета New-AzureRmTrafficManagerEndpoint. Он добавляет конечную точку в существующий профиль диспетчера трафика за одну операцию.

## <a name="adding-azure-endpoints"></a>Добавление конечных точек Azure

Конечные точки Azure ссылаются на службы, размещенные в Azure. Поддерживаются два типа конечных точек Azure.

1. Веб-приложения Azure.
2. Ресурсы Azure PublicIpAddress, которые могут подключаться к подсистеме балансировки нагрузки или к сетевому адаптеру виртуальной машины. Для использования в диспетчере трафика конечной точке publicIpAddress должно быть назначено DNS-имя.

В каждом случае:

* Для указания службы используется параметр targetResourceId командлета `Add-AzureRmTrafficManagerEndpointConfig` или `New-AzureRmTrafficManagerEndpoint`.
* Параметры Target и EndpointLocation определяются с помощью параметра TargetResourceId.
* Указывать Weight необязательно. Параметр Weight используется только в том случае, если профиль настроен для использования взвешенного (weighted) метода маршрутизации трафика. В остальных случаях этот параметр игнорируется. Если этот параметр указывается, его значение должно быть числом в диапазоне от 1 до 1000. Значение по умолчанию — 1.
* Указывать Priority необязательно. Параметр Priority используется только в том случае, если профиль настроен для использования приоритетного (priority) метода маршрутизации. В остальных случаях этот параметр игнорируется. Допускаются значения от 1 до 1000 (чем меньше число, тем выше приоритет). Если приоритет указан для одной конечной точки, он должен указываться и для всех остальных конечных точек. Если значения не указаны, конечным точкам в порядке очереди присваиваются значения по умолчанию: 1 и т. д.

### <a name="example-1-adding-web-app-endpoints-using-add-azurermtrafficmanagerendpointconfig"></a>Пример 1. Добавление конечных точек веб-приложения с помощью командлета `Add-AzureRmTrafficManagerEndpointConfig`

В этом примере мы создадим профиль диспетчера трафика и добавим две конечные точки веб-приложения с помощью командлета `Add-AzureRmTrafficManagerEndpointConfig`.

```powershell
$profile = New-AzureRmTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
$webapp1 = Get-AzureRMWebApp -Name webapp1
Add-AzureRmTrafficManagerEndpointConfig -EndpointName webapp1ep -TrafficManagerProfile $profile -Type AzureEndpoints -TargetResourceId $webapp1.Id -EndpointStatus Enabled
$webapp2 = Get-AzureRMWebApp -Name webapp2
Add-AzureRmTrafficManagerEndpointConfig -EndpointName webapp2ep -TrafficManagerProfile $profile -Type AzureEndpoints -TargetResourceId $webapp2.Id -EndpointStatus Enabled
Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
```
### <a name="example-2-adding-a-publicipaddress-endpoint-using-new-azurermtrafficmanagerendpoint"></a>Пример 2. Добавление конечной точки publicIpAddress с помощью командлета `New-AzureRmTrafficManagerEndpoint`

В этом примере мы добавляем в профиль диспетчера трафика ресурс с общедоступным IP-адресом. Общедоступный IP-адрес должен включать DNS-имя и может быть привязан к сетевому адаптеру виртуальной машины или к балансировщику нагрузки.

```powershell
$ip = Get-AzureRmPublicIpAddress -Name MyPublicIP -ResourceGroupName MyRG
New-AzureRmTrafficManagerEndpoint -Name MyIpEndpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type AzureEndpoints -TargetResourceId $ip.Id -EndpointStatus Enabled
```

## <a name="adding-external-endpoints"></a>Добавление внешних конечных точек

Диспетчер трафика использует внешние конечные точки для передачи трафика в службы, размещенные за пределами Azure. Как и конечные точки Azure, внешние конечные точки можно добавить с помощью командлета `Add-AzureRmTrafficManagerEndpointConfig`, за которым следует командлет `Set-AzureRmTrafficManagerProfile` или `New-AzureRMTrafficManagerEndpoint`.

При добавлении внешних конечных точек:

* Доменное имя конечной точки должно быть указано с помощью параметра Target.
* EndpointLocation является обязательным параметром, если используется метод маршрутизации трафика Performance. В противном случае этот параметр является необязательным. Его значением должно быть [допустимое имя региона Azure](https://azure.microsoft.com/regions/).
* Параметры Weight и Priority являются необязательными.

### <a name="example-1-adding-external-endpoints-using-add-azurermtrafficmanagerendpointconfig-and-set-azurermtrafficmanagerprofile"></a>Пример 1. Добавление внешних конечных точек с помощью командлетов `Add-AzureRmTrafficManagerEndpointConfig` и `Set-AzureRmTrafficManagerProfile`

В этом примере мы создаем профиль диспетчера трафика, добавляем в него две внешние конечные точки и фиксируем изменения.

```powershell
$profile = New-AzureRmTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
Add-AzureRmTrafficManagerEndpointConfig -EndpointName eu-endpoint -TrafficManagerProfile $profile -Type ExternalEndpoints -Target app-eu.contoso.com -EndpointLocation "North Europe" -EndpointStatus Enabled
Add-AzureRmTrafficManagerEndpointConfig -EndpointName us-endpoint -TrafficManagerProfile $profile -Type ExternalEndpoints -Target app-us.contoso.com -EndpointLocation "Central US" -EndpointStatus Enabled
Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
```

### <a name="example-2-adding-external-endpoints-using-new-azurermtrafficmanagerendpoint"></a>Пример 2. Добавление внешних конечных точек с помощью командлета `New-AzureRmTrafficManagerEndpoint`

В этом примере мы добавляем внешнюю конечную точку в существующий профиль. Профиль указывается с помощью имени профиля и имени группы ресурсов.

```powershell
New-AzureRmTrafficManagerEndpoint -Name eu-endpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type ExternalEndpoints -Target app-eu.contoso.com -EndpointStatus Enabled
```

## <a name="adding-nested-endpoints"></a>Добавление "вложенных" конечных точек

Каждый профиль диспетчера трафика определяет один метод маршрутизации трафика. Но бывают случаи, когда требуется более сложная маршрутизация трафика, чем та, которую может обеспечить отдельный профиль диспетчера трафика. Чтобы воспользоваться преимуществами нескольких методов маршрутизации трафика, можно выполнить вложение профилей диспетчера трафика. Вложенные профили позволяют переопределить работу диспетчера трафика по умолчанию и обеспечить поддержку более объемных и сложных развертываний приложений. Более подробные примеры см. в статье [Вложенные профили диспетчера трафика](traffic-manager-nested-profiles.md).

Вложенные конечные точки настраиваются в родительском профиле по типу конечной точки "NestedEndpoints". При указании вложенных конечных точек:

* Конечная точка должна быть указана с помощью параметра targetResourceId.
* EndpointLocation является обязательным параметром, если используется метод маршрутизации трафика Performance. В противном случае этот параметр является необязательным. Его значением должно быть [допустимое имя региона Azure](http://azure.microsoft.com/regions/).
* Параметры Weight и Priority для конечных точек Azure являются необязательными.
* Параметр MinChildEndpoints является необязательным. Значение по умолчанию — 1. Если количество доступных конечных точек опускается ниже этого значения, родительский профиль будет рассматривать дочерний профиль как профиль более низкого уровня, направляя трафик к другим конечным точкам родительского профиля.

### <a name="example-1-adding-nested-endpoints-using-add-azurermtrafficmanagerendpointconfig-and-set-azurermtrafficmanagerprofile"></a>Пример 1. Добавление вложенных конечных точек с помощью командлетов `Add-AzureRmTrafficManagerEndpointConfig` и `Set-AzureRmTrafficManagerProfile`

В этом примере мы создаем новые дочерний и родительский профили диспетчера трафика, добавляем дочерний профиль как вложенный в родительский профиль и фиксируем изменения.

```powershell
$child = New-AzureRmTrafficManagerProfile -Name child -ResourceGroupName MyRG -TrafficRoutingMethod Priority -RelativeDnsName child -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
$parent = New-AzureRmTrafficManagerProfile -Name parent -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName parent -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
Add-AzureRmTrafficManagerEndpointConfig -EndpointName child-endpoint -TrafficManagerProfile $parent -Type NestedEndpoints -TargetResourceId $child.Id -EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2
Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
```

Для краткости мы не добавляли другие конечные точки в родительский и дочерний профили.

### <a name="example-2-adding-nested-endpoints-using-new-azurermtrafficmanagerendpoint"></a>Пример 2. Добавление вложенных конечных точек с помощью командлета `New-AzureRmTrafficManagerEndpoint`

В этом примере мы добавляем существующий дочерний профиль как вложенную конечную точку в существующий родительский профиль. Профиль указывается с помощью имени профиля и имени группы ресурсов.

```powershell
$child = Get-AzureRmTrafficManagerEndpoint -Name child -ResourceGroupName MyRG
New-AzureRmTrafficManagerEndpoint -Name child-endpoint -ProfileName parent -ResourceGroupName MyRG -Type NestedEndpoints -TargetResourceId $child.Id -EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2
```

## <a name="adding-endpoints-from-another-subscription"></a>Добавление конечных точек из другой подписки

Диспетчер трафика может работать с конечными точками из нескольких подписок. Вам нужно переключиться на подписку с конечной точкой, которую вы хотите добавить для получения необходимых входных данных для диспетчера трафика. Затем необходимо переключиться на подписки с профилем диспетчера трафика и добавить в них конечную точку. В примере ниже показано, как это сделать с помощью общедоступного IP-адреса.

```powershell
Set-AzureRmContext -SubscriptionId $EndpointSubscription
$ip = Get-AzureRmPublicIpAddress -Name $IpAddresName -ResourceGroupName $EndpointRG

Set-AzureRmContext -SubscriptionId $trafficmanagerSubscription
New-AzureRmTrafficManagerEndpoint -Name $EndpointName -ProfileName $ProfileName -ResourceGroupName $TrafficManagerRG -Type AzureEndpoints -TargetResourceId $ip.Id -EndpointStatus Enabled
```

## <a name="update-a-traffic-manager-endpoint"></a>Обновление конечной точки диспетчера трафика

Есть два способа обновления существующей конечной точки диспетчера трафика.

1. Получить профиль диспетчера трафика с помощью командлета `Get-AzureRmTrafficManagerProfile`, обновить свойства конечной точки в профиле и зафиксировать изменения с помощью `Set-AzureRmTrafficManagerProfile`. Этот метод удобен тем, что позволяет в ходе одной операции обновления изменить настройки нескольких конечных точек.
2. Получить конечную точку диспетчера трафика с помощью командлета `Get-AzureRmTrafficManagerEndpoint`, обновить свойства конечной точки и зафиксировать изменения с помощью командлета `Set-AzureRmTrafficManagerEndpoint`. Этот метод проще, так как он не требует индексации массива конечных точек в профиле.

### <a name="example-1-updating-endpoints-using-get-azurermtrafficmanagerprofile-and-set-azurermtrafficmanagerprofile"></a>Пример 1. Обновление конечных точек с помощью командлетов `Get-AzureRmTrafficManagerProfile` и `Set-AzureRmTrafficManagerProfile`

В этом примере мы изменим приоритет двух конечных точек в существующем профиле.

```powershell
$profile = Get-AzureRmTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG
$profile.Endpoints[0].Priority = 2
$profile.Endpoints[1].Priority = 1
Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
```

### <a name="example-2-updating-an-endpoint-using-get-azurermtrafficmanagerendpoint-and-set-azurermtrafficmanagerendpoint"></a>Пример 2. Обновление конечной точки с помощью командлетов `Get-AzureRmTrafficManagerEndpoint` и `Set-AzureRmTrafficManagerEndpoint`

В этом примере мы изменим вес одной конечной точки в существующем профиле.

```powershell
$endpoint = Get-AzureRmTrafficManagerEndpoint -Name myendpoint -ProfileName myprofile -ResourceGroupName MyRG -Type ExternalEndpoints
$endpoint.Weight = 20
Set-AzureRmTrafficManagerEndpoint -TrafficManagerEndpoint $endpoint
```

## <a name="enabling-and-disabling-endpoints-and-profiles"></a>Включение и отключение конечных точек и профилей

Диспетчер трафика позволяет включать и отключать как отдельные конечные точки, так и целые профили.
Для этого нужно получить, обновить и зафиксировать ресурсы конечной точки или профиля. Для упрощения этих стандартных операций существуют специальные командлеты.

### <a name="example-1-enabling-and-disabling-a-traffic-manager-profile"></a>Пример 1. Включение и отключение профиля диспетчера трафика

Для включения профиля диспетчера трафика используйте командлет `Enable-AzureRmTrafficManagerProfile`. Профиль можно указать с помощью объекта профиля. Объект профиля можно передать по конвейеру или с помощью параметра -TrafficManagerProfile. В этом примере мы укажем профиль с помощью имени профиля и имени группы ресурсов.

```powershell
Enable-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup
```

Отключение профиля диспетчера трафика:

```powershell
Disable-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup
```

Командлет Disable-AzureRmTrafficManagerProfile запрашивает подтверждение. Этот запрос можно скрыть с помощью параметра -Force.

### <a name="example-2-enabling-and-disabling-a-traffic-manager-endpoint"></a>Пример 2. Включение и отключение конечной точки диспетчера трафика

Для включения конечной точки диспетчера трафика используйте командлет `Enable-AzureRmTrafficManagerEndpoint`. Задать конечную точку можно двумя способами:

1. С помощью объекта TrafficManagerEndpoint, передаваемого через конвейер, или с помощью параметра -TrafficManagerEndpoint.
2. С помощью имени конечной точки, ее типа, имени профиля и имени группы ресурсов:

```powershell
Enable-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
```

Аналогичным образом можно отключить конечную точку диспетчера трафика:

```powershell
Disable-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG -Force
```

Как и командлет `Disable-AzureRmTrafficManagerProfile`, `Disable-AzureRmTrafficManagerEndpoint` запрашивает подтверждение. Этот запрос можно скрыть с помощью параметра -Force.

## <a name="delete-a-traffic-manager-endpoint"></a>Удаление конечной точки диспетчера трафика

Для удаления отдельных конечных точек используйте командлет `Remove-AzureRmTrafficManagerEndpoint`:

```powershell
Remove-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
```

Этот командлет запрашивает подтверждение. Этот запрос можно скрыть с помощью параметра -Force.

## <a name="delete-a-traffic-manager-profile"></a>Удаление профиля диспетчера трафика

Чтобы удалить профиль диспетчера трафика, используйте командлет `Remove-AzureRmTrafficManagerProfile`, указав имя профиля и имя группы ресурсов:

```powershell
Remove-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG [-Force]
```

Этот командлет запрашивает подтверждение. Этот запрос можно скрыть с помощью параметра -Force.

Удаляемый профиль также можно указать с помощью объекта профиля:

```powershell
$profile = Get-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
Remove-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile [-Force]
```

Эти операции также можно объединить в последовательность:

```powershell
Get-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG | Remove-AzureRmTrafficManagerProfile [-Force]
```

## <a name="next-steps"></a>Дополнительная информация

[Мониторинг диспетчера трафика](traffic-manager-monitoring.md)

[Рекомендации по безопасности для диспетчера трафика](traffic-manager-performance-considerations.md)
