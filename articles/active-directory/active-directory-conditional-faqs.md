<properties
	pageTitle="Часто задаваемые вопросы об условном доступе | Microsoft Azure"
	description="Часто задаваемые вопросы об условном доступе"
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/10/2016"
	ms.author="femila"/>

# Часто задаваемые вопросы об условном доступе

**Можно ли применять условия устройства в Azure AD, если используется управление мобильными устройствами для Office 365?**
 
При использовании MDM для Office 365 все политики устройств сохраняются в Office 365. Во избежание конфликтов между политиками, настроенными в Office 365 и в Azure AD, в настоящее время установка этих политик в Azure AD запрещена, если используется MDM для Office 365.

<!---HONumber=AcomDC_0218_2016-->