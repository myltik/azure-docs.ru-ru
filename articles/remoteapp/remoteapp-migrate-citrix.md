---

title: "Как выполнить миграцию из Azure RemoteApp в Citrix XenApp Essentials | Документация Майкрософт"
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
ms.sourcegitcommit: 8f987d079b8658d591994ce678f4a09239270181
ms.openlocfilehash: de47edc5ea4dc1b67bd69b74929acf249b0d43e6
ms.contentlocale: ru-ru
ms.lasthandoff: 05/25/2017


---

# <a name="how-to-migrate-from-azure-remoteapp-to-citrix-xenapp-essentials"></a>Как выполнить миграцию из Azure RemoteApp в Citrix XenApp Essentials

Для пользователей Azure RemoteApp, желающих перейти на Citrix XenApp Essentials, существует несколько предварительных требований.  Сначала рекомендуем прочитать [пошаговое техническое руководство по развертыванию для Citrix XenApp Essentials](https://docs.citrix.com/content/dam/docs/en-us/citrix-cloud/downloads/xenapp-essentials-deployment-guide.pdf), а также ознакомиться с [интерактивной технической библиотекой](http://docs.citrix.com/en-us/citrix-cloud/xenapp-and-xendesktop-service/xenapp-essentials.html) Citrix. 

К счастью, вы можете продолжать использовать большую часть инфраструктуры для Azure RemoteApp, в которую вы вложили средства, но есть несколько общих требований для развертывания XenApp Essentials.

## <a name="prerequisites"></a>Предварительные требования

1. Создайте новую виртуальную сеть или выберите в Azure Resource Manager виртуальную сеть Azure для развертывания Citrix XenApp Essentials. В Azure RemoteApp используется классическая модель Azure. Служба Citrix XenApp Essentials поддерживает только Azure Resource Manager.  
2. Убедитесь, что выбранная виртуальная сеть имеет сетевой доступ к контроллеру домена, так как в Citrix поддерживаются только гибридные развертывания. При использовании облачного развертывания Azure RemoteApp необходимо обеспечить сетевой доступ виртуальной сети к контроллеру домена Active Directory. Мы рекомендуем использовать доменные службы Azure Active Directory (AAD DS). 
3. Чтобы успешно выполнить присоединение к домену с первой попытки, убедитесь, что служба DNS правильно настроена для виртуальной сети. Вы можете создать виртуальную машину в выбранной виртуальной сети и вручную выполнить присоединение к домену, чтобы убедиться, что служба DNS и присоединение к домену работают так, как ожидалось. Это обеспечит успешное развертывание Citrix XenApp Essentials с первой попытки. 
4. При необходимости создайте пиринговое подключение между классической виртуальной сетью Azure, которая используется с Azure RemoteApp, и виртуальной сетью Azure Resource Manager, если они находятся в одном регионе. В противном случае для работы с виртуальной сетью используйте VPN-подключение типа "сеть — сеть". 
5. При необходимости прочтите статью [Как перенести данные в службу Azure RemoteApp и из нее](remoteapp-migrate.md). 
6. Обновите существующий образ Azure RemoteApp, включив в него компонент Citrix VDA (инструкции см. в документации Citrix). 
7. Перейдите в Azure Marketplace и начните развертывание Citrix XenApp Essentials.

Желаем удачи и благодарим за использование Azure RemoteApp. 

## <a name="other-considerations"></a>Дополнительные рекомендации

- Служба Citrix XenApp Essentials поддерживает только гибридные развертывания. Иными словами, для присоединения к домену ей требуется сетевой доступ к контроллеру домена. При использовании облачного развертывания Azure RemoteApp нужно будет использовать AAD DS или обеспечить доступ виртуальной сети к Active Directory для присоединения к домену. 
- Сведения о переносе данных пользователя в CXE см. в статье [Как перенести данные в службу Azure RemoteApp и из нее](remoteapp-migrate.md). 
- Служба Citrix XenApp Essentials поддерживает только учетные записи Active Directory. Учетные записи Майкрософт (@outlook.com, @msn.com, @hotmail.com и т. д.) не поддерживаются. 

## <a name="understanding-billing-for-citrix-xenapp-essentials"></a>Основные сведения о выставлении счетов для Citrix XenApp Essentials 

Полные сведения о ценах см. в [часто задаваемых вопросах](https://www.citrix.com/global-partners/microsoft/resources/xenapp-essentials-faq.html#tab-30699) и [обзорной статье о Citrix](https://www.citrix.com/global-partners/microsoft/remote-app.html). Существует три компонента выставления счетов Citrix XenApp Essentials:

1. Сервисный сбор Citrix — 12 долларов на пользователя в месяц. Как и для всех покупок в Azure Marketplace, для этого сбора используется метод оплаты, связанный с вашей подпиской Azure. Для клиентов с соглашением Enterprise денежные кредиты Azure не используются. 
2. Клиентская лицензия служб удаленных рабочих столов. Необходимо использовать собственную клиентскую лицензию служб удаленных рабочих столов (ожидается в ближайшее время) или внести сбор за удаленный доступ (RAF) вместе с платой за Citrix XenApp Essentials в размере 6,25 долларов США. Клиенты с соглашением Enterprise могут использовать для этой цели денежные кредиты Azure. Если вы желаете использовать существующие клиентские лицензии служб удаленных рабочих столов, свяжитесь с нами [arainfo@microsoft.com](mailto:arainfo@microsoft.com, чтобы мы внесли соответствующие сведения в ваш счет. 
3. Служба вычислений и служба хранилища Azure. Это плата за использование службы хранилища Azure и вычислительных ресурсов для виртуальных машин. Учитывайте цены при выборе размера виртуальных машин и плотности пользователей. Клиенты с соглашением Enterprise могут использовать для этой цели денежные кредиты Azure.

Если у вас еще остались вопросы, свяжитесь с нами.
1. Напишите нам по адресу [arainfo@microsoft.com](mailto:arainfo@microsoft.com).
2. Обратитесь в [службу поддержки Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Чтобы получить помощь в выполнении шагов 1–5, описанных выше, сначала [создайте обращение в службу поддержки Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Чтобы получить помощь в выполнении шагов 6–7, создайте запрос в службу поддержки на портале управления Citrix. 

