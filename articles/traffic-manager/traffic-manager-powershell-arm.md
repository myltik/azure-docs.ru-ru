<properties
   pageTitle="Предварительная поддержка диспетчера трафика Azure в диспетчере ресурсов Azure | Microsoft Azure"
   description="Использование PowerShell для работы с диспетчером трафика в диспетчере ресурсов Azure (ARM) (предварительная версия)"
   services="traffic-manager"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/25/2016"
   ms.author="joaoma" />

# Предварительная поддержка диспетчера трафика Azure в диспетчере ресурсов Azure
Диспетчер ресурсов Azure (ARM) представляет собой новую платформу управления для служб в Azure. Теперь профилями диспетчера трафика Azure можно управлять с помощью интерфейсов API и инструментов на основе диспетчера ресурсов Azure. Дополнительные сведения о диспетчере ресурсов Azure см. в статье [Использование групп ресурсов для управления ресурсами Azure](../azure-preview-portal-using-resource-groups.md).

## Модель ресурсов

Для настройки диспетчера трафика используется набор параметров, которые называются профилем. Он содержит параметры DNS, параметры маршрутизации трафика, параметры отслеживания конечных точек, а также список конечных точек службы, на которые будет выполняться маршрутизация трафика.

В ARM каждому профилю диспетчера трафика соответствует ресурс ARM типа TrafficManagerProfiles, для управления которым используется поставщик ресурсов Microsoft.Network. На уровне API REST каждый профиль имеет следующий URI:

	https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/trafficManagerProfiles/{profile-name}?api-version={api-version}

## Сравнение диспетчера трафика Azure с API управления службами

При настройке профилей диспетчера трафика с помощью ARM в нем доступны те же функциональные возможности, что и при использовании API управления службами Azure (ASM), за исключением перечисленных ниже ограничений предварительной версии.

Однако несмотря на одинаковый набор функций используются несколько иные термины:

- Метод балансировки нагрузки, который определяет, как именно диспетчер трафика выбирает конечную точку для маршрутизации трафика при ответе на определенный DNS-запрос, переименован в метод маршрутизации трафика.

- Метод маршрутизации трафика «циклический перебор» называется взвешенным.

- Метод маршрутизации трафика «отработка отказа» называется приоритетным.

## Ограничения
На данный момент существуют некоторые ограничения в поддержке диспетчера трафика Azure диспетчером ресурсов Azure:

- Профили диспетчера трафика, созданные с использованием существующих (не связанных с ARM) API управления службами Azure (ASM), инструментов и портала «классической» версии, недоступны в ARM, и наоборот. Перенос профилей из API-интерфейсов ASM в ARM сейчас не поддерживается. Можно только удалить и повторно создать профиль.

- "Вложенные" конечные точки диспетчера трафика поддерживаются через API диспетчера ресурсов Azure, PowerShell диспетчера ресурсов Azure и режим диспетчера ресурсов Azure интерфейса командной строки Azure. На данный момент они не поддерживаются в портале Azure (который также использует API диспетчера ресурсов Azure).

- При ссылке на веб-приложение конечные точки диспетчера трафика типа AzureEndpoints могут ссылаться только на [слот веб-приложения](../app-service-web/web-sites-staged-publishing.md) по умолчанию. Настраиваемые слоты пока не поддерживаются. Настраиваемые слоты можно задавать с помощью типа ExternalEndpoints.

## Настройка Azure PowerShell

В этих инструкциях используется оболочка Microsoft Azure PowerShell, которую нужно настроить с помощью описанных ниже действий.

Пользователи, не использующие PowerShell или Windows, могут выполнять аналогичные операции через интерфейс командной строки Azure. Все операции, за исключением управления "вложенными" профилями диспетчера трафика, также доступны через портал Azure.

### Шаг 1
Установите последнюю версию Azure PowerShell, которую можно скачать со страницы загружаемых файлов Azure.

### Шаг 2
Войдите в свою учетную запись Azure.

	PS C:\> Login-AzureRmAccount

Вам будет предложено указать свои учетные данные для проверки подлинности.

### Шаг 3.
Выберите подписку Azure.

	PS C:\> Set-AzureRmContext -SubscriptionName "MySubscription"

