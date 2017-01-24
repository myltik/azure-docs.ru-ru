---
title: "Настройка VPN-подключений в службе управления API Azure"
description: "Узнайте, как настроить VPN-подключение в службе управления API Azure и обращаться к веб-службам через него."
services: api-management
documentationcenter: 
author: antonba
manager: erikre
editor: 
ms.assetid: 55a2a1e1-d07e-4111-9ce3-8837ed5040d6
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: antonba
translationtype: Human Translation
ms.sourcegitcommit: 30ec6f45da114b6c7bc081f8a2df46f037de61fd
ms.openlocfilehash: 98e88b1c69de8732f1a4ae54c8ecd0b11a547f35


---
# <a name="how-to-setup-vpn-connections-in-azure-api-management"></a>Настройка VPN-подключений в службе управления API Azure
Поддержка VPN службой управления API позволяет подключить шлюз службы управления API к виртуальной сети Azure (классической). Это позволяет клиентам службы управления API безопасно подключаться к локальным серверным веб-службам, в противном случае они были бы недоступны для доступа через Интернет.

> [!NOTE]
> Управление API Azure работает с классическими виртуальными сетями. Дополнительные сведения о создании классической виртуальной сети см. в статье [Создание (классической) виртуальной сети с помощью портала Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). Сведения о подключении классических виртуальных сетей к виртуальным сетям ARM см. в статье [Подключение классических виртуальных сетей к новым виртуальным сетям](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).
> 
> 

## <a name="enable-vpn"> </a>Включение VPN-подключений
> VPN-подключения доступны только на уровнях **Premium** и **Developer**. Чтобы перейти к нему, откройте службу управления API на [классическом портале Azure][Azure Classic Portal] и перейдите на вкладку **Масштаб**. В разделе **Общие** выберите уровень Premium и нажмите кнопку "Сохранить".
> 
> 

Чтобы включить VPN-подключения, откройте службу управления API на [классическом портале Azure][Azure Classic Portal] и перейдите на вкладку **Настройка**. 

В разделе VPN укажите для параметра **VPN-подключение** значение **Вкл.**

![Вкладка настройки экземпляра службы управления API][api-management-setup-vpn-configure]

Будет показан список всех регионов, где предоставляется служба управления API.

Выберите VPN и подсеть для каждого региона. Список сетей VPN заполняется на основе виртуальных сетей, имеющихся в подписке Azure и настроенных в регионе, конфигурирование которого выполняется.

![Выбор VPN][api-management-setup-vpn-select]

В нижней части экрана нажмите кнопку **Сохранить** . Во время обновления на классическом портале Azure невозможно выполнять другие операции в службе управления API. Шлюз службы будет оставаться доступным, и вызовы среды выполнения не будут затронуты.

Обратите внимание, что виртуальный IP-адрес шлюза будет меняться при каждом включении или отключении VPN.

## <a name="connect-vpn"> </a>Подключение к веб-службе за VPN
После подключения службы управления API к VPN обращение к веб-службам в виртуальной сети ничем не будет отличаться от обращения к общедоступным службам. Просто введите локальный адрес или имя узла (если для виртуальной сети Azure был настроен DNS-сервер) веб-службы в поле **URL-адрес веб-службы** при создании нового API или редактировании существующего.

![Добавление интерфейса API из VPN][api-management-setup-vpn-add-api]

## <a name="required-ports-for-api-management-vpn-support"></a>Порты, необходимые для поддержки VPN управления API
При размещении экземпляра службы управления API в виртуальной сети используются порты, указанные в следующей таблице. Если эти порты заблокированы, служба может работать неправильно. Блокировка одного или нескольких из этих портов является самой распространенной проблемой неправильной настройки при использовании управления API в виртуальной сети.

| Порты | Направление | Транспортный протокол | Назначение | Ресурс и назначение |
| --- | --- | --- | --- | --- |
| 80, 443 |Входящий трафик |TCP |Подключения клиентов к службе управления API |INTERNET — VIRTUAL_NETWORK |
| 80, 443 |Исходящие |TCP |Зависимость управления API от службы хранилища Azure и служебной шины Azure |VIRTUAL_NETWORK — INTERNET |
| 1433 |Исходящие |TCP |Зависимости управления API от SQL |VIRTUAL_NETWORK — INTERNET |
| 9350, 9351, 9352, 9353, 9354 |Исходящие |TCP |Зависимости управления API от служебной шины |VIRTUAL_NETWORK — INTERNET |
| 5671 |Исходящие |AMQP |Зависимость управления API для политики ведения журнала концентратора событий |VIRTUAL_NETWORK — INTERNET |
| 6381, 6382, 6383 |Входящий и исходящий трафик |UDP |Зависимости управления API от кэша Redis |VIRTUAL_NETWORK — VIRTUAL_NETWORK |
| 445 |Исходящие |TCP |Зависимость управления API от общей папки Azure для GIT |VIRTUAL_NETWORK — INTERNET |

## <a name="custom-dns"> </a>Настройка пользовательского DNS-сервера
Управление API зависит от нескольких служб Azure. Когда экземпляр службы управления API размещается в виртуальной сети, в которой используется пользовательский DNS-сервер, необходимо иметь возможность разрешения имен узлов этих служб Azure. Следуйте [этим](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) рекомендациям по настройке пользовательского DNS-сервера.  

## <a name="related-content"> </a>Связанная информация
* [Создание виртуальной сети с VPN-подключением типа "сеть — сеть" с помощью классического портала Azure][Create a virtual network with a site-to-site VPN connection using the Azure Classic Portal]
* [Как использовать инспектор API для трассировки вызовов в службе управления API Azure][How to use the API Inspector to trace calls in Azure API Management]

[api-management-setup-vpn-configure]: ./media/api-management-howto-setup-vpn/api-management-setup-vpn-configure.png
[api-management-setup-vpn-select]: ./media/api-management-howto-setup-vpn/api-management-setup-vpn-select.png
[api-management-setup-vpn-add-api]: ./media/api-management-howto-setup-vpn/api-management-setup-vpn-add-api.png

[Enable VPN connections]: #enable-vpn
[Connect to a web service behind VPN]: #connect-vpn
[Related content]: #related-content

[Azure Classic Portal]: https://manage.windowsazure.com/

[Create a virtual network with a site-to-site VPN connection using the Azure Classic Portal]: ../vpn-gateway/vpn-gateway-site-to-site-create.md
[How to use the API Inspector to trace calls in Azure API Management]: api-management-howto-api-inspector.md



<!--HONumber=Dec16_HO3-->


