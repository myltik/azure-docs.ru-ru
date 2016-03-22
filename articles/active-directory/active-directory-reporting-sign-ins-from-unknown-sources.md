<properties
	pageTitle="Операции входа из неизвестных источников"
	description="Отчет, в котором указываются пользователи, успешно выполнившие вход в каталог, используя анонимный IP-адрес прокси-сервера."
	services="active-directory"
	documentationCenter=""
	authors="SSalahAhmed"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/04/2016"
	ms.author="saah;kenhoff"/>

# Операции входа из неизвестных источников
В этом отчете указываются пользователи, которые успешно выполнили вход в ваш каталог, при этом им назначен IP-адрес клиента, который распознается корпорацией Майкрософт как IP-адрес анонимного прокси-сервера (например, IP-адрес Tor). Пользователи часто используют эти прокси-серверы, чтобы скрыть IP-адреса своих компьютеров. Иногда их используют злоумышленники.

В результатах этого отчета отображается количество успешных операций входа пользователя в ваш каталог со своего адреса и с IP-адреса прокси-сервера.


![Операции входа из неизвестных источников](./media/active-directory-reporting-sign-ins-from-unknown-sources/signInsFromUnknownSources.PNG)

<!---HONumber=AcomDC_0309_2016-->