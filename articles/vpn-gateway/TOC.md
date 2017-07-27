# Обзор
## [Основные сведения о VPN-шлюзах Azure](vpn-gateway-about-vpngateways.md)
## [VPN-шлюз: вопросы и ответы](vpn-gateway-vpn-faq.md)
## [Ограничения подписки и службы](../azure-subscription-service-limits.md?toc=%2fazure%2fvpn-gateway%2ftoc.json)

# Начало работы
## [Планирование и проектирование VPN-шлюза](vpn-gateway-plan-design.md)
## [Сведения о параметрах VPN-шлюза](vpn-gateway-about-vpn-gateway-settings.md)
## [О VPN-устройствах](vpn-gateway-about-vpn-devices.md)
## [О требованиях к криптографии](vpn-gateway-about-compliance-crypto.md)
## [О BGP и VPN-шлюзе](vpn-gateway-bgp-overview.md)
## [Основные сведения о высокодоступных подключениях](vpn-gateway-highlyavailable.md)

# Практическое руководство
## Настройка подключения "сеть — сеть"
### [Портал Azure](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
### [PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
### [Интерфейс командной строки Azure](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
### [Портал Azure (классический)](vpn-gateway-howto-site-to-site-classic-portal.md)
### [Классический портал (классическая модель)](vpn-gateway-site-to-site-create.md)
## Настройка подключения "точка — сеть"
### [Портал Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
### [PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
### [Портал Azure (классический)](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
### Создание самозаверяющих сертификатов для подключений типа "точка — сеть"
#### [PowerShell](vpn-gateway-certificates-point-to-site.md)
#### [Makecert](vpn-gateway-certificates-point-to-site-makecert.md)
## Настройка подключения между виртуальными сетями
### [Портал Azure](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
### [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
### [Интерфейс командной строки Azure](vpn-gateway-howto-vnet-vnet-cli.md)
### [Портал Azure (классический)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
## Настройка подключения между виртуальными сетями для разных моделей развертывания
### [Портал Azure](vpn-gateway-connect-different-deployment-models-portal.md)
### [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
## Настройка параллельных подключений "сеть —сеть" и ExpressRoute
### [PowerShell](../expressroute/expressroute-howto-coexist-resource-manager.md?toc=%2fazure%2fvpn-gateway%2ftoc.json)
## Настройка нескольких подключений типа "сеть — сеть"
### [Портал Azure](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
### [PowerShell (классическая модель)](vpn-gateway-multi-site.md)
## Подключение нескольких VPN-устройств на основе политик
### [PowerShell](vpn-gateway-connect-multiple-policybased-rm-ps.md)
## Настройка политик IPsec/IKE для подключений
### [PowerShell](vpn-gateway-ipsecikepolicy-rm-powershell.md)
## Настройка высокодоступных подключений в режиме "активный — активный"
### [PowerShell](vpn-gateway-activeactive-rm-powershell.md)
## Настройка BGP для VPN-шлюза
### [PowerShell](vpn-gateway-bgp-resource-manager-ps.md)
## Настройка принудительного туннелирования
### [PowerShell](vpn-gateway-forced-tunneling-rm.md)
### [PowerShell (классическая модель)](vpn-gateway-about-forced-tunneling.md)
## Изменение настроек локального сетевого шлюза
### [Портал Azure](vpn-gateway-modify-local-network-gateway-portal.md)
### [PowerShell](vpn-gateway-modify-local-network-gateway.md)
### [Интерфейс командной строки Azure](vpn-gateway-modify-local-network-gateway-cli.md)
## [Проверка подключения VPN-шлюза](vpn-gateway-verify-connection-resource-manager.md)
## [Сброс VPN-шлюза](vpn-gateway-resetgw-classic.md)
## Удаление VPN-шлюза
### [Портал Azure](vpn-gateway-delete-vnet-gateway-portal.md)
### [PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
### [PowerShell (классическая модель)](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
## [Настройка VPN-шлюза (классическая модель)](vpn-gateway-configure-vpn-gateway-mp.md)
## [SKU шлюзов (старая версия)](vpn-gateway-about-skus-legacy.md)
## Настройка сторонних VPN-устройств
### [Общие сведения и конфигурация Azure](vpn-gateway-3rdparty-device-config-overview.md)
### [Пример: устройство Cisco ASA (IKEv2/без BGP)](vpn-gateway-3rdparty-device-config-cisco-asa.md)
## Устранение неполадок
### [Проверка пропускной способности VPN для виртуальной сети](vpn-gateway-validate-throughput-to-vnet.md)
### [Предлагаемые сообществом параметры устройств VPN или брандмауэра](vpn-gateway-third-party-settings.md)
### [Проблемы с подключением типа "точка — сеть"](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
### [Периодические разрывы подключения типа "сеть —сеть"](vpn-gateway-troubleshoot-site-to-site-disconnected-intermittently.md)
### [Сбой подключения типа "сеть — сеть"](vpn-gateway-troubleshoot-site-to-site-cannot-connect.md) 

# Справочные материалы
## [PowerShell](/powershell/module/azurerm.network/?view=azurermps-4.0.0#vpn)
## [PowerShell (классическая модель)](/powershell/module/azure/?view=azuresmps-3.7.0#networking)
## [REST](/rest/api/network/virtualnetworkgateways)
## [REST (классическая модель)](https://msdn.microsoft.com/library/jj154113)
## [Интерфейс командной строки Azure](/cli/azure/network/vnet-gateway)

# Сопутствующие материалы
## [Виртуальная сеть](/azure/virtual-network/)
## [Шлюз приложений](/azure/application-gateway/)
## [Azure DNS](/azure/dns/)
## [Диспетчер трафика](/azure/traffic-manager/)
## [Балансировщик нагрузки](/azure/load-balancer/)
## [ExpressRoute](/azure/expressroute/)

# Ресурсы
## [Стратегия развития Azure](https://azure.microsoft.com/roadmap/?category=networking)
## [Блог](https://azure.microsoft.com/blog/topics/networking)
## [Форум](https://social.msdn.microsoft.com/Forums/azure/home?forum=WAVirtualMachinesVirtualNetwork)
## [Цены](https://azure.microsoft.com/pricing/details/vpn-gateway)
## [СОГЛАШЕНИЕ ОБ УРОВНЕ ОБСЛУЖИВАНИЯ](https://azure.microsoft.com/support/legal/sla)
## [Видеоролики](https://azure.microsoft.com/documentation/videos/index/?services=vpn-gateway)
