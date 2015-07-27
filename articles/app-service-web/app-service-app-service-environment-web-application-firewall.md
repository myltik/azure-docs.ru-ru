<properties 
	pageTitle="Настройка брандмауэра веб-приложения (WAF) для среды службы приложений" 
	description="Узнайте, как настроить брандмауэр веб-приложения перед средой службы приложений." 
	services="app-service\web" 
	documentationCenter="" 
	authors="naziml" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/02/2015" 
	ms.author="naziml"/>

# Настройка брандмауэра веб-приложения (WAF) для среды службы приложений

## Обзор ##
Брандмауэры веб-приложений, например [Barracuda для Azure](https://www.barracuda.com/programs/azure), который доступен в [Azure Marketplace](http://azure.microsoft.com/marketplace/partners/barracudanetworks/waf-byol/), помогают защитить веб-приложения, проверяя входящий веб-трафик и блокируя внедрения кода SQL, межсайтовые сценарии, загрузку вредоносных программ, а также DDoS и другие атаки. Также они проверяют ответы от внутренних веб-серверов для предотвращения потери данных (DLP). В сочетании с изоляцией и дополнительным масштабированием, предоставляемым средами службы приложений, это обеспечивает идеальную среду для размещения важных коммерческих веб-приложений с большим трафиком, способных противостоять вредоносным запросам.

## Настройка ##
Мы настроим среду службы приложений за несколькими экземплярами Barracuda WAF с равномерной нагрузкой, чтобы только трафик от WAF мог достичь среды службы приложений. Так среда не будет доступна из промежуточной подсети. Мы также настроим диспетчер трафика Azure перед экземплярами Barracuda WAF, чтобы распределить нагрузку между несколькими центрами обработки данных и регионами Azure. Общая схема настройки выглядит примерно так:

![Архитектура][Architecture]

## Настройка среды службы приложений ##
Инструкции по настройке среды службы приложений, см. в соответствующей [документации](app-service-web-how-to-create-an-app-service-environment.md). После создания среды службы приложений можно создать в ней [веб-приложения](app-service-web-overview.md), [приложения API](app-service-api-apps-why-best-platform.md) и [мобильные приложения](app-service-mobile-value-prop-preview.md), которые будут защищены WAF, который мы настроим в следующем разделе.

## Настройка облачной службы Barracuda WAF ##
У нас есть [подробная статья](https://techlib.barracuda.com/WAF/AzureDeploy) о развертывании Barracuda WAF на виртуальной машине в Azure. Так как нам нужна отказоустойчивость и отсутствие единой точки отказа, необходимо развернуть по крайней мере 2 экземпляра виртуальных машин с WAF в той же облачной службе, следуя этим инструкциям.

### Добавление конечных точек в облачной службе ###
После создания в облачной службе двух и более экземпляров виртуальных машин с WAF можно с помощью [портала управления Azure](https://portal.azure.com) добавить конечные точки HTTP и HTTPS, которые используются вашим приложением, как показано на рисунке ниже.

![Настройка конечной точки][ConfigureEndpoint]

Если в приложениях используются другие конечные точки, их также необходимо добавить в этот список.

### Настройка Barracuda WAF через портал управления ###
Barracuda WAF использует TCP-порт 8000 для настройки с помощью портала управления. Так как имеется несколько экземпляров ВМ WAF, необходимо повторить приведенные здесь шаги для каждого экземпляра ВМ.


> Примечание. После завершения настройки WAF удалите конечную точку TCP/8000 из всех ВМ WAF для безопасности вашего WAF.

Добавьте конечную точку управления, как показано на рисунке ниже, для настройки вашего Barracuda WAF.

![Добавление конечной точки управления][AddManagementEndpoint]
 
С помощью браузера перейдите к конечной точке управления в своей облачной службе. Если имя вашей облачной службы test.cloudapp.net, доступ к этой конечной точке можно получить по адресу http://test.cloudapp.net:8000. Отобразится страница входа (как на рисунке ниже), через которую можно войти с помощью учетных данных, которые вы указали на этапе установки WAF ВМ.

![Страница управления входом][ManagementLoginPage]

После входа вы увидите панель мониторинга (как на рисунке ниже) с базовой статистикой о защите WAF.

![Панель управления][ManagementDashboard]

На вкладке «Службы» можно настроить WAF для защиты служб. Дополнительные сведения о настройке WAF Barracuda можно найти в соответствующей [документации](https://techlib.barracuda.com/waf/getstarted1). В примере ниже приведена настройка веб-приложения Azure, обслуживающего трафик по протоколам HTTP и HTTPS.

![Управление: добавление служб][ManagementAddServices]

> Примечание. В зависимости от настроек ваших приложений и функций, используемых в среде службы приложений, будет необходимо перенаправлять трафик для TCP портов, отличных от 80 и 443, например при наличии настройки IP SSL для веб-приложения. Список сетевых портов, которые используются в средах службы приложений, см. в разделе «Сетевые порты» [документации по управлению входящим трафиком](app-service-app-service-environment-control-inbound-traffic.md).

## Настройка диспетчера трафика Microsoft Azure (необязательно) ##
Если приложение доступно в нескольких регионах, необходимо сбалансировать нагрузку с помощью [диспетчера трафика Azure](traffic-manager.md). Для этого можно добавить конечную точку на [портале управления Azure](https://manage.azure.com) с помощью имени облачной службы для вашего WAF в профиле диспетчера трафика, как показано на рисунке ниже.

![Конечная точка диспетчера трафика][TrafficManagerEndpoint]

Если приложение требует проверки подлинности, убедитесь, что у вас есть ресурсы для проверки доступности приложения, которые не требуют проверки подлинности через диспетчер трафика. URL-адрес можно настроить в разделе «Настройка» [портала управления Azure](https://manage.azure.com), как показано ниже.

![Настройка диспетчера трафика][ConfigureTrafficManager]

Для перенаправления проверочных запросов диспетчера трафика из WAF в приложение необходимо установить Website Translations на Barracuda WAF для перенаправления трафика к приложению, как показано в следующем примере.

![Переводы веб-сайта][WebsiteTranslations]

## Защита трафика в среде службы приложений с помощью групп ресурсов сети##
Подробные сведения об ограничении трафика в среду службы приложений только от WAF с помощью VIP-адреса облачной службы см. в [документации по управлению входящим трафиком](app-service-app-service-environment-control-inbound-traffic.md). Ниже приведен пример команды Powershell для выполнения этой задачи для TCP-порта 80.


    Get-AzureNetworkSecurityGroup -Name "RestrictWestUSAppAccess" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP Barracuda" -Type Inbound -Priority 201 -Action Allow -SourceAddressPrefix '191.0.0.1'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP

Замените SourceAddressPrefix на виртуальный IP-адрес (VIP) облачной службы WAF.

> Примечание. VIP облачной службы изменится, если удалить и повторно создать облачную службу. Не забудьте после этого обновить IP-адрес в группе ресурсов сети.
 
<!-- IMAGES -->
[Architecture]: ./media/app-service-app-service-environment-web-application-firewall/Architecture.png
[ConfigureEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/ConfigureEndpoint.png
[AddManagementEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/AddManagementEndpoint.png
[ManagementAddServices]: ./media/app-service-app-service-environment-web-application-firewall/ManagementAddServices.png
[ManagementDashboard]: ./media/app-service-app-service-environment-web-application-firewall/ManagementDashboard.png
[ManagementLoginPage]: ./media/app-service-app-service-environment-web-application-firewall/ManagementLoginPage.png
[TrafficManagerEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/TrafficManagerEndpoint.png
[ConfigureTrafficManager]: ./media/app-service-app-service-environment-web-application-firewall/ConfigureTrafficManager.png
[WebsiteTranslations]: ./media/app-service-app-service-environment-web-application-firewall/WebsiteTranslations.png

<!---HONumber=July15_HO3-->