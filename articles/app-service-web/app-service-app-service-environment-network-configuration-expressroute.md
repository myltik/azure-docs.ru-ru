<properties 
	pageTitle="Сведения о настройке сети для работы с Express Route" 
	description="Сведения о настройке сети для выполнения сред службы приложений в виртуальных сетях, подключенных к каналу ExpressRoute." 
	services="app-service\web" 
	documentationCenter="" 
	authors="stefsch" 
	manager="nirma" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/30/2015" 
	ms.author="stefsch"/>

# Сведения о конфигурации сети для сред службы приложений с ExpressRoute 

## Обзор ##
Клиенты могут подключить канал [Azure ExpressRoute][ExpressRoute] к своей инфраструктуре виртуальной сети, расширяя таким образом свою локальную сеть в Azure. Среду службы приложений можно создать в подсети этой инфраструктуры [виртуальной сети][virtualnetwork]. Затем с помощью приложений, работающих в среде службы приложений, можно установить безопасное подключение к серверным ресурсам, доступным только через соединение ExpressRoute.

## Необходимое сетевое подключение ##
Существуют требования к сетевому подключению для среды службы приложений, которым изначально может не соответствовать виртуальная сеть, подключенная к ExpressRoute.

Для правильной работы среды службы приложений необходимы следующие компоненты.


-  Исходящее сетевое подключение к ресурсам хранилища Azure и базам данных SQL, которые находятся в одном регионе со средой службы приложений. Этот сетевой путь не может проходить через прокси внутренней корпоративной сети, так как это скорее всего изменит текущий адрес NAT для исходящего сетевого трафика. Изменение адреса NAT для исходящего сетевого трафика среды службы приложений, направленного к конечным точкам хранилища Azure и базам данных SQL, приведет к появлению проблем подключения.
-  Конфигурация DNS для виртуальной сети должна позволять разрешать адреса конечных точек в следующих доменах, которыми управляет Azure: **. file.core.windows.net\*,**. blob.core.windows.net\*,**. database.windows.net\*.-  Конфигурация DNS для виртуальной сети должна оставаться неизменной во время создания сред службы приложений, а также во время их повторной настройки и масштабирования.   
-  Разрешения на входящий сетевой доступ к требуемым портам для сред службы приложений необходимо предоставлять согласно процедуре, описанной в этой [статье][requiredports].

Требования DNS можно выполнить при помощи правильной настройки DNS для виртуальной сети.

Чтобы выполнить требования к входящему сетевому доступу, необходимо настроить [группу сетевой безопасности][NetworkSecurityGroups] в подсети среды службы приложений. Дополнительную информацию о предоставлении необходимого доступа см. в этой [статье][requiredports].

## Активация исходящего сетевого подключения для среды службы приложений##
По умолчанию только что созданный канал ExpressRoute объявляет основной маршрут, который позволяет создавать исходящее подключение к Интернету. С такой конфигурацией среда службы приложений сможет подключаться к другим конечным точкам Azure.

Тем не менее типичная настройка клиента сама определяет собственный основной маршрут, который вместо этого отправляет исходящий интернет-трафик через инфраструктуру прокси-сервера или брандмауэра клиента. Этот поток трафика постоянно прерывает среды службы приложений, поскольку исходящий трафик заблокирован локально или транслирован с помощью NAT в нераспознаваемый набор адресов, которые больше не относятся к различным конечным точкам Azure.

Чтобы решить проблему, следует указать один или несколько определяемых пользователем маршрутов в подсети, которая содержит среду службы приложений. Определяемые пользователем маршруты задают маршруты для подсети, которые будут использоваться вместо основного маршрута.

Справочные сведения об определяемых пользователем маршрутах см. в этом [обзоре][UDROverview].

Сведения о создании и настройке определяемых пользователем маршрутов см. в этой [инструкции][UDRHowTo].

## Пример настройки UDR для среды службы приложений ##

**Предварительные требования**

1. Установите последнюю версию Azure Powershell, которую можно загрузить со [страницы загрузок Azure][AzureDownloads] (за июнь 2015 г. или более позднюю версию). В разделе «Средства командной строки» выберите ссылку «Установка» в разделе «Windows Powershell», чтобы установить последние командлеты Powershell.