Чтобы просмотреть перечень доступных подписок, используйте командлет Get-AzureRmSubscription.

### Шаг 4.

Для управления службой диспетчера трафика используется поставщик ресурсов Microsoft.Network. Прежде чем приступать к работе с диспетчером с помощью ARM, необходимо зарегистрировать подписку Azure для использования этого поставщика. Эта операция выполняется один раз для каждой подписки.

	PS C:\> Register-AzureRmResourceProvider –ProviderNamespace Microsoft.Network

### Шаг 5
Создайте группу ресурсов (этот шаг можно пропустить, если вы используете существующую группу).

	PS C:\> New-AzureRmResourceGroup -Name MyRG -Location "West US"

В диспетчере ресурсов Azure для всех групп ресурсов должно быть указано расположение. Оно используется в качестве расположения по умолчанию для всех ресурсов данной группы. Однако поскольку ресурсы профилей диспетчера трафика являются глобальными, а не региональными, выбор расположения группы ресурсов никак не повлияет на работу диспетчера трафика Azure.

## Создание профиля диспетчера трафика

Чтобы создать профиль диспетчера трафика, воспользуйтесь командлетом New-AzureRmTrafficManagerProfile:

	PS C:\> $profile = New-AzureRmTrafficManagerProfile –Name MyProfile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName contoso -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"

Он принимает перечисленные ниже параметры.

- Name: имя ресурса ARM для ресурса профиля диспетчера трафика. У профилей в одной группе ресурсов должны быть уникальные имена. Это имя отличается от имени DNS, которое используется в запросах DNS.

- ResourceGroupName: имя группы ресурсов ARM, которая содержит ресурс профиля.

- TrafficRoutingMethod: метод маршрутизации трафика, который определяет конечную точку, возвращаемую в ответ на поступающие запросы DNS. Возможные значения: Performance (производительный), Weighted (взвешенный) и Priority (приоритетный).

- RelativeDnsName: относительное DNS-имя, предоставляемое этим профилем диспетчера трафика. Это значение объединяется с DNS-именем домена, с помощью которого диспетчер трафика Azure формирует полное доменное имя профиля. Например, для значения contoso будет создан профиль диспетчера трафика с полным доменным именем contoso.trafficmanager.net.

- TTL: срок жизни запроса DNS в секундах. Это значение дает локальным сопоставителям DNS и DNS-клиентам понять, в течение какого срока нужно хранить в кэше ответы DNS, полученные от профиля диспетчера трафика.

- MonitorProtocol: протокол, который следует использовать для контроля работоспособности конечных точек. Допустимые значения: HTTP и HTTPS.

- MonitorPort: порт TCP, который следует использовать для контроля работоспособности конечных точек.

- MonitorPath: путь относительно доменного имени конечной точки, который используется для проверки ее работоспособности.

