# Обзор
## [Виртуальные сети](virtual-networks-overview.md)
## [Маршрутизация](virtual-networks-udr-overview.md)
## [Пиринг между виртуальными сетями](virtual-network-peering-overview.md)
## [Конечные точки службы виртуальной сети](virtual-network-service-endpoints-overview.md)
## [Виртуальная сеть для служб Azure](virtual-network-for-azure-services.md)
## [Безопасность](security-overview.md)
## [Работа с контейнерами в сети](container-networking.md)
## [Непрерывность бизнес-процессов](virtual-network-disaster-recovery-guidance.md)
## [Назначение IP-адресов](virtual-network-ip-addresses-overview-arm.md)
## [Защита от атак DDoS](ddos-protection-overview.md)
## [Часто задаваемые вопросы](virtual-networks-faq.md)
## Классический
### [Назначение IP-адресов](virtual-network-ip-addresses-overview-classic.md)
### [Списки управления доступом](virtual-networks-acl.md)

# Начало работы
## [Создание первой собственной виртуальной сети](virtual-network-get-started-vnet-subnet.md)

# Практическое руководство
## Планирование и проектирование
### [Виртуальные сети](virtual-network-vnet-plan-design-arm.md)
### [Группы безопасности сети](virtual-networks-nsg.md)

## Развернуть
### [Виртуальные сети](virtual-networks-create-vnet-arm-pportal.md)
#### [Azure PowerShell](virtual-networks-create-vnet-arm-ps.md)
#### [Интерфейс командной строки Azure](virtual-networks-create-vnet-arm-cli.md)
#### [Шаблон](virtual-networks-create-vnet-arm-template-click.md)

### Группы безопасности сети
#### [Портал Azure](virtual-networks-create-nsg-arm-pportal.md)
#### [Azure PowerShell](virtual-networks-create-nsg-arm-ps.md)
#### [Интерфейс командной строки Azure](virtual-networks-create-nsg-arm-cli.md)
#### [Шаблон](virtual-networks-create-nsg-arm-template.md)
#### [Группы безопасности приложений](create-network-security-group-preview.md)
#### Классический
##### [Azure PowerShell](virtual-networks-create-nsg-classic-ps.md)
##### [Azure CLI 1.0](virtual-networks-create-nsg-classic-cli.md)

### Определяемые пользователем маршруты
#### [Портал Azure](create-user-defined-route-portal.md)
#### [Azure PowerShell](virtual-network-create-udr-arm-ps.md)
#### [Интерфейс командной строки Azure](virtual-network-create-udr-arm-cli.md)
#### [Шаблон](virtual-network-create-udr-arm-template.md)
#### Классический
##### [Azure PowerShell](virtual-network-create-udr-classic-ps.md)
##### [Интерфейс командной строки Azure](virtual-network-create-udr-classic-cli.md)

### Пиринг между виртуальными сетями
#### [Одна модель развертывания — одна подписка](virtual-network-create-peering.md)
#### [Одна модель развертывания — разные подписки](create-peering-different-subscriptions.md)
#### [Разные модели развертывания — одна подписка](create-peering-different-deployment-models.md)
#### [Разные модели развертывания — разные подписки](create-peering-different-deployment-models-subscriptions.md)

### [Конечные точки службы виртуальной сети](virtual-network-service-endpoints-configure.md)

### Общедоступный IP-адрес — зона доступности
#### [Портал Azure](create-public-ip-availability-zone-portal.md)
#### [Интерфейс командной строки Azure](create-public-ip-availability-zone-cli.md)
#### [PowerShell](create-public-ip-availability-zone-powershell.md)

### Виртуальные машины
#### [Пропускная способность сети на виртуальных машинах](virtual-machine-network-throughput.md)
#### Создание виртуальной машины со статическим общедоступным IP-адресом
##### [Портал Azure](virtual-network-deploy-static-pip-arm-portal.md)
##### [Azure PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
##### [Интерфейс командной строки Azure](virtual-network-deploy-static-pip-arm-cli.md)
##### [Шаблон](virtual-network-deploy-static-pip-arm-template.md)
##### Классический
###### [Azure PowerShell](virtual-networks-reserved-public-ip.md)