2. Мы рекомендуем создать уникальную подсеть для монопольного использования средой службы приложений. В этом случае маршруты UDR, используемые в подсети, будут открывать только исходящий трафик для среды службы приложений.
3. **Важно!** Не следует развертывать среду службы приложений, **пока** не будут выполнены следующие настройки. Это гарантирует наличие исходящего сетевого подключения до развертывания среды службы приложений.

**Шаг 1. Создание именованной таблицы маршрутов**

Следующий фрагмент кода создает таблицу маршрутов с именем DirectInternetRouteTable в регионе Azure Запад США.

    New-AzureRouteTable -Name 'DirectInternetRouteTable' -Location uswest

**Шаг 2. Создание одного или нескольких маршрутов в таблице**

Чтобы включить исходящий доступ к Интернету, в таблицу необходимо добавить один или несколько маршрутов. В приведенном ниже примере добавляются маршруты для охвата всех возможных адресов Azure, используемых в регионе Запад США.

    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 1' -AddressPrefix 23.0.0.0/8 -NextHopType Internet
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 2' -AddressPrefix 40.0.0.0/8 -NextHopType Internet
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 3' -AddressPrefix 65.0.0.0/8 -NextHopType Internet
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 4' -AddressPrefix 104.0.0.0/8 -NextHopType Internet
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 5' -AddressPrefix 137.0.0.0/8 -NextHopType Internet
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 6' -AddressPrefix 138.0.0.0/8 -NextHopType Internet
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 7' -AddressPrefix 157.0.0.0/8 -NextHopType Internet
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 8' -AddressPrefix 168.0.0.0/8 -NextHopType Internet
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 9' -AddressPrefix 191.0.0.0/8 -NextHopType Internet


Полный, обновленный список диапазонов CIDR, которые использует Azure, можно загрузить в виде XML-файла, содержащего все диапазоны из [Центра загрузки Майкрософт][DownloadCenterAddressRanges]

**Примечание.** С некоторого времени сокращение CIDR 0.0.0.0/0 будет доступно для использования в параметре *AddressPrefix*. Это сокращение обозначает «все интернет-адреса». Вместо этого сейчас разработчикам приходится использовать широкий набор диапазонов CIDR для охвата всех возможных диапазонов адресов Azure, используемых в регионе, где развернута среда службы приложений.

**Шаг 3. Привязка таблицы маршрутов к подсети, содержащей среду службы приложений**

В последнем шаге настройки таблица маршрутов привязывается к подсети, в которой будет развернута среда службы приложений. Следующая команда привязывает DirectInternetRouteTable к ASESubnet, которая в конечном итоге будет содержать среду службы приложений.

    Set-AzureSubnetRouteTable -VirtualNetworkName 'YourVirtualNetworkNameHere' -SubnetName 'ASESubnet' -RouteTableName 'DirectInternetRouteTable'


**Шаг 4. Заключительные действия**

После привязки таблицы маршрутов к подсети мы рекомендуем сначала выполнить тестирование и убедиться в требуемом результате. Например, разверните виртуальную машину в подсети и убедитесь в следующем, что следующие утверждения верны.


- Исходящий трафик к конечным точкам Azure не передается на канал ExpressRoute.
- Поиски DNS для конечных точек Azure разрешаются должным образом. 

После выполнения приведенных выше шагов можно приступать к созданию среды службы приложений.

## Приступая к работе

Чтобы начать работу со средами службы приложений, см. статью [Введение в среду службы приложений][IntroToAppServiceEnvironment]

Дополнительные сведения о платформе службы приложений Azure см. в статье [Служба приложений Azure][AzureAppService].

<!-- LINKS -->
[virtualnetwork]: http://azure.microsoft.com/services/virtual-network/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[requiredports]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[NetworkSecurityGroups]: http://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[UDROverview]: http://azure.microsoft.com/documentation/articles/virtual-networks-udr-overview/
[UDRHowTo]: http://azure.microsoft.com/documentation/articles/virtual-networks-udr-how-to/
[HowToCreateAnAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[AzureDownloads]: http://azure.microsoft.com/downloads/
[DownloadCenterAddressRanges]: http://www.microsoft.com/ru-ru/download/details.aspx?id=41653
[NetworkSecurityGroups]: https://msdn.microsoft.com/library/azure/dn848316.aspx
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[IntroToAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
 

<!-- IMAGES -->

<!---HONumber=July15_HO5-->