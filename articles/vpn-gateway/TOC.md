# [Документация по VPN-шлюзам](index.md)

# Обзор
## [Основные сведения о VPN-шлюзах Azure](vpn-gateway-about-vpngateways.md)
## [VPN-шлюз: вопросы и ответы](vpn-gateway-vpn-faq.md)
## [Ограничения подписки и службы](../azure-subscription-service-limits.md?toc=%2fazure%2fvpn-gateway%2ftoc.json)

# Начало работы
## Создание VPN-шлюза на основе маршрутов
### [портал Azure](create-routebased-vpn-gateway-portal.md)
### [Azure PowerShell](create-routebased-vpn-gateway-powershell.md)
### [интерфейс командной строки Azure](create-routebased-vpn-gateway-cli.md)

# Основные понятия
## [Планирование и проектирование VPN-шлюза](vpn-gateway-plan-design.md)
## [Сведения о параметрах VPN-шлюза](vpn-gateway-about-vpn-gateway-settings.md)
## [О VPN-устройствах](vpn-gateway-about-vpn-devices.md)
## [О требованиях к криптографии](vpn-gateway-about-compliance-crypto.md)
## [О BGP и VPN-шлюзе](vpn-gateway-bgp-overview.md)
## [Основные сведения о высокодоступных подключениях](vpn-gateway-highlyavailable.md)
## [Сведения о подключениях "точка — сеть"](point-to-site-about.md)

# Практическое руководство
## Настройка подключений "сеть — сеть"
### [портал Azure](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
### [Azure PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
### [интерфейс командной строки Azure](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
### [Портал Azure (классический)](vpn-gateway-howto-site-to-site-classic-portal.md)

## [Загрузка скриптов конфигурации VPN-устройства](vpn-gateway-download-vpndevicescript.md)

## Настройка подключений "точка — сеть". Собственная проверка подлинности Azure на основе сертификата
### Настройка VPN для подключений "точка — сеть"
#### [портал Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
#### [Azure PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
#### [Портал Azure (классический)](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
### Создание самозаверяющих сертификатов
#### [Azure PowerShell](vpn-gateway-certificates-point-to-site.md)
#### [Makecert](vpn-gateway-certificates-point-to-site-makecert.md)
### [Создание и установка файлов конфигурации VPN-клиента](point-to-site-vpn-client-configuration-azure-cert.md)
### [Установка сертификатов клиента](point-to-site-how-to-vpn-client-install-azure-cert.md)

## Настройка подключений "точка — сеть". Проверка подлинности RADIUS
### Настройка VPN для подключений "точка — сеть"
#### [Azure PowerShell](point-to-site-how-to-radius-ps.md)
### [Создание и установка файлов конфигурации VPN-клиента](point-to-site-vpn-client-configuration-radius.md)
### [Интеграция аутентификации VPN-подключений "точка — сеть" по протоколу RADIUS и сервера политики сети](vpn-gateway-radiuis-mfa-nsp.md)

