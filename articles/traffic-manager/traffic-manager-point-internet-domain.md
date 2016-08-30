.<properties
   pageTitle="Направление интернет-домена компании на домен диспетчера трафика | Microsoft Azure"
   description="В этой статье вы узнаете, как направить доменное имя вашей компании на доменное имя диспетчера трафика."
   services="traffic-manager"
   documentationCenter=""
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/17/2016"
   ms.author="sewhee" />

# Указание домена Traffic Manager для интернет-домена компании

Чтобы направить имя домена вашей компании на доменное имя диспетчера трафика, замените запись ресурса DNS на интернет-сервере DNS на тип записи CNAME, в результате чего доменное имя компании будет сопоставляться с доменным именем профиля диспетчера трафика. Доменное имя диспетчера трафика указывается в разделе **Общие** на странице конфигурации в профиле диспетчера трафика.

Например, чтобы направить доменное имя компании www.contoso.com на доменное имя диспетчера трафика contoso.trafficmanager.net, обновите запись ресурса DNS на следующую:

    www.contoso.com IN CNAME contoso.trafficmanager.net

Весь трафик, направленный в *www.contoso.com*, теперь будет перенаправляться в *contoso.trafficmanager.net*.

>[AZURE.IMPORTANT] Нельзя направить домен второго уровня, например *contoso.com*, в домен диспетчера трафика. Это невозможно из-за того, что, согласно ограничению протокола DNS, нельзя использовать записи CNAME для доменных имен второго уровня.

## Дальнейшие действия

[Методы маршрутизации диспетчера трафика](traffic-manager-routing-methods.md)

[Диспетчер трафика — включение, отключение или удаление профиля диспетчера трафика](disable-enable-or-delete-a-profile.md)

[Диспетчер трафика — отключение и включение конечной точки диспетчера трафика](disable-or-enable-an-endpoint.md)

<!---HONumber=AcomDC_0824_2016-->