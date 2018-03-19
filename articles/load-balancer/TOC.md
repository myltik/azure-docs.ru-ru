# [Документация по подсистеме балансировки нагрузки](index.md)

# Обзор
## [Что такое подсистема балансировки нагрузки?](load-balancer-overview.md)
## [Что такое подсистема балансировки нагрузки уровня "Стандартный"?](load-balancer-standard-overview.md)
## [Общедоступная подсистема балансировки нагрузки](load-balancer-internet-overview.md)
## [Внутренняя подсистема балансировки нагрузки](load-balancer-internal-overview.md)
## [Основные сведения о пробах подсистемы балансировки нагрузки](load-balancer-custom-probe-overview.md)
## [Основные сведения о высокодоступных портах](load-balancer-ha-ports-overview.md)
## [Поддержка Azure Resource Manager](load-balancer-arm.md)
## [Поддержка протокола IPv6](load-balancer-ipv6-overview.md)
## [Использование нескольких виртуальных IP-адресов](load-balancer-multivip-overview.md)
## [Общие сведения об исходящих подключениях](load-balancer-outbound-connections.md)
## [Подсистема балансировки нагрузки уровня "Стандартный" и зоны доступности](load-balancer-standard-availability-zones.md)

# Начало работы

## [Настройка внутренней подсистемы балансировки нагрузки](load-balancer-get-started-ilb-arm-portal.md)
### [Настройка внутренней подсистемы балансировки нагрузки (PowerShell)](load-balancer-get-started-ilb-arm-ps.md)
### [Настройка внутренней подсистемы балансировки нагрузки (CLI)](load-balancer-get-started-ilb-arm-cli.md)
### [Настройка внутренней подсистемы балансировки нагрузки (шаблон)](load-balancer-get-started-ilb-arm-template.md)

## [Настройка общедоступной подсистемы балансировки нагрузки](load-balancer-get-started-internet-portal.md)
### [Настройка общедоступной подсистемы балансировки нагрузки (PowerShell)](load-balancer-get-started-internet-arm-ps.md)
### [Настройка общедоступной подсистемы балансировки нагрузки (CLI)](load-balancer-get-started-internet-arm-cli.md)
### [Настройка общедоступной подсистемы балансировки нагрузки (шаблон)](load-balancer-get-started-internet-arm-template.md)

## [Создание общедоступной подсистемы балансировки нагрузки с поддержкой IPv6](load-balancer-ipv6-internet-ps.md)
### [Создание общедоступной подсистемы балансировки нагрузки с поддержкой IPv6 (CLI)](load-balancer-ipv6-internet-cli.md)
### [Создание общедоступной подсистемы балансировки нагрузки с поддержкой IPv6 (шаблон)](load-balancer-ipv6-internet-template.md)

## [Создание избыточной в пределах зоны общедоступной подсистемы балансировки нагрузки уровня "Стандартный"](load-balancer-get-started-internet-az-portal.md)
### [Создание избыточной в пределах зоны общедоступной подсистемы балансировки нагрузки уровня "Стандартный" (PowerShell)](load-balancer-get-started-internet-az-powershell.md)
### [Создание избыточной в пределах зоны общедоступной подсистемы балансировки нагрузки уровня "Стандартный" (CLI)](load-balancer-get-started-internet-az-cli.md)

# Практическое руководство
## [Настройка времени ожидания простоя TCP для подсистемы балансировки нагрузки](load-balancer-tcp-idle-timeout.md)
## [Настройка режима распространения для подсистемы балансировки нагрузки](load-balancer-distribution-mode.md)
## [Настройка внутренней подсистемы балансировки нагрузки для SQL AlwaysOn](load-balancer-configure-sqlao.md)
## [Настройка нескольких виртуальных IP-адресов для облачной службы](load-balancer-multivip.md)
## [Комбинирование служб балансировки нагрузки](../traffic-manager/traffic-manager-load-balancing-azure.md?toc=%2fazure%2fload-balancer%2ftoc.json)
## [Использование конфигураций с несколькими IP-адресами](load-balancer-multiple-ip.md)
### [Использование конфигураций с несколькими IP-адресами (интерфейс командной строки)](load-balancer-multiple-ip-cli.md)
### [Использование конфигураций с несколькими IP-адресами (PowerShell)](load-balancer-multiple-ip-powershell.md)
## [Log Analytics для Azure Load Balancer](load-balancer-monitor-log.md)
## [Настройка DHCPv6 для виртуальных машин Linux](load-balancer-ipv6-for-linux.md)
## [Настройка высокодоступных портов для внутренней подсистемы балансировки нагрузки](load-balancer-configure-ha-ports.md)

## Устранение неполадок
### [Устранение неполадок Azure Load Balancer](load-balancer-troubleshoot.md)

## Статьи о классической модели развертывания
### [Исходящие подключения (классическое развертывание)](load-balancer-outbound-connections-classic.md)
### [Настройка внутренней подсистемы балансировки нагрузки для облачных служб](load-balancer-get-started-ilb-classic-cloud.md)
#### [Настройка внутренней подсистемы балансировки нагрузки для облачных служб (PowerShell)](load-balancer-get-started-ilb-classic-ps.md)
#### [Настройка внутренней подсистемы балансировки нагрузки для облачных служб (CLI)](load-balancer-get-started-ilb-classic-cli.md)
### [Настройка общедоступной подсистемы балансировки нагрузки (классическая модель: PowerShell)](load-balancer-get-started-internet-classic-ps.md)
#### [Настройка общедоступной подсистемы балансировки нагрузки (классическая модель: облачные службы)](load-balancer-get-started-internet-classic-cloud.md)
#### [Настройка общедоступной подсистемы балансировки нагрузки (классическая модель: CLI)](load-balancer-get-started-internet-classic-cli.md)

# Справочные материалы
## [Примеры кода](https://azure.microsoft.com/en-us/resources/samples/?service=load-balancer)
## [Azure PowerShell](/powershell/module/azurerm.network)
## [Azure CLI](/cli/azure/network/lb)
## [.NET](/dotnet/api/microsoft.azure.management.network.models)
## [Java](/java/api/com.microsoft.azure.management.network)
## [Node.js](http://azure.github.io/azure-sdk-for-node/azure-arm-network/latest/LoadBalancers.html)
## [Ruby](http://www.rubydoc.info/gems/azure_mgmt_network/Azure/ARM/Network/LoadBalancers)
## [Python](http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.mgmt.network.operations.html#azure.mgmt.network.operations.LoadBalancersOperations)
## [REST](https://msdn.microsoft.com/library/azure/mt163651.aspx)

# Сопутствующие материалы
## [Шлюз приложений](/azure/application-gateway/)
## [ExpressRoute](/azure/expressroute/)
## [Виртуальная сеть](/azure/virtual-network/)
## [VPN-шлюз](/azure/vpn-gateway/)
## [Виртуальная машина](/azure/virtual-machines/)
## [Диспетчер трафика](/azure/traffic-manager/)
## [DNS](/azure/dns/)

# Ресурсы
## [Стратегия развития Azure](https://azure.microsoft.com/roadmap/?category=networking)
## [Цены](https://azure.microsoft.com/pricing/details/load-balancer/)
## [Калькулятор цен](https://azure.microsoft.com/pricing/calculator/)
## [Обновления службы](https://azure.microsoft.com/updates/?product=load-balancer)
