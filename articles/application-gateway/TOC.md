# [Документация по шлюзу приложений](index.md)

# Обзор
## [Что такое шлюз приложений?](application-gateway-introduction.md)
## [Шлюз приложений: часто задаваемые вопросы](application-gateway-faq.md)
## [Брандмауэр веб-приложения](application-gateway-web-application-firewall-overview.md)
### [Основные наборы правил и правила](application-gateway-crs-rulegroups-rules.md)
## [Мониторинг работоспособности](application-gateway-probe-overview.md)
## [Маршрутизация URL-адресов](application-gateway-url-route-overview.md)
## [Несколько сайтов](application-gateway-multi-site-overview.md)
## [Сквозное шифрование SSL](application-gateway-backend-ssl.md)
## [Политика SSL](application-gateway-ssl-policy-overview.md)
## [Перенаправление](application-gateway-redirect-overview.md)
## [Поддержка мультитенантного режима](application-gateway-web-app-overview.md)
## [WebSocket](application-gateway-websocket.md)
## [Ограничения служб](../azure-subscription-service-limits.md?toc=%2fazure%2fapplication-gateway%2ftoc.json#application-gateway-limits)
# Начало работы
## [портал Azure](application-gateway-create-gateway-portal.md)
## [Azure PowerShell](application-gateway-create-gateway-arm.md)
## [Интерфейс командной строки Azure](application-gateway-create-gateway-cli.md)
# Практическое руководство
## Создание шлюза приложений с помощью VMSS
### [Azure PowerShell](tutorial-create-vmss-powershell.md)
### [интерфейс командной строки Azure](tutorial-create-vmss-cli.md)
## [Использование шаблона](application-gateway-create-gateway-arm-template.md)
## Настройка брандмауэра веб-приложения
### [портал Azure](application-gateway-web-application-firewall-portal.md)
### [Azure PowerShell](application-gateway-web-application-firewall-powershell.md)
### [интерфейс командной строки Azure](application-gateway-web-application-firewall-cli.md)
## Настройка правил брандмауэра веб-приложения
### [портал Azure](application-gateway-customize-waf-rules-portal.md)
### [интерфейс командной строки Azure](application-gateway-customize-waf-rules-cli.md)
## Настройка SSL
### [портал Azure](application-gateway-ssl-portal.md)
### [Azure PowerShell](application-gateway-ssl-arm.md)
### [интерфейс командной строки Azure](application-gateway-ssl-cli.md)
### [Политика SSL](application-gateway-configure-ssl-policy-powershell.md)
## Настройка маршрутизации на основе путей URL-адресов
### [портал Azure](application-gateway-create-url-route-portal.md)
### [Azure PowerShell](application-gateway-create-url-route-arm-ps.md)
### [интерфейс командной строки Azure](application-gateway-create-url-route-cli.md)
## Настройка размещения нескольких сайтов
### [портал Azure](application-gateway-create-multisite-portal.md)
### [Azure PowerShell](application-gateway-create-multisite-azureresourcemanager-powershell.md)
### [интерфейс командной строки Azure](tutorial-multisite-cli.md)
## Настройка внешнего перенаправления
### [Azure PowerShell](tutorial-external-site-redirect-powershell.md)
### [интерфейс командной строки Azure](tutorial-external-site-redirect-cli.md)
## Настройка внутреннего перенаправления
### [Azure PowerShell](tutorial-internal-site-redirect-powershell.md)
### [интерфейс командной строки Azure](tutorial-internal-site-redirect-cli.md)
## Настройка перенаправления из HTTP в HTTPS
### [Azure PowerShell](tutorial-http-redirect-powershell.md)
### [интерфейс командной строки Azure](tutorial-http-redirect-cli.md)
## Настройка перенаправления URL-адресов
### [Azure PowerShell](tutorial-url-redirect-powershell.md)
### [интерфейс командной строки Azure](tutorial-url-redirect-cli.md)
## Настройка веб-приложений в качестве элементов серверного пула
### [Azure PowerShell](application-gateway-web-app-powershell.md)
## Настройка проб работоспособности
### [портал Azure](application-gateway-create-probe-portal.md)
### [Azure PowerShell](application-gateway-create-probe-ps.md)
## [Интеграция центра безопасности Azure](application-gateway-integration-security-center.md)
## [Комбинирование служб балансировки нагрузки](../traffic-manager/traffic-manager-load-balancing-azure.md?toc=%2fazure%2fapplication-gateway%2ftoc.json)
## [Настройка сквозного шифрования SSL](application-gateway-end-to-end-ssl-powershell.md)
## Устранение неполадок
### [Устранение ошибок в шлюзе приложений](application-gateway-troubleshooting-502.md)
### [Ведение журналов диагностики и метрики](application-gateway-diagnostics.md)
### [Проблемы сходства сеансов шлюза приложения](https://support.microsoft.com/help/4033827/troubleshooting-azure-application-gateway-session-affinity-issues)
# Справочные материалы
## [Azure PowerShell](/powershell/azure/overview)
## [интерфейс командной строки Azure](/cli/azure/network/application-gateway)
## [.NET](/dotnet/api)
## [Java](/java/api/com.microsoft.azure.management.network)
## [Node.js](http://azure.github.io/azure-sdk-for-node/azure-arm-network/latest/ApplicationGateways)
## [Ruby](http://www.rubydoc.info/gems/azure_mgmt_network/0.8.0/Azure/ARM/Network/ApplicationGateways)
## [Python](http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.mgmt.network.operations.html#azure.mgmt.network.operations.ApplicationGatewaysOperations)
## [REST](https://docs.microsoft.com/rest/api/applicationgateway)
# Сопутствующие материалы
## [ExpressRoute](/azure/expressroute/)
## [Виртуальная сеть](/azure/virtual-network/)
## [VPN-шлюз](/azure/vpn-gateway/)
## [Виртуальные машины](/azure/virtual-machines/)
## [Подсистема балансировки нагрузки](/azure/load-balancer/)
## [Диспетчер трафика](/azure/traffic-manager/)
# Ресурсы
## [Стратегия развития Azure](https://azure.microsoft.com/roadmap/?category=networking)
## [Форум MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=WAVirtualMachinesVirtualNetwork)
## [Цены](https://azure.microsoft.com/pricing/details/application-gateway/)
## [Калькулятор цен](https://azure.microsoft.com/pricing/calculator/)
## [Обновления службы](https://azure.microsoft.com/updates/?product=application-gateway)
## [Соглашение об уровне обслуживания](https://azure.microsoft.com/support/legal/sla/)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-application-gateway)
