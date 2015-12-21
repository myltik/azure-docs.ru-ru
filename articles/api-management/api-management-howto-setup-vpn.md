<properties
	pageTitle="Настройка VPN-подключений в службе управления API Azure"
	description="Узнайте, как настроить VPN-подключение в службе управления API Azure и обращаться к веб-службам через него."
	services="api-management"
	documentationCenter=""
	authors="antonba"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="api-management"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/03/2015"
	ms.author="antonba"/>

# Настройка VPN-подключений в службе управления API Azure

Поддержка VPN службой управления API позволяет подключить шлюз службы управления API к виртуальной сети Azure. Это позволяет клиентам службы управления API безопасно подключаться к локальным серверным веб-службам, в противном случае они были бы недоступны для доступа через Интернет.

## <a name="enable-vpn"> </a>Включение VPN-подключений

>VPN-подключения доступны только на уровне **Premium**. Чтобы перейти к нему, откройте службу управления API на [классическом портале Azure][] и перейдите на вкладку **Масштаб**. В разделе **Общие** выберите уровень Premium и нажмите кнопку "Сохранить".

Чтобы включить VPN-подключения, откройте службу управления API на [классическом портале Azure][] и перейдите на вкладку **Настройка**.

В разделе VPN установите параметр **VPN-подключение** в значение **Вкл.**.

![Вкладка настройки экземпляра службы управления API][api-management-setup-vpn-configure]

Будет показан список всех регионов, где предоставляется служба управления API.

Выберите VPN и подсеть для каждого региона. Список сетей VPN заполняется на основе виртуальных сетей, имеющихся в подписке Azure и настроенных в регионе, конфигурирование которого выполняется.

![Выбор VPN][api-management-setup-vpn-select]

В нижней части экрана нажмите кнопку **Сохранить**. Во время обновления на классическом портале Azure невозможно выполнять другие операции в службе управления API. Шлюз службы будет оставаться доступным, и вызовы среды выполнения не будут затронуты.

Обратите внимание, что виртуальный IP-адрес шлюза будет меняться при каждом включении или отключении VPN.

## <a name="connect-vpn"> </a>Подключение к веб-службе за VPN

После подключения службы управления API к VPN обращение к веб-службам в виртуальной сети ничем не будет отличаться от обращения к общедоступным службам. Просто введите локальный адрес или имя узла (если для виртуальной сети Azure был настроен DNS-сервер) веб-службы в поле **URL-адрес веб-службы** при создании нового API или редактировании существующего.

![Добавление интерфейса API из VPN][api-management-setup-vpn-add-api]


## <a name="related-content"> </a>Связанная информация


* [Руководство по созданию виртуальной сети для подключений между организациями по типу "сеть-сеть"][]
* [Как использовать инспектор API для трассировки вызовов в службе управления API Azure][]

[api-management-setup-vpn-configure]: ./media/api-management-howto-setup-vpn/api-management-setup-vpn-configure.png
[api-management-setup-vpn-select]: ./media/api-management-howto-setup-vpn/api-management-setup-vpn-select.png
[api-management-setup-vpn-add-api]: ./media/api-management-howto-setup-vpn/api-management-setup-vpn-add-api.png

[Enable VPN connections]: #enable-vpn
[Connect to a web service behind VPN]: #connect-vpn
[Related content]: #related-content

[классическом портале Azure]: https://manage.windowsazure.com/

[Руководство по созданию виртуальной сети для подключений между организациями по типу "сеть-сеть"]: ../virtual-networks-create-site-to-site-cross-premises-connectivity
[Как использовать инспектор API для трассировки вызовов в службе управления API Azure]: api-management-howto-api-inspector.md

<!---HONumber=AcomDC_1210_2015-->