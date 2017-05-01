---
title: "Требования Azure AD и Active Directory для Azure RemoteApp | Документация Майкрософт"
description: "Узнайте, как настроить Active Directory для работы с Azure RemoteApp."
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: 66366b25-6012-45fa-a4f6-da0ddfe0b486
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 232901ab919c63ea70e52afb845240b41a517c51
ms.lasthandoff: 03/31/2017


---
# <a name="azure-ad--active-directory-requirements-for-azure-remoteapp"></a>Требования Azure AD + Active Directory для Azure RemoteApp
> [!IMPORTANT]
> Мы выводим службу Azure RemoteApp из эксплуатации 31 августа 2017 года. Дополнительные сведения см. в [объявлении](https://go.microsoft.com/fwlink/?linkid=821148).
> 
> 

Для гибридной коллекции Azure RemoteApp или для облачной коллекции, которую необходимо включить в федерацию, используя AD Connect, вам потребуется выполнить следующие действия.

### <a name="connect-azure-ad-and-active-directory"></a>Подключите Azure AD и Active Directory
Для подключения к клиенту Azure AD и вашей локальной среде Active Directory используйте AD Connect. Подключение двух каталогов можно выполнить всего за [4 щелчка](https://blogs.technet.microsoft.com/enterprisemobility/2014/08/04/connecting-ad-and-azure-ad-only-4-clicks-with-azure-ad-connect/) .

Примечание. Для гибридных коллекций требуется синхронизация службы каталогов.

### <a name="make-sure-your-domaincom-match"></a>Проверка соответствия @domain.com
Перед началом работы убедитесь, что имя участника-пользователя для локального леса соответствует суффиксу домена Azure AD. 

После настройки доменного суффикса имени участника-пользователя в Azure AD все пользователи, выполняющие вход в Azure RemoteApp, будут входить в систему как user@<the suffix you set up>. Убедитесь, что пользователи также могут входить с помощью аналогичного имени user@suffix в локальный домен. В некоторых случаях можно настроить одно доменное имя в Azure AD несмотря на то, что локально для пользователя указан другой суффикс домена. В этом случае пользователи не будет возможность подключения к компьютерам, подключенным к домену, или ресурсам через Azure RemoteApp.

Например, если вы настроили доменный суффикс своего имени участника-пользователя в AAD как contoso.com, однако некоторые пользователи из локальной сети или домена приложения настроены для входа в систему с помощью @contoso.uk, то они не смогут корректно войти в коллекцию ARA. Для выполнения входа в систему имена участников-пользователей в AAD и AD должны совпадать.

### <a name="create-objects-for-azure-remoteapp"></a>Создайте объекты для Azure RemoteApp
Кроме того, необходимо создать следующие локальные объекты Active Directory:

* Учетная запись службы для предоставления доступа к доменным ресурсам программ RemoteApp путем соединения конечных точек RDSH с локальным доменом.
* Подразделение для хранения объектов машин RemoteApp. Использовать подразделение рекомендуется (но не требуется) для изолирования учетных записей и политик, которые будут использоваться с RemoteApp.

При создании коллекции RemoteApp требуются оба этих объекта, поэтому сначала выполните указанные действия.