#### Создание виртуальной машины со статическим частным IP-адресом
##### [Портал Azure](virtual-networks-static-private-ip-arm-pportal.md)
##### [Azure PowerShell](virtual-networks-static-private-ip-arm-ps.md)
##### [Интерфейс командной строки Azure](virtual-networks-static-private-ip-arm-cli.md)
##### Классический
###### [Портал Azure](virtual-networks-static-private-ip-classic-pportal.md)
###### [Azure PowerShell](virtual-networks-static-private-ip-classic-ps.md)
###### [Интерфейс командной строки Azure](virtual-networks-static-private-ip-classic-cli.md)

#### Создание виртуальной машины с несколькими сетевыми интерфейсами
##### [Azure PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
##### [Интерфейс командной строки Azure](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
##### [Шаблон](virtual-network-deploy-multinic-arm-template.md)

##### Классический
###### [Azure PowerShell](virtual-network-deploy-multinic-classic-ps.md)
###### [Интерфейс командной строки Azure](virtual-network-deploy-multinic-classic-cli.md)

#### Создание виртуальной машины с несколькими IP-адресами
##### [Портал Azure](virtual-network-multiple-ip-addresses-portal.md)
##### [Azure PowerShell](virtual-network-multiple-ip-addresses-powershell.md)
##### [Интерфейс командной строки Azure](virtual-network-multiple-ip-addresses-cli.md)
##### [Шаблон](virtual-network-multiple-ip-addresses-template.md)

#### [Создание виртуальной машины с ускоренной сетью](virtual-network-create-vm-accelerated-networking.md)

### Сценарии подключения
#### [Виртуальная сеть к виртуальной сети](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [Виртуальная сеть (Resource Manager) к виртуальной сети (классическая модель)](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [Виртуальная сеть к локальной сети (VPN)](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [Виртуальная сеть к локальной сети (ExpressRoute)](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [Архитектура высокодоступной гибридной сети](../guidance/guidance-hybrid-network-expressroute-vpn-failover.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

### Сценарии обеспечения безопасности
#### [Защита сетей с использованием виртуальных устройств](virtual-network-scenario-udr-gw-nva.md)
#### [Сеть периметра между Azure и Интернетом](../guidance/guidance-iaas-ra-secure-vnet-dmz.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [Облачная служба и сетевая безопасность](../best-practices-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
##### [Создание сети периметра с группами безопасности сети](virtual-networks-dmz-nsg.md)
##### [Создание сети периметра с группами безопасности сети (классическая модель)](virtual-networks-dmz-nsg-asm.md)
##### [Создание сети периметра с брандмауэром и группами безопасности сети (классическая модель)](virtual-networks-dmz-nsg-fw-asm.md)
##### [Сеть периметра с брандмауэром, определяемым пользователем маршрутом и группами безопасности сети (классическая модель)](virtual-networks-dmz-nsg-fw-udr-asm.md)

##### [Пример приложения](virtual-networks-sample-app.md)

### Классический
#### [Виртуальная сеть](create-virtual-network-classic.md)
##### [Портал Azure](virtual-networks-create-vnet-classic-pportal.md)
##### [Azure PowerShell](virtual-networks-create-vnet-classic-netcfg-ps.md)
##### [Интерфейс командной строки Azure](virtual-networks-create-vnet-classic-cli.md)
#### [Указание параметров DNS в файле конфигурации виртуальной сети](virtual-networks-specifying-a-dns-settings-in-a-virtual-network-configuration-file.md)
#### [Указание параметров DNS в файле конфигурации службы](virtual-networks-specifying-dns-settings-in-a-service-configuration-file.md)

## Настройка
### Виртуальные машины
#### [Добавление и удаление сетевых интерфейсов](virtual-network-network-interface-vm.md)
#### [Разрешение имен для виртуальных машин и облачных служб](virtual-networks-name-resolution-for-vms-and-role-instances.md)
#### [Использование динамических DNS для регистрации имен узлов на собственном DNS-сервере](virtual-networks-name-resolution-ddns.md)
#### [Оптимизация пропускной способности сети](virtual-network-optimize-network-bandwidth.md)
#### [Просмотр и изменение имен узлов](virtual-networks-viewing-and-modifying-hostnames.md)
#### Классический
##### Статические IP-адреса
###### [PowerShell](virtual-networks-reserved-private-ip.md)
###### [ИНТЕРФЕЙС КОМАНДНОЙ СТРОКИ](virtual-networks-static-private-ip-classic-cli.md)
##### [Общие сведения об общедоступных IP-адресах уровня экземпляра](virtual-networks-instance-level-public-ip.md)