## Настройка подключений между виртуальными сетями
### [портал Azure](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
### [Azure PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
### [интерфейс командной строки Azure](vpn-gateway-howto-vnet-vnet-cli.md)
### [Портал Azure (классический)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
## Настройка подключения между виртуальными сетями для разных моделей развертывания
### [портал Azure](vpn-gateway-connect-different-deployment-models-portal.md)
### [Azure PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
## Настройка параллельных подключений "сеть —сеть" и ExpressRoute
### [Azure PowerShell](../expressroute/expressroute-howto-coexist-resource-manager.md?toc=%2fazure%2fvpn-gateway%2ftoc.json)
## Настройка нескольких подключений типа "сеть — сеть"
### [портал Azure](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
### [Azure PowerShell (классическая модель)](vpn-gateway-multi-site.md)
## Подключение нескольких VPN-устройств на основе политик
### [Azure PowerShell](vpn-gateway-connect-multiple-policybased-rm-ps.md)
## Настройка политик IPsec/IKE для подключений
### [Azure PowerShell](vpn-gateway-ipsecikepolicy-rm-powershell.md)
## Настройка высокодоступных подключений в режиме "активный — активный"
### [Azure PowerShell](vpn-gateway-activeactive-rm-powershell.md)
## Настройка BGP для VPN-шлюза
### [Azure PowerShell](vpn-gateway-bgp-resource-manager-ps.md)
### [интерфейс командной строки Azure](bgp-how-to-cli.md)
## Настройка принудительного туннелирования
### [Azure PowerShell](vpn-gateway-forced-tunneling-rm.md)
### [Azure PowerShell (классическая модель)](vpn-gateway-about-forced-tunneling.md)
## Изменение настроек локального сетевого шлюза
### [портал Azure](vpn-gateway-modify-local-network-gateway-portal.md)
### [Azure PowerShell](vpn-gateway-modify-local-network-gateway.md)
### [интерфейс командной строки Azure](vpn-gateway-modify-local-network-gateway-cli.md)
## [Проверка подключения VPN-шлюза](vpn-gateway-verify-connection-resource-manager.md)
## [Сброс VPN-шлюза](vpn-gateway-resetgw-classic.md)
## Удаление VPN-шлюза
### [портал Azure](vpn-gateway-delete-vnet-gateway-portal.md)
### [Azure PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
### [Azure PowerShell (классическая модель)](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
## [Настройка VPN-шлюза (классическая модель)](vpn-gateway-configure-vpn-gateway-mp.md)
## [SKU шлюзов (устаревшая версия)](vpn-gateway-about-skus-legacy.md)
## Настройка сторонних VPN-устройств
### [Общие сведения и конфигурация Azure](vpn-gateway-3rdparty-device-config-overview.md)
### [Пример: устройство Cisco ASA (IKEv2/без BGP)](vpn-gateway-3rdparty-device-config-cisco-asa.md)
## [Миграция из классической модели развертывания в модель Resource Manager](vpn-gateway-classic-resource-manager-migration.md)
## [Устранение неполадок](vpn-gateway-troubleshoot.md)
### [Предлагаемые сообществом параметры устройств VPN или брандмауэра](vpn-gateway-third-party-settings.md)
### [Настройка и проверка VPN-подключений и подключений между виртуальными сетями](https://support.microsoft.com/help/4032151/configuring-and-validating-vnet-or-vpn-connections)
### [Проверка пропускной способности VPN для виртуальной сети](vpn-gateway-validate-throughput-to-vnet.md)
### Проблемы с подключением "точка — сеть"
#### [Проблемы с подключением "точка — сеть"](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
#### [Проблемы с подключением "точка — сеть" для VPN-клиента Mac OS X ](vpn-gateway-troubleshoot-point-to-site-osx-ikev2.md)
### Проблемы с подключением "сеть-сеть"
#### [Проблемы с подключением "сеть — сеть"](vpn-gateway-troubleshoot-site-to-site-cannot-connect.md)
#### [Периодические разрывы подключения типа "сеть —сеть"](vpn-gateway-troubleshoot-site-to-site-disconnected-intermittently.md)


# Справочные материалы
## [Azure PowerShell](/powershell/module/azurerm.network/?view=azurermps-4.0.0#vpn)
## [Azure PowerShell (классическая модель)](/powershell/module/azure/?view=azuresmps-3.7.0#networking)
## [REST](/rest/api/network/virtualnetworkgateways)
## [REST (классическая модель)](https://msdn.microsoft.com/library/jj154113)
## [интерфейс командной строки Azure](/cli/azure/network/vnet-gateway)

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
## [Калькулятор цен](https://azure.microsoft.com/pricing/calculator/)
## [Соглашение об уровне обслуживания](https://azure.microsoft.com/support/legal/sla)
## [Видеоролики](https://azure.microsoft.com/documentation/videos/index/?services=vpn-gateway)
