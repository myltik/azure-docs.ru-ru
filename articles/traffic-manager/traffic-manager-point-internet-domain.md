<properties
   pageTitle="Направление интернет-домена компании на домен диспетчера трафика"
   description="В этой статье вы узнаете, как направить доменное имя вашей компании на доменное имя диспетчера трафика."
   services="traffic-manager"
   documentationCenter=""
   authors="cherylmc"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/27/2015"
   ms.author="cherylmc" />

# Направление интернет-домена компании на домен диспетчера трафика

Чтобы направить имя домена вашей компании на доменное имя диспетчера трафика, замените запись ресурса DNS на интернет-сервере DNS на тип записи CNAME, в результате чего доменное имя компании будет сопоставляться с доменным именем профиля диспетчера трафика. Доменное имя диспетчера трафика указывается в разделе **Общие** на странице конфигурации в профиле диспетчера трафика.

Например, чтобы направить доменное имя компании **www.contoso.com** на доменное имя диспетчера трафика **contoso.trafficmanager.net**, обновите запись ресурса DNS на следующую:

    www.contoso.com IN CNAME contoso.trafficmanager.net 

Все запросы на трафик на *www.contoso.com* будут направляться на *contoso.trafficmanager.net*.

>[AZURE.IMPORTANT]Вы не можете направлять домен второго уровня, такой как *contoso.com*, на домен диспетчера трафика. Это невозможно из-за того, что, согласно ограничению протокола DNS, нельзя использовать записи CNAME для доменных имен второго уровня.

## См. также

[О методах балансировки нагрузки диспетчера трафика](traffic-manager-load-balancing-methods.md)

[Задачи настройки диспетчера трафика](https://msdn.microsoft.com/library/azure/hh744830.aspx)

[Обзор диспетчера трафика](traffic-manager-overview.md)

<!--HONumber=49--> 