### Классический
#### Доступ к спискам управления
##### [Портал Azure](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
##### [Azure PowerShell](virtual-networks-acl-powershell.md)

## Управление
### [Виртуальные сети](virtual-network-manage-network.md)
#### [Подсети](virtual-network-manage-subnet.md)
#### [Пиринг](virtual-network-manage-peering.md)
#### Классический
##### [Файл конфигурации сети](virtual-networks-using-network-configuration-file.md)
##### [Миграция из территориальной группы в регион](virtual-networks-migrate-to-regional-vnet.md)
### Группы безопасности сети
#### [Портал Azure](virtual-network-manage-nsg-arm-portal.md)
#### [Azure PowerShell](virtual-network-manage-nsg-arm-ps.md)
#### [Интерфейс командной строки Azure](virtual-network-manage-nsg-arm-cli.md)

#### [Журналы](virtual-network-nsg-manage-log.md)
### Сетевые интерфейсы
#### [Создание, изменение и удаление сетевых интерфейсов](virtual-network-network-interface.md)
#### [Добавление, изменение и удаление IP-адресов](virtual-network-network-interface-addresses.md)
### Виртуальные машины
#### [Перемещение виртуальной машины в другую подсеть](virtual-networks-move-vm-role-to-subnet.md)
### [Типы IP-адресов и методы распределения в Azure](virtual-network-public-ip-address.md)
### Защита от атак DDoS
#### [Портал Azure](ddos-protection-manage-portal.md)
#### [Azure PowerShell](ddos-protection-manage-ps.md)

## Устранение неполадок
### Группы безопасности сети
#### [Портал Azure](virtual-network-nsg-troubleshoot-portal.md)
#### [Azure PowerShell](virtual-network-nsg-troubleshoot-powershell.md)
### Маршруты
#### [Портал Azure](virtual-network-routes-troubleshoot-portal.md)
#### [Azure PowerShell](virtual-network-routes-troubleshoot-powershell.md)
### [Тестирование пропускной способности](virtual-network-bandwidth-testing.md)
### [Не удается удалить виртуальные сети](virtual-network-troubleshoot-cannot-delete-vnet.md)
### [Проблемы с подключением между виртуальными машинами](virtual-network-troubleshoot-connectivity-problem-between-vms.md)

# Справочные материалы
## [Примеры кода](https://azure.microsoft.com/en-us/resources/samples/?service=virtual-network)
## [Azure PowerShell (Resource Manager)](/powershell/module/azurerm.network)
## [Azure PowerShell (классическая модель)](/powershell/module/azure/)
## [Интерфейс командной строки Azure](/cli/azure/network)
## [Java](/java/api/)
## [REST (Resource Manager)](https://msdn.microsoft.com/library/mt163658.aspx)
## [REST (классическая модель)](https://msdn.microsoft.com/library/jj157182.aspx)


# Сопутствующие материалы
## [Виртуальные машины](/azure/virtual-machines/)
## [Шлюз приложений](/azure/application-gateway/)
## [Azure DNS](/azure/dns/)
## [Диспетчер трафика](/azure/traffic-manager/)
## [Балансировщик нагрузки](/azure/load-balancer/)
## [VPN-шлюз](/azure/vpn-gateway/)
## [ExpressRoute](/azure/expressroute/)

# Ресурсы
## [Стратегия развития Azure](https://azure.microsoft.com/roadmap/?category=networking)
## [Блог о сетях](http://azure.microsoft.com/blog/topics/networking)
## [Форум по сетям](https://social.msdn.microsoft.com/Forums/azure/home?forum=WAVirtualMachinesVirtualNetwork)
## [Цены](https://azure.microsoft.com/pricing/details/virtual-network)
## [Калькулятор цен](https://azure.microsoft.com/pricing/calculator/)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-virtual-network)
## [Поставщик сетевых ресурсов](resource-groups-networking.md)
