---
title: "Доменные службы Azure Active Directory: включение поддержки службы профилей пользователей SharePoint | Документация Майкрософт"
description: "Настройте управляемые домены доменных служб Azure Active Directory для поддержки синхронизации профилей для сервера SharePoint"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/28/2017
ms.author: maheshu
translationtype: Human Translation
ms.sourcegitcommit: 2ef700f9a27d27997d53143260603bda822aa104
ms.openlocfilehash: b84853da989d18414a31cf5b178c6506abc56bbe


---

# <a name="configure-a-managed-domain-to-support-profile-synchronization-for-sharepoint-server"></a>Настройка управляемого домена для поддержки синхронизации профилей для сервера SharePoint
Сервер SharePoint содержит службу профилей пользователей, которая используется для синхронизации профилей пользователей. Для настройки службы профилей пользователей необходимо иметь соответствующее разрешение домена Active Directory. Дополнительные сведения см. в статье [Предоставление доменным службам Active Directory разрешений для синхронизации профилей в SharePoint Server 2013](https://technet.microsoft.com/library/hh296982.aspx).

В этой статье описывается настройка управляемых доменов доменных служб Azure AD для развертывания службы синхронизации профилей пользователей для сервера SharePoint.

## <a name="the-aad-dc-service-accounts-group"></a>Группа "Учетные записи службы контроллера домена AAD"
Группа безопасности **Учетные записи службы контроллера домена AAD** доступна в подразделении "Пользователи" на управляемом домене. Эту группу можно найти в оснастке MMC **Пользователи и компьютеры Active Directory** на управляемом домене.

![Группа безопасности "Учетные записи службы контроллера домена AAD"](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts.png)

Участникам этой группы безопасности делегируются следующие привилегии доступа:
- Репликация изменений каталога в атрибуте Root DSE управляемого домена.
- Репликация изменений каталога в контексте именования конфигураций (cn = контейнер конфигурации) управляемого домена.

Эта группа безопасности также входит во встроенную группу **Пред-Windows 2000 доступ**.

![Группа безопасности "Учетные записи службы контроллера домена AAD"](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts-properties.png)


## <a name="enable-your-managed-domain-to-support-sharepoint-server-user-profile-sync"></a>Включение управляемого домена для поддержки синхронизации профилей пользователей SharePoint Server
Учетную запись службы, используемую для синхронизации профилей пользователей SharePoint, можно добавить в группу **Учетные записи службы контроллера домена AAD**. В результате синхронизированная учетная запись получает достаточные привилегии доступа для репликации изменений в каталог. На этом этапе настраивается правильная работа синхронизации профилей пользователей сервера SharePoint.

![Добавление участников в группу "Учетные записи службы контроллера домена AAD"](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts-add-member.png)

![Добавление участников в группу "Учетные записи службы контроллера домена AAD"](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts-add-member2.png)

## <a name="related-content"></a>Похожий контент
* [Предоставление доменным службам Active Directory разрешений для синхронизации профилей в SharePoint Server 2013](https://technet.microsoft.com/library/hh296982.aspx)



<!--HONumber=Jan17_HO5-->


