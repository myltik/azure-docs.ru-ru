# Обзор
## [Виртуальные сети](virtual-networks-overview.md)
## [Определяемые пользователем маршруты и IP-пересылка](virtual-networks-udr-overview.md)
## [Пиринг между виртуальными сетями](virtual-network-peering-overview.md)
## [Непрерывность бизнес-процессов](virtual-network-disaster-recovery-guidance.md)
## [Часто задаваемые вопросы](virtual-networks-faq.md)
## Назначение IP-адресов
### [Диспетчер ресурсов](virtual-network-ip-addresses-overview-arm.md)
### [Классический](virtual-network-ip-addresses-overview-classic.md)
## Виртуальные машины
### [Сетевые интерфейсы](virtual-network-network-interface-overview.md)
### [Разрешение имен](virtual-networks-name-resolution-for-vms-and-role-instances.md)

# Начало работы
## [Создание виртуальной сети](virtual-networks-create-vnet-arm-pportal.md)
## [Развертывание виртуальной машины в виртуальной сети](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

# Практическое руководство:
## Планирование и проектирование
### [Виртуальные сети](virtual-network-vnet-plan-design-arm.md)
### [Группы безопасности сети](virtual-networks-nsg.md)

## Развернуть
### виртуальные сети;
#### [Портал](virtual-networks-create-vnet-arm-pportal.md)
#### [PowerShell](virtual-networks-create-vnet-arm-ps.md)
#### [ИНТЕРФЕЙС КОМАНДНОЙ СТРОКИ](virtual-networks-create-vnet-arm-cli.md)
#### [Шаблон](virtual-networks-create-vnet-arm-template-click.md)
#### [Портал (классический)](virtual-networks-create-vnet-classic-pportal.md)
#### [PowerShell (классическая модель)](virtual-networks-create-vnet-classic-netcfg-ps.md)
#### [Интерфейс командной строки (классическая модель)](virtual-networks-create-vnet-classic-cli.md)

### Группы безопасности сети
#### [Портал](virtual-networks-create-nsg-arm-pportal.md)
#### [PowerShell](virtual-networks-create-nsg-arm-ps.md)
#### [ИНТЕРФЕЙС КОМАНДНОЙ СТРОКИ](virtual-networks-create-nsg-arm-cli.md)
#### [Шаблон](virtual-networks-create-nsg-arm-template.md)
#### [PowerShell (классическая модель)](virtual-networks-create-nsg-classic-ps.md)
#### [Интерфейс командной строки (классическая модель)](virtual-networks-create-nsg-classic-cli.md)

### Определяемые пользователем маршруты
#### [PowerShell](virtual-network-create-udr-arm-ps.md)
#### [ИНТЕРФЕЙС КОМАНДНОЙ СТРОКИ](virtual-network-create-udr-arm-cli.md)
#### [Шаблон](virtual-network-create-udr-arm-template.md)
#### [PowerShell (классическая модель)](virtual-network-create-udr-classic-ps.md)
#### [Интерфейс командной строки (классическая модель)](virtual-network-create-udr-classic-cli.md)

### Пиринг между виртуальными сетями
#### [Портал](virtual-networks-create-vnetpeering-arm-portal.md)
#### [PowerShell](virtual-networks-create-vnetpeering-arm-ps.md)
#### [Шаблон](virtual-networks-create-vnetpeering-arm-template-click.md)

### Виртуальные машины

#### Статические общедоступные IP-адреса
##### [Портал](virtual-network-deploy-static-pip-arm-portal.md)
##### [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
##### [ИНТЕРФЕЙС КОМАНДНОЙ СТРОКИ](virtual-network-deploy-static-pip-arm-cli.md)
##### [Шаблон](virtual-network-deploy-static-pip-arm-template.md)
##### [PowerShell (классическая модель)](virtual-networks-reserved-public-ip.md)

#### Статические частные IP-адреса
##### [Портал](virtual-networks-static-private-ip-arm-pportal.md)
##### [PowerShell](virtual-networks-static-private-ip-arm-ps.md)
##### [ИНТЕРФЕЙС КОМАНДНОЙ СТРОКИ](virtual-networks-static-private-ip-arm-cli.md)
##### [Портал (классический)](virtual-networks-static-private-ip-classic-pportal.md)
##### [PowerShell (классическая модель)](virtual-networks-static-private-ip-classic-ps.md)
##### [Интерфейс командной строки (классическая модель)](virtual-networks-static-private-ip-classic-cli.md)

#### Несколько сетевых интерфейсов
##### [PowerShell](virtual-network-deploy-multinic-arm-ps.md)
##### [ИНТЕРФЕЙС КОМАНДНОЙ СТРОКИ](virtual-network-deploy-multinic-arm-cli.md)
##### [Шаблон](virtual-network-deploy-multinic-arm-template.md)
##### [PowerShell (классическая модель)](virtual-network-deploy-multinic-classic-ps.md)
##### [Интерфейс командной строки (классическая модель)](virtual-network-deploy-multinic-classic-cli.md)

#### Несколько IP-адресов
##### [Портал Azure](virtual-network-multiple-ip-addresses-portal.md)
##### [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)

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
##### [Простая сеть периметра с группами безопасности сети](virtual-networks-dmz-nsg-asm.md)
##### [Сеть периметра с брандмауэром и группами безопасности сети](virtual-networks-dmz-nsg-fw-asm.md)
##### [Сеть периметра с брандмауэром, определяемым пользователем маршрутом и группами безопасности сети](virtual-networks-dmz-nsg-fw-udr-asm.md)
##### [Пример приложения](virtual-networks-sample-app.md)

## Настройка
### Ускорение работы в сети
#### [Портал Azure](virtual-network-accelerated-networking-portal.md)
#### [PowerShell](virtual-network-accelerated-networking-powershell.md)
### Доступ к спискам управления
#### [Классический портал](virtual-networks-acl.md)
#### [PowerShell](virtual-networks-acl-powershell.md)

## Управление
### Группы безопасности сети
#### [Портал](virtual-network-manage-nsg-arm-portal.md)
#### [PowerShell](virtual-network-manage-nsg-arm-ps.md)
#### [ИНТЕРФЕЙС КОМАНДНОЙ СТРОКИ](virtual-network-manage-nsg-arm-cli.md)
#### [Журналы](virtual-network-nsg-manage-log.md)
#### Устранение неполадок
##### [Портал](virtual-network-nsg-troubleshoot-portal.md)
##### [PowerShell](virtual-network-nsg-troubleshoot-powershell.md)
### Устранение неполадок маршрутов
#### [Портал](virtual-network-routes-troubleshoot-portal.md)
#### [PowerShell](virtual-network-routes-troubleshoot-powershell.md)
### Виртуальные машины
#### [Просмотр и изменение имен узлов](virtual-networks-viewing-and-modifying-hostnames.md)
#### [Перемещение виртуальной машины в другую подсеть](virtual-networks-move-vm-role-to-subnet.md)

# Справочные материалы
## [PowerShell (Resource Manager)](https://msdn.microsoft.com/library/mt163510(v=azure.300))
## [PowerShell (классическая модель)](https://msdn.microsoft.com/library/mt270335(v=azure.300))
## [Интерфейс командной строки Azure](/cli/azure/)
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
## [Блог о сетях](http://azure.microsoft.com/blog/topics/networking)
## [Форум по сетям](https://social.msdn.microsoft.com/Forums/azure/home?forum=WAVirtualMachinesVirtualNetwork)
## [Цены](https://azure.microsoft.com/pricing/details/virtual-network)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-virtual-network)


<!--HONumber=Nov16_HO3-->


