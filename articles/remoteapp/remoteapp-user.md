---
title: "Добавление пользователя в коллекцию Azure RemoteApp | Документация Майкрософт"
description: "Узнайте о том, как добавить пользователя в коллекцию Azure RemoteApp"
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: 6b751fd2-2b11-499f-a2eb-12cb47f3129b
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 3dbeba840501f395c9bad07109fe8ea47947a5fa


---
# <a name="how-to-add-a-user-to-your-azure-remoteapp-collection"></a>Добавление пользователя в коллекцию Azure RemoteApp
> [!IMPORTANT]
> Мы выводим удаленное приложение Azure RemoteApp из эксплуатации. Дополнительные сведения см. в [объявлении](https://go.microsoft.com/fwlink/?linkid=821148).
> 
> 

Прежде чем пользователи смогут видеть и использовать ваши приложения в Azure RemoteApp, необходимо предоставить им доступ к вашей коллекции. Это просто: на вкладке **Доступ пользователя** введите данные учетной записи пользователя, а затем установите флажок.

Какие данные учетной записи требуются? Это зависит от типа созданной коллекции (облачной или гибридной) и того, используется ли Office 365 профессиональный плюс в этой коллекции.

## <a name="supported-user-identities"></a>Поддерживаемые удостоверения пользователей
Разные типы коллекций (облачные или гибридные) поддерживают использование разных удостоверений пользователей для доступа к приложениям.  

Для гибридной коллекции RemoteApp необходимо настроить локальную инфраструктуру доменов Active Directory и клиент Azure Active Directory с интеграцией каталогов (при желании можно настроить и единый вход). Кроме того, в локальном каталоге необходимо создать несколько объектов Active Directory.  

Что касается облачной коллекции RemoteApp, любому пользователю с поддержкой удостоверений Azure Active Directory можно предоставить доступ к RemoteApp, чтобы включить учетные записи Майкрософт.  См. таблицу ниже.

Пользователи Office 365 являются пользователями Azure Active Directory. При наличии у них гибридных, синхронизированных с каталогами учетных записей Azure Active Directory этим пользователям можно предоставить доступ путем гибридного развертывания RemoteApp.   

С помощью этой таблицы можно быстро проверить, какие удостоверения поддерживаются в вашей коллекции, а также в чем состоят требования к Active Directory.

| Учетные записи пользователей | Облако | Гибридная среда |
| --- | --- | --- |
| Учетная запись Майкрософт |Да |Нет |
| Azure Active Directory (Azure AD) | | |
| Только облако Azure AD |Да |Нет |
| ADsync с синхронизацией паролей |Да |Да |
| ADsync без синхронизации паролей |Да |Нет |
| ADsync с AD FS |Да |Да |
| [Сторонние поставщики удостоверений, поддерживаемые Azure](https://msdn.microsoft.com/library/azure/jj679342.aspx) (например, Ping) |Да |Да |
| Multi-Factor Authentication |Да |Да |

[Узнайте больше](remoteapp-ad.md) о настройке Active Directory для RemoteApp.

> [!NOTE]
> Пользователи Azure Active Directory должны относиться к клиенту, связанному с вашей подпиской. (Вы можете просмотреть и изменить связанную подписку на вкладке **Параметры** на портале. Дополнительные сведения см. в статье [Смена клиента Azure Active Directory в Azure RemoteApp](remoteapp-changetenant.md).)
> 
> 

## <a name="office-365-proplus-user-account-information"></a>Данные учетных записей пользователей для Office 365 профессиональный плюс
При работе с образом шаблона Office 365 профессиональный плюс *или* пользовательским образом шаблона, использующим Office 365, разрешается добавлять только тех пользователей Azure Active Directory, у которых есть подписки Office 365 для домена организации по умолчанию. Дополнительные сведения см. в статье [Использование Office с Azure RemoteApp](remoteapp-o365.md).




<!--HONumber=Nov16_HO3-->


