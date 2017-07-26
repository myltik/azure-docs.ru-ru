---

title: "Миграция из Azure RemoteApp в Citrix XenApp Essentials | Документы Майкрософт"
description: "Как выполнить миграцию из Azure RemoteApp в Citrix XenApp Essentials"
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: 695a8165-3454-4855-8e21-f2eb2c61201b
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: mbaldwin
ms.translationtype: Human Translation
ms.sourcegitcommit: 07584294e4ae592a026c0d5890686eaf0b99431f
ms.openlocfilehash: fcd96a466d1c0dad17d7012308281ef868463b19
ms.contentlocale: ru-ru
ms.lasthandoff: 06/01/2017


---

# <a name="migrate-from-azure-remoteapp-to-citrix-xenapp-essentials"></a>Миграция из Azure RemoteApp в Citrix XenApp Essentials

Если вы используете Azure RemoteApp и хотите перейти на Citrix XenApp Essentials, есть несколько требований, которые следует учитывать. Во-первых, прочитайте [пошаговое техническое руководство по развертыванию для Citrix XenApp Essentials](https://docs.citrix.com/content/dam/docs/en-us/citrix-cloud/downloads/xenapp-essentials-deployment-guide.pdf) и ознакомьтесь с [интернет-библиотекой технической документации](http://docs.citrix.com/en-us/citrix-cloud/xenapp-and-xendesktop-service/xenapp-essentials.html) компании Citrix. 

## <a name="prerequisite-steps-for-migration"></a>Обязательные шаги для миграции

1. Создайте новую виртуальную сеть или определите, в какой виртуальной сети Azure в Azure Resource Manager вы развернете Citrix XenApp Essentials. Azure RemoteApp использует классический портал Azure; Citrix XenApp Essentials поддерживает только Azure Resource Manager.  
2. Убедитесь в наличии сетевого доступа к контроллеру домена из выбранной виртуальной сети, так как Citrix поддерживает только гибридные развертывания. При использовании облачного развертывания Azure RemoteApp убедитесь в наличии сетевого доступа к контроллеру домена Active Directory в виртуальной сети. Можно также использовать доменные службы Azure Active Directory (Azure AD DS). 
3. Убедитесь, что система DNS виртуальной сети настроена правильно, чтобы первая попытка присоединения к домену прошла успешно. Вы можете создать виртуальную машину в выбранной виртуальной сети и выполнить присоединение к домену вручную, чтобы гарантировать, что система DNS и присоединение к домену работает должным образом. Это гарантирует успешную первую попытку развертывания Citrix XenApp Essentials. 
4. При необходимости создайте пиринг между виртуальной сетью классического портала Azure, которую вы используете с Azure RemoteApp, и виртуальной сетью Azure Resource Manager. Этот пиринг сработает, если обе сети находятся в одном регионе. Если это не так, используйте межсайтовый VPN, чтобы связать виртуальные сети. 
5. При необходимости прочтите статью [Как перенести данные в службу Azure RemoteApp и из нее](remoteapp-migrate.md). 
6. Обновите существующий образ Azure RemoteApp, включив в него компонент Citrix VDA (инструкции см. в документации Citrix). 
7. Перейдите в Azure Marketplace и начните развертывание Citrix XenApp Essentials.

## <a name="other-considerations"></a>Дополнительные рекомендации

При миграции следует учитывать дополнительные сведения.
- Служба Citrix XenApp Essentials поддерживает только гибридные развертывания. Иными словами, для присоединения к домену ей требуется сетевой доступ к контроллеру домена. Если вы используете облачное развертывание Azure RemoteApp, используйте для присоединения к домену Azure AD DS или убедитесь, что в виртуальной сети есть доступ к Active Directory. 
- Чтобы узнать, как переместить данные пользователей в Citrix XenApp Essentials, см. статью [Перенос данных в Azure RemoteApp и обратно](remoteapp-migrate.md). 
- Служба Citrix XenApp Essentials поддерживает только учетные записи Active Directory. Она не поддерживает учетные записи Майкрософт (например, outlook.com, msn.com или hotmail.com). 

## <a name="citrix-xenapp-essentials-billing"></a>Выставление счетов за Citrix XenApp Essentials

Полные сведения о ценах см. в разделах [Часто задаваемые вопросы](https://www.citrix.com/global-partners/microsoft/resources/xenapp-essentials-faq.html#tab-30699) и [Обзор Citrix](https://www.citrix.com/global-partners/microsoft/remote-app.html). Есть три компонента выставления счетов для Citrix XenApp Essentials:

- Плата за использование службы Citrix, составляющая 12 долл. США на пользователя в месяц. Как и все покупки в Azure Marketplace, эта плата взимается при помощи способа оплаты, связанного с вашей подпиской Azure. Денежные кредиты Azure невозможно использовать для клиентов Enterprise Agreement (EA). 
- Клиентские лицензии (CAL) служб удаленных данных (RDS). Сейчас вы можете оплатить сбор за удаленный доступ, который входит в состав платы за Citrix XenApp Essentials, в размере 6,25 долл. США. Если вы являетесь клиентом EA, для этого можно использовать денежные кредиты Azure. Если вы хотите использовать существующие клиентские лицензии RDS, обратитесь к нам по адресу [ arainfo@microsoft.com ](mailto:arainfo@microsoft.com), чтобы мы могли применить их для вашего счета. 
- Служба вычислений и служба хранилища Azure. Это затраты на хранилище Azure и применяемые ресурсы вычислений для используемых виртуальных машин. Учитывайте цены при выборе размера виртуальных машин и плотности пользователей. Если вы являетесь клиентом EA, для этого можно использовать денежные кредиты Azure.

Если у вас остались вопросы:
- Напишите нам по адресу [arainfo@microsoft.com](mailto:arainfo@microsoft.com).
- Обратитесь в [службу поддержки Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Начните с [обращения в службу поддержки Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), чтобы вам помогли с обязательными шагами 1–5. По вопросам шагов 6–7 обратитесь в Citrix, открыв запрос в службу поддержки на портале управления Citrix. 

