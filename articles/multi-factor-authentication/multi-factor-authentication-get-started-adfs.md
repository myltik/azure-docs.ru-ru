---
title: "Azure MFA: двухфакторная проверка подлинности и AD FS | Документация Майкрософт"
description: "Эта страница содержит сведения о службе Azure Multi-Factor Authentication, описывающие начало работы с Azure MFA и AD FS."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 44fbba68-6cf9-46c1-a9df-736580b68ae3
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/23/2017
ms.author: kgremban
ms.translationtype: Human Translation
ms.sourcegitcommit: 54b5b8d0040dc30651a98b3f0d02f5374bf2f873
ms.openlocfilehash: 28aede545c738137ff04257214e4a3f42792d85c
ms.contentlocale: ru-ru
ms.lasthandoff: 04/28/2017


---
# <a name="getting-started-with-azure-multi-factor-authentication-and-active-directory-federation-services"></a>Приступая к работе со службой Azure Multi-Factor Authentication и службами федерации Active Directory
<center>![Облако](./media/multi-factor-authentication-get-started-adfs/adfs.png)</center>

Если в организации создана федерация локальной службы Active Directory со службой Azure Active Directory с помощью служб федерации Active Directory, доступны два варианта использования Многофакторной идентификации Azure.

* Защита облачных ресурсов с помощью службы Azure Multi-Factor Authentication и служб федерации Active Directory
* Защита облачных и локальных ресурсов с помощью сервера Azure Multi-Factor Authentication

В следующей таблице перечислены возможности проверки для защиты ресурсов с помощью Многофакторной идентификации Azure и служб федерации Active Directory.

| Проверка для браузерных приложений | Проверка для небраузерных приложений |
|:--- |:--- |:--- |
| Защита ресурсов Azure AD с помощью Azure Multi-Factor Authentication |<li>Первый этап проверки выполняется локально с помощью служб федерации Active Directory.</li> <li>Второй этап выполняется с использованием телефона для облачной проверки подлинности.</li> |
| Защита ресурсов Azure AD с помощью служб федерации Active Directory |<li>Первый этап проверки выполняется локально с помощью служб федерации Active Directory.</li><li>Второй этап выполняется локально путем обработки утверждения.</li> |

Разъяснения касательно паролей приложений для федеративных пользователей.

* Пароли приложений проверяются с использованием облачной проверки подлинности и, следовательно, обходят федерацию. Федерация активно используется только при настройке пароля приложения.
* Параметры контроля доступа локальных клиентов не учитываются при использовании паролей приложений.
* У вас нет возможности вести журнал локальной проверки подлинности для паролей приложений.
* Отключение или удаление учетной записи может занять до 3 часов с учетом синхронизации каталогов, которая задерживает процесс отключения или удаления паролей приложений в облачном удостоверении.

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о настройке службы или сервера Многофакторной идентификации Azure с помощью служб федерации Active Directory см. в таких статьях:

* [Защита облачных ресурсов с помощью службы Azure Multi-Factor Authentication и служб AD FS](multi-factor-authentication-get-started-adfs-cloud.md)
* [Защита облачных и локальных ресурсов с помощью сервера Azure Multi-Factor Authentication и сервера Windows Server 2012 R2 AD FS](multi-factor-authentication-get-started-adfs-w2k12.md)
* [Защита облачных и локальных ресурсов с помощью сервера Azure Multi-Factor Authentication и AD FS 2.0](multi-factor-authentication-get-started-adfs-adfs2.md)

