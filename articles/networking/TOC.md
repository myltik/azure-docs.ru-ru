# Обзор
## [О сетях Azure](networking-overview.md)
## Архитектура
### [Виртуальные центры данных](networking-virtual-datacenter.md)
### [Асимметричная маршрутизация с использованием нескольких сетевых путей](../expressroute/expressroute-asymmetric-routing.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Проектирование безопасной сети](../best-practices-network-security.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Звездообразная топология](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)
### [Рекомендации по обеспечению сетевой безопасности](../security/azure-security-network-security-best-practices.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Сетевые виртуальные модули высокой доступности](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha )
### [Объединение методов балансировки нагрузки](../traffic-manager/traffic-manager-load-balancing-azure.md?toc=%2fazure%2fnetworking%2ftoc.json)
## Планирование и проектирование
### [Виртуальные сети](../virtual-network/virtual-network-vnet-plan-design-arm.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Возможность распределенного подключения через VPN](../vpn-gateway/vpn-gateway-plan-design.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Возможность распределенного подключения по выделенному частному каналу](../expressroute/expressroute-workflows.md?toc=%2fazure%2fnetworking%2ftoc.json)

##  Основные понятия
### [Виртуальные сети](../virtual-network/virtual-networks-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Балансировка сетевой нагрузки](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Балансировка нагрузки приложений](../application-gateway/application-gateway-introduction.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [DNS](../dns/dns-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Распределение трафика на основе DNS](../traffic-manager/traffic-manager-overview.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [VPN-подключение к локальной среде](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Выделенное подключение к локальной среде](../expressroute/expressroute-introduction.md?toc=%2fazure%2fnetworking%2ftoc.json)


# Начало работы
## [Создание первой собственной виртуальной сети](../virtual-network/quick-create-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)

# Практическое руководство
## Подключение к Интернету
### [Общедоступные серверы: балансировка сетевой нагрузки](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Общедоступные серверы: балансировка нагрузки приложений](../application-gateway/application-gateway-create-gateway-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Защита веб-приложений](../application-gateway/application-gateway-web-application-firewall-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Распределение трафика между расположениями](../traffic-manager/traffic-manager-configure-geographic-routing-method.md?toc=%2fazure%2fnetworking%2ftoc.json)
## Внутренние подключения
### [Частные серверы: балансировка сетевой нагрузки](../load-balancer/load-balancer-get-started-ilb-arm-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Частные серверы: балансировка нагрузки приложений](../application-gateway/application-gateway-ilb-arm.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Подключение виртуальных сетей в одном расположении](../virtual-network/virtual-networks-create-vnetpeering-arm-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Подключение виртуальных сетей в разных расположениях](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
## Возможность распределенного подключения
### [Создание VPN-подключения типа "сеть — сеть" (IPsec/IKE)](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Создание VPN-подключения типа "точка — сеть" (по протоколу SSTP с сертификатами)](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Создание выделенного частного подключения (ExpressRoute)](../expressroute/expressroute-howto-circuit-portal-resource-manager.md?toc=%2fazure%2fnetworking%2ftoc.json)

## управления
### [Общие сведения о мониторинге сетей](network-monitoring-overview.md)
### [Проверка использования ресурсов в соответствии с ограничениями Azure](check-usage-against-limits.md)
### [Просмотр топологии сети](../network-watcher/network-watcher-topology-powershell.md?toc=%2fazure%2fnetworking%2ftoc.json)

## Примеры сценариев
### [интерфейс командной строки Azure](cli-samples.md)
### [Azure PowerShell](powershell-samples.md)

## Учебники
### [Балансировка нагрузки виртуальных машин Linux в Azure для создания высокодоступного приложения](../virtual-machines/linux/tutorial-load-balance-nodejs.md?toc=%2fazure%2fnetworking%2ftoc.json)
### [Подключение компьютера к виртуальной сети](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json)

# Справочные материалы
## [интерфейс командной строки Azure](https://docs.microsoft.com/cli/azure/network)
## [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.network/?view=azurermps-3.8.0)
## [.Net](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.network?view=azuremgmtnetwork-9.1.0-preview)
## [Node.js](https://azure.microsoft.com/develop/nodejs/#azure-sdk)
## [REST](https://msdn.microsoft.com/library/mt163658.aspx)

# Ресурсы
## [Создание шаблонов](/azure/azure-resource-manager/resource-group-authoring-templates?toc=%2fazure%2fnetworking%2ftoc.json)
## [Стратегия развития Azure](https://azure.microsoft.com/roadmap/?category=networking)
## [Шаблоны, созданные сообществом](https://azure.microsoft.com/resources/templates/)
## [Блог о сетях](http://azure.microsoft.com/blog/topics/networking)
## [Цены](https://azure.microsoft.com/pricing)
## [Калькулятор цен](https://azure.microsoft.com/pricing/calculator/)
## [Доступность по регионам](https://azure.microsoft.com/regions/services/)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-virtual-network)
## [Видеоролики](https://azure.microsoft.com/resources/videos/index/?services=virtual-network)

