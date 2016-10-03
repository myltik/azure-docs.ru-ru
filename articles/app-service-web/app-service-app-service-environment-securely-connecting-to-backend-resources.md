<properties 
	pageTitle="Безопасное подключение к серверным ресурсам из среды службы приложений" 
	description="Подробные сведения о безопасном подключении к серверным ресурсам из среды службы приложений." 
	services="app-service" 
	documentationCenter="" 
	authors="ccompy" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/11/2016" 
	ms.author="stefsch"/>

# Безопасное подключение к серверным ресурсам из среды службы приложений #

## Обзор ##
Так как среда службы приложений всегда создается **либо** в виртуальной сети Azure Resource Manager, **либо** в [виртуальной сети][virtualnetwork], использующей классическую модель развертывания, исходящие подключения из среды службы приложений к другим внутренним ресурсам могут передаваться исключительно по виртуальной сети. В связи с последним изменением от июня 2016 года среды ASE можно развертывать в виртуальных сетях, использующих либо диапазоны общедоступных адресов, либо адресные пространства RFC1918 (т. е. частные адреса).

Например, SQL Server может работать в кластере виртуальных машин с заблокированным портом 1433. Конечная точка может быть ACLd, разрешая только доступ от других ресурсов в той же виртуальной сети.

Другой пример: конфиденциальные конечные точки могут выполняться локально и быть подключены к Azure через подключение [сеть-сеть][SiteToSite] или [Azure ExpressRoute][ExpressRoute]. В результате только ресурсы в виртуальной сети, подключенной к туннелям "сеть-сеть" или ExpressRoute, смогут иметь доступ к локальным конечным точкам.

Во всех этих сценариях приложения, выполняемые в среде службы приложений, смогут безопасно подключаться к различным серверам и ресурсам. Исходящий трафик от приложений, выполняемых в среде службы приложений, к частным конечным точкам в той же виртуальной сети (или подключенным к той же виртуальной сети), будет идти только по виртуальной сети. Исходящий трафик к частным конечным точкам не будет проходить через общедоступный Интернет.

Одно из предупреждений применяется для исходящего трафика из среды службы приложений к конечным точкам в виртуальной сети. Среды службы приложений не могут достичь конечных точек виртуальных машин, расположенных в **той же** подсети, что и среда службы приложений. Обычно это не должно быть проблемой при условии, что среды службы приложений развертываются в подсеть, зарезервированную для монопольного использования средой службы приложений.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## Требования к DNS и исходящим подключениям ##
Для надлежащего функционирования среды службы приложений требуется исходящий доступ к различным конечным точкам. Полный список внешних конечных точек, используемых в ASE, приведен в разделе "Необходимое сетевое подключение" статьи [Сведения о конфигурации сети для сред службы приложений с ExpressRoute](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity).

Для сред службы приложений требуется, чтобы для виртуальной сети была настроена допустимая инфраструктура DNS. Если после создания среды службы приложений по какой-либо причине меняется конфигурация DNS, разработчик может принудительно задать выбор новой конфигурации DNS в среде службы приложений. Перезагрузка разворачиваемой среды с помощью значка "Перезапуск", расположенного в верхней части колонки управления средой службы приложений на портале, приведет к выбору новой конфигурации DNS в среде.

Также рекомендуется настроить пользовательские DNS-серверы в виртуальной сети заранее, т. е. до создания среды службы приложений. Если конфигурация виртуальной сети DNS изменяется во время создания среды службы приложений, это приведет к сбою процесса создания среды службы приложений. Аналогично, если на другой стороне VPN-шлюза имеется пользовательский DNS-сервер, который недоступен, процесс создания среды службы приложений будет прерван.

## Подключение к SQL Server
В типичной конфигурации SQL Server конечная точка прослушивает порт 1433:

![Конечная точка сервера SQL Server][SqlServerEndpoint]

Существует два подхода к ограничению трафика к этой конечной точке.


- [Сетевые списки управления доступом][NetworkAccessControlLists] \(Network ACLs)

- [Группы сетевой безопасности][NetworkSecurityGroups]


## Ограничение доступа с помощью сетевых списков управления доступом

Порт 1433 можно защитить с помощью сетевого списка управления доступом. В примере ниже адреса клиентов из виртуальной сети добавляются в белый список, а доступ ко всем других клиентам блокируется.

![Пример сетевого списка управления доступом][NetworkAccessControlListExample]

Все приложения, работающего в среде службы приложений в той же виртуальной сети, что и SQL Server, смогут подключиться к экземпляру SQL Server с помощью внутреннего IP-адреса **виртуальной сети** для виртуальной машины SQL Server.

Пример строки подключения ниже ссылается на SQL Server, использующий частный IP-адрес.

    Server=tcp:10.0.1.6;Database=MyDatabase;User ID=MyUser;Password=PasswordHere;provider=System.Data.SqlClient

Хотя виртуальная машина имеет также общедоступную конечную точку, попытки подключения с использованием общедоступного IP-адреса будут отклонены из-за использования сетевых списков управления доступом.

## Ограничение доступа с помощью группы сетевой безопасности
Альтернативный подход для обеспечения безопасного доступа заключается в использовании группы сетевой безопасности. Группы сетевой безопасности можно применять к отдельным виртуальным машинам или к подсети, содержащей виртуальные машины.

Сначала необходимо создать группу сетевой безопасности:

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

Ограничить доступ только для внутреннего трафика виртуальной сети очень просто с помощью группы сетевой безопасности. Правила по умолчанию в группе сетевой безопасности разрешают доступ только от других сетевых клиентов в той же виртуальной сети.

В результате, блокирование доступа к SQL Server сводится к применению группы сетевой безопасности с ее правилами по умолчанию либо к работающим виртуальным машинам, где запущен SQL Server, либо к подсети, содержащей виртуальные машины.

В следующем примере группа сетевой безопасности применяется к содержащей подсети:

    Get-AzureNetworkSecurityGroup -Name "testNSGExample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-1'
    
Конечным результатом является набор правил безопасности, которые блокируют внешний доступ, предоставляя внутренний доступ из виртуальной сети:

![Правила сетевой безопасности по умолчанию][DefaultNetworkSecurityRules]


## Приступая к работе
Все статьи и практические руководства, посвященные средам службы приложений, доступны в [файле сведений для сред службы приложений](../app-service/app-service-app-service-environments-readme.md).

Чтобы начать работу со средами службы приложений, см. статью [Введение в среду службы приложений][IntroToAppServiceEnvironment]

Подробности об управлении входящим трафиком в среде службы приложений см. раздел [Управление входящим трафиком в среде службы приложений][ControlInboundASE]

Дополнительные сведения о платформе службы приложений Azure см. в статье [Служба приложений Azure][AzureAppService].

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]
 

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[ControlInboundTraffic]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[SiteToSite]: https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[NetworkAccessControlLists]: https://azure.microsoft.com/documentation/articles/virtual-networks-acl/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[IntroToAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[ControlInboundASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/

<!-- IMAGES -->
[SqlServerEndpoint]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/SqlServerEndpoint01.png
[NetworkAccessControlListExample]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/NetworkAcl01.png
[DefaultNetworkSecurityRules]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/DefaultNetworkSecurityRules01.png

<!---HONumber=AcomDC_0713_2016-->