Командлет создает профиль в диспетчере трафика Azure и возвращает соответствующий объект профиля. На этом этапе профиль не содержит конечных точек. Инструкции по их добавлению см. в разделе [Добавление конечных точек диспетчера трафика](#adding-traffic-manager-endpoints).

## Получение профиля диспетчера трафика

Существующий объект профиля диспетчера трафика можно получить с помощью командлета Get-AzureRmTrafficManagerProfle:

	PS C:\> $profile = Get-AzureRmTrafficManagerProfile –Name MyProfile -ResourceGroupName MyRG

Этот командлет возвращает профиль объекта трафика.

## Изменение профиля диспетчера трафика [](#update-traffic-manager-profile)

Изменение профилей диспетчера трафика (например, для добавления и удаления конечных точек либо изменения параметров) выполняется в три этапа, которые перечислены ниже.

1.	Получение профиля с помощью командлета Get-AzureRmTrafficManagerProfile (также можно использовать профиль, возвращенный командлетом New-AzureRmTrafficManagerProfile).

2.	Изменение профиля (добавление или удаление конечных точек, изменение параметров конечных точек и профиля). Эти изменения выполняются в автономном режиме и только в локальном объекте, который соответствует данному профилю.

3.	Фиксация изменений с помощью командлета Set-AzureRmTrafficManagerProfile. Он заменяет существующий профиль в диспетчере трафика Azure указанным объектом профиля.

Можно изменять любые свойства профиля, кроме значения RelativeDnsName, которое невозможно изменить после создания профиля (для этого необходимо удалить и повторно создать профиль).

Например, так можно изменить срок жизни профиля:

	PS C:\> $profile = Get-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyRG
	PS C:\> $profile.Ttl = 300
	PS C:\> Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile

## Добавление конечных точек диспетчера трафика
Существует три типа конечных точек диспетчера трафика. 1. Конечные точки Azure. Представляют службы, размещенные в Azure. 2. Внешние конечные точки. Представляют службы, размещенные за пределами Azure. 3. Вложенные конечные точки. Используются для создания вложенных иерархий профилей диспетчера трафика, позволяя использовать расширенные конфигурации маршрутизации трафика в сложных приложениях. Они пока не поддерживаются в API ARM.

Конечные точки всех трех типов можно добавлять двумя способами. 1. С помощью 3-этапного процесса, который похож на описанный в разделе [Обновление профиля диспетчера трафика](#update-traffic-manager-profile): получить объект профиля с помощью командлета Get-AzureRmTrafficManagerProfile, обновить объект локально, добавив конечную точку с помощью командлета Add-AzureRmTrafficManagerEndpointConfig, передать изменения в диспетчер трафика Azure с помощью командлета Set-AzureRmTrafficManagerProfile. Этот метод удобен тем, что позволяет за одно обновление внести несколько изменений в настройки конечной точки. 2. С помощью командлета New-AzureRmTrafficManagerEndpoint. Он добавляет конечную точку в существующий профиль диспетчера трафика за одну операцию.

### Добавление конечных точек Azure

Конечные точки Azure ссылаются на другие службы, размещенные в Azure. Сейчас поддерживаются три типа конечных точек Azure. 1. Веб-приложения Azure. 2. «Классические» облачные службы (которые могут содержать службу PaaS или виртуальные машины IaaS). 3. Ресурсы ARM Microsoft.Network/publicIpAddress, которые могут подключаться к балансировщику нагрузки или к сетевому адаптеру виртуальной машины. Обратите внимание, что для использования в диспетчере трафика к конечной точке publicIpAddress должно быть привязано DNS-имя.

Независимо от типа конечной точки: — Служба задается с помощью параметра targetResourceId командлетов Add-AzureRmTrafficManagerEndpointConfig или New-AzureRmTrafficManagerEndpoint. — Параметры Target и EndpointLocation задавать не нужно (они неявно определяются по указанному выше параметру TargetResourceId). — Параметр Weight является необязательным. Параметр Weight используется только в том случае, если профиль настроен для использования взвешенного (weighted) метода маршрутизации. В остальных случаях этот параметр игнорируется. Если параметр задан, его значение должно быть в диапазоне от 1 до 1000. По умолчанию используется значение 1. —Параметр Priority является необязательным. Параметр Priority используется только в том случае, если профиль настроен для использования приоритетного (priority) метода маршрутизации. В остальных случаях этот параметр игнорируется. Допустимые значения: от 1 до 1000 (чем ниже значение, тем выше приоритет). Если приоритет указан для одной конечной точки, он должен указываться и для всех остальных конечных точек. Если значения не указаны, конечным точкам в порядке очереди присваиваются значения по умолчанию: 1, 2, 3 и т. д.

#### Пример 1. Добавление конечных точек веб-приложения с помощью Add-AzureRmTrafficManagerEndpointConfig
В этом примере мы создаем новый профиль диспетчера трафика и добавляем две конечные точки веб-приложения с помощью командлета Add-AzureRmTrafficManagerEndpointConfig, а затем переносим обновленный профиль в диспетчер трафика Azure с помощью командлета Set-AzureRmTrafficManagerProfile.

	PS C:\> $profile = New-AzureRmTrafficManagerProfile –Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
	PS C:\> $webapp1 = Get-AzureRMWebApp -Name webapp1
	PS C:\> Add-AzureRmTrafficManagerEndpointConfig –EndpointName webapp1ep –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp1.Id –EndpointStatus Enabled
	PS C:\> $webapp2 = Get-AzureRMWebApp -Name webapp2
	PS C:\> Add-AzureRmTrafficManagerEndpointConfig –EndpointName webapp2ep –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp2.Id –EndpointStatus Enabled
	PS C:\> Set-AzureRmTrafficManagerProfile –TrafficManagerProfile $profile  

#### Пример 2. Добавление конечной точки «классической» облачной службы с помощью New-AzureRmTrafficManagerEndpoint
В этом примере мы добавляем в профиль диспетчера трафика конечную точку «классической» облачной службы. Обратите внимание, что в этом случае указываем имя профиля и имя группы ресурсов, а не передаем объект профиля (можно использовать оба метода).

	PS C:\> $cloudService = Get-AzureRmResource -ResourceName MyCloudService -ResourceType "Microsoft.ClassicCompute/domainNames" -ResourceGroupName MyCloudService
	PS C:\> New-AzureRmTrafficManagerEndpoint –Name MyCloudServiceEndpoint –ProfileName MyProfile -ResourceGroupName MyRG –Type AzureEndpoints -TargetResourceId $cloudService.Id –EndpointStatus Enabled

#### Пример 3. Добавление конечной точки publicIpAddress с помощью New-AzureRmTrafficManagerEndpoint
В этом примере мы добавляем в профиль диспетчера трафика ресурс ARM с общедоступным IP-адресом. Общедоступный IP-адрес должен включать DNS-имя и может быть привязан к сетевому адаптеру виртуальной машины или к балансировщику нагрузки.

	PS C:\> $ip = Get-AzureRmPublicIpAddress -Name MyPublicIP -ResourceGroupName MyRG
	PS C:\> New-AzureRmTrafficManagerEndpoint –Name MyIpEndpoint –ProfileName MyProfile -ResourceGroupName MyRG –Type AzureEndpoints -TargetResourceId $ip.Id –EndpointStatus Enabled

### Добавление внешних конечных точек
Диспетчер трафика использует внешние конечные точки для передачи трафика в службы, размещенные за пределами Azure. Как и конечные точки Azure, внешние конечные точки можно добавлять с помощью командлета Add-AzureRmTrafficManagerEndpointConfig в сочетании с командлетом Set-AzureRmTrafficManagerProfile или New-AzureRMTrafficManagerEndpoint.

При указании внешних конечных точек: — Доменное имя конечной точки следует задавать в параметре Target. — Параметр EndpointLocation является обязательным, только если используется метод маршрутизации трафика Performance. В качестве значения должно быть указано [допустимое имя региона Azure](https://azure.microsoft.com/regions/). — Параметры Weight и Priority являются необязательными, как и для конечных точек Azure.

#### Пример 1. Добавление внешних конечных точек с помощью Add-AzureRmTrafficManagerEndpointConfig и Set-AzureRmTrafficManagerProfile
В этом примере мы создаем новый профиль диспетчера трафика, добавляем в него две внешние конечные точки и фиксируем изменения.

	PS C:\> $profile = New-AzureRmTrafficManagerProfile –Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
	PS C:\> Add-AzureRmTrafficManagerEndpointConfig –EndpointName eu-endpoint –TrafficManagerProfile $profile –Type ExternalEndpoints -Target app-eu.contoso.com –EndpointStatus Enabled
	PS C:\> Add-AzureRmTrafficManagerEndpointConfig –EndpointName us-endpoint –TrafficManagerProfile $profile –Type ExternalEndpoints -Target app-us.contoso.com –EndpointStatus Enabled
	PS C:\> Set-AzureRmTrafficManagerProfile –TrafficManagerProfile $profile  

#### Пример 2. Добавление внешних конечных точек с помощью New-AzureRmTrafficManagerEndpoint
В этом примере мы добавляем внешнюю конечную точку в существующий профиль, указывая его с помощью имени профиля и имени группы ресурсов.

	PS C:\> New-AzureRmTrafficManagerEndpoint –Name eu-endpoint –ProfileName MyProfile -ResourceGroupName MyRG –Type ExternalEndpoints -Target app-eu.contoso.com –EndpointStatus Enabled

### Добавление "вложенных" конечных точек

Диспетчер трафика позволяет настроить профиль диспетчера трафика (мы назовем его "дочерним" профилем) в качестве конечной точки в другом профиле диспетчера трафика (который мы назовем "родительским" профилем).

Вложенный диспетчер трафика позволяет создавать более гибкие и эффективные схемы маршрутизации трафика и отработки отказа для поддержки потребностей более крупных и сложных развертываний. [В этой записи блога](https://azure.microsoft.com/blog/new-azure-traffic-manager-nested-profiles/) представлено несколько примеров.

Вложенные конечные точки настраиваются в родительском профиле по типу конечной точки "NestedEndpoints". При указании вложенных конечных точек: — Конечную точку (дочерний профиль) следует указывать с помощью параметра targetResourceId. — Параметр EndpointLocation является обязательным только в том случае, если используется метод маршрутизации трафика "Performance". В качестве значения должно быть указано [допустимое имя региона Azure](http://azure.microsoft.com/regions/). — Параметры Weight и Priority являются необязательными, как и для конечных точек Azure. — Параметр MinChildEndpoints является необязательным, по умолчанию его значение равно 1. Если число доступных конечных точек в дочернем профиле опускается ниже этого значения, то родительский профиль будет рассматривать дочерний профиль как "деградирующий" и направляет трафик к другим конечным точкам родительского профиля.


#### Пример 1. Добавление вложенных конечных точек с помощью Add-AzureRmTrafficManagerEndpointConfig и Set-AzureRmTrafficManagerProfile

В этом примере мы создаем новый диспетчер трафика дочернего и родительского профилей, добавляем дочерний профиль как вложенный в родительский профиль и фиксируем изменения. (Для краткости мы не будем добавлять другие конечные точки в дочерний профиль или в родительский профиль, несмотря на то, что обычно они также требуются.)

	PS C:\> $child = New-AzureRmTrafficManagerProfile –Name child -ResourceGroupName MyRG -TrafficRoutingMethod Priority -RelativeDnsName child -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
	PS C:\> $parent = New-AzureRmTrafficManagerProfile –Name parent -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName parent -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
	PS C:\> Add-AzureRmTrafficManagerEndpointConfig –EndpointName child-endpoint –TrafficManagerProfile $parent –Type NestedEndpoints -TargetResourceId $child.Id –EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2
	PS C:\> Set-AzureRmTrafficManagerProfile –TrafficManagerProfile $profile

#### Пример 2. Добавление вложенных конечных точек с помощью New-AzureRmTrafficManagerEndpoint

В этом примере мы добавляем существующий дочерний профиль как вложенную конечную точку в существующий родительский профиль, указывая его с помощью имени профиля и имени группы ресурсов.

	PS C:\> $child = Get-AzureRmTrafficManagerEndpoint –Name child -ResourceGroupName MyRG
	PS C:\> New-AzureRmTrafficManagerEndpoint –Name child-endpoint –ProfileName parent -ResourceGroupName MyRG –Type NestedEndpoints -TargetResourceId $child.Id –EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2


## Обновление конечной точки диспетчера трафика
Есть два способа обновления существующей конечной точки диспетчера трафика. 1. Получить профиль диспетчера трафика с помощью командлета Get-AzureRmTrafficManagerProfile, обновить свойства конечной точки в профиле и зафиксировать изменения с помощью Set-AzureRmTrafficManagerProfile. Этот метод удобен тем, что позволяет за одно обновление изменить настройки нескольких конечных точек. 2. Получить конечную точку диспетчера трафика с помощью командлета Get-AzureRmTrafficManagerEndpoint, обновить свойства конечной точки и зафиксировать изменения с помощью Set-AzureRmTrafficManagerEndpoint. Этот метод проще, так как он не требует индексации массива конечных точек в профиле.

#### Пример 1. Обновление конечных точек с помощью Get-AzureRmTrafficManagerProfile и Set-AzureRmTrafficManagerProfile
В этом примере мы будем изменять приоритет двух конечных точек в существующем профиле.

	PS C:\> $profile = Get-AzureRmTrafficManagerProfile –Name myprofile -ResourceGroupName MyRG
	PS C:\> $profile.Endpoints[0].Priority = 2
	PS C:\> $profile.Endpoints[1].Priority = 1
	PS C:\> Set-AzureRmTrafficManagerProfile –TrafficManagerProfile $profile

#### Пример 2. Обновление конечной точки с помощью Get-AzureRmTrafficManagerEndpoint и Set-AzureRmTrafficManagerEndpoint
В этом примере мы будем изменять вес одной конечной точки в существующем профиле.

	PS C:\> $endpoint = Get-AzureRmTrafficManagerEndpoint -Name myendpoint -ProfileName myprofile -ResourceGroupName MyRG -Type ExternalEndpoints
	PS C:\> $endpoint.Weight = 20
	PS C:\> Set-AzureRmTrafficManagerEndpoint -TrafficManagerEndpoint $endpoint

## Включение и отключение конечных точек и профилей
Диспетчер трафика позволяет включать и отключать как отдельные конечные точки, так и целые профили. Для этого нужно получить, обновить и зафиксировать ресурсы конечной точки или профиля. Для упрощения этих стандартных операций существуют специальные командлеты.

#### Пример 1. Включение и отключение профиля диспетчера трафика
Чтобы включить профиль диспетчера трафика, воспользуйтесь командлетом Enable-AzureRmTrafficManagerProfile. Профиль можно указать с помощью объекта профиля (передав его по конвейеру или через параметр «-TrafficManagerProfile») либо с помощью имени профиля и имени группы ресурсов, как в этом примере.

	PS C:\> Enable-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup

Аналогичным образом можно отключить профиль диспетчера трафика:

	PS C:\> Disable-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup

Командлет Disable-AzureRmTrafficManagerProfile выдает запрос подтверждения, который можно отключить, указав параметр «-Force».

#### Пример 2. Включение и отключение конечной точки диспетчера трафика
Чтобы включить конечную точку диспетчера трафика, воспользуйтесь командлетом Enable-AzureRmTrafficManagerEndpoint. Конечную точку можно указать с помощью объекта TrafficManagerEndpoint (передав по конвейеру или через параметр «-TrafficManagerEndpoint») либо с помощью имени конечной точки, типа конечной точки, имени профиля и имени группы ресурсов:

	PS C:\> Enable-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG

Аналогичным образом можно отключить конечную точку диспетчера трафика:

 	PS C:\> Disable-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG -Force

Командлет Disable-AzureRmTrafficManagerEndpoint, как и Disable-AzureRmTrafficManagerProfile, выдает запрос подтверждения, который можно отключить, указав параметр «-Force».

## Удаление конечной точки диспетчера трафика
Один из способов удаления конечной точки диспетчера трафика: получить объект профиля (с помощью командлета Get-AzureRmTrafficManagerProfile), обновить список конечных точек в объекте локального профиля и зафиксировать изменения (с помощью Set-AzureRmTrafficManagerProfile). Этот метод позволяет зафиксировать изменения сразу в нескольких конечных точках.

Другой способ удаления отдельных конечных точек — с помощью командлета Remove-AzureRmTrafficManagerEndpoint:

	PS C:\> Remove-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
	
Этот командлет запросит подтверждение, которое можно отключить, указав параметр «-Force».

## Удаление профиля диспетчера трафика
Чтобы удалить профиль диспетчера трафика, выполните командлет Remove-AzureRmTrafficManagerProfile, указав имя профиля и имя группы ресурсов:

	PS C:\> Remove-AzureRmTrafficManagerProfile –Name MyProfile -ResourceGroupName MyRG [-Force]

Этот командлет запрашивает подтверждение. Чтобы отключить его, можно указать необязательный параметр -Force. Удаляемый профиль также можно указать с помощью объекта профиля:

	PS C:\> $profile = Get-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyRG
	PS C:\> Remove-AzureTrafficManagerProfile –TrafficManagerProfile $profile [-Force]

Эти операции также можно объединить в последовательность:

	PS C:\> Get-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyRG | Remove-AzureTrafficManagerProfile [-Force]

## Дальнейшие действия

[Мониторинг диспетчера трафика](traffic-manager-monitoring.md)

[Рекомендации по безопасности для диспетчера трафика](traffic-manager-performance-considerations.md)
 

<!---HONumber=AcomDC_0204_2016-->