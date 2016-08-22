<properties
	pageTitle="Изменение хэш-алгоритма подписи для отношения доверия с проверяющей стороной Office 365 | Microsoft Azure"
	description="Эта страница содержит рекомендации по изменению алгоритма SHA для доверия федерации с Office 365."
    keywords="SHA1,SHA256,O365,федерация,aadconnect,adfs,ad fs,изменение sha,доверие федерации,отношение доверия с проверяющей стороной"
	services="active-directory"
	documentationCenter=""
	authors="anandyadavmsft"
	manager="samueld"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/01/2016"
	ms.author="anandy"/>

#Изменение хэш-алгоритма подписи для отношения доверия с проверяющей стороной Office 365

##Обзор

Службы AD FS подписывают свои маркеры для Azure Active Directory, чтобы защитить их от незаконного изменения. Подпись основывается на алгоритме SHA1 или SHA256. Теперь Microsoft Azure Active Directory поддерживает маркеры, подписанные с помощью алгоритма SHA256, а также рекомендует выбрать этот алгоритм подписи маркера для обеспечения максимальной защиты. В этой статье описывается процедура замены алгоритма подписи маркера на алгоритм с более высоким уровнем защиты — SHA256.

##Изменение алгоритма подписи маркера

После того, как вы выберете алгоритм подписи, следуя указанным ниже инструкциям, AD FS начнет подписывать маркеры для отношения доверия с проверяющей стороной Office 365 с помощью алгоритма SHA-256. При этом не требуется вносить какие-либо дополнительные изменения в настройки. И данное изменение никак не скажется на процедуре доступа конечных пользователей к Office 365 или другим приложениям Azure AD.

###Использование консоли управления

* Откройте консоль управления AD FS на сервере-источнике AD FS.
* Разверните узел AD FS и выберите "Relying party trusts" (Отношения доверия с проверяющей стороной).
* Щелкните правой кнопкой мыши свой объект отношений доверия с проверяющей стороной Office 365 или Azure и выберите пункт "Properties" (Свойства).
* Перейдите на вкладку "Advanced" (Дополнительно) и в поле "Secure hash algorithm" (Защищенный хэш-алгоритм) выберите значение SHA256.
* Нажмите кнопку "ОК".

![Алгоритм подписи SHA256 — MMC](./media/active-directory-aadconnectfed-sha256guidance/mmc.png)

###Использование командлетов AD FS PowerShell

* На любом сервере с AD FS откройте PowerShell с правами администратора.
* Задайте защищенный хэш-алгоритм с помощью командлета Set-AdfsRelyingPartyTrust.

 <code>Set-AdfsRelyingPartyTrust -TargetName 'Microsoft Office 365 Identity Platform' -SignatureAlgorithm 'http://www.w3.org/2001/04/xmldsig-more#rsa-sha256'</code>

##Также ознакомьтесь:

* [Управление службами федерации Active Directory и их настройка с помощью Azure AD Connect](./active-directory-aadconnect-federation-management.md#repairing-the-trust)

<!---HONumber=AcomDC_0810_2016-->