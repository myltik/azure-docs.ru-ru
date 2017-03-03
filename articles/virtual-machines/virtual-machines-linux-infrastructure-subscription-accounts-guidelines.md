---
title: "Подписка и учетная запись для виртуальных машин Linux в Azure | Документация Майкрософт"
description: "Изучите основные рекомендации по проектированию и реализации, касающиеся подписок и учетных записей в Azure."
documentationcenter: 
services: virtual-machines-linux
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 19343826-7eef-42a1-98be-4ec65b0f377a
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/16/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 233116deaaaf2ac62981453b05c4a5254e836806
ms.openlocfilehash: 0a4ddfa550913df1c987d1782bcd4bc3c41eb048
ms.lasthandoff: 01/31/2017


---
# <a name="azure-subscription-and-accounts-guidelines"></a>Рекомендации по подпискам и учетным записям Azure
[!INCLUDE [virtual-machines-linux-infrastructure-guidelines-intro](../../includes/virtual-machines-linux-infrastructure-guidelines-intro.md)]

Эта статья посвящена различным подходам к управлению подписками и учетными записями по мере расширения среды и базы пользователей.

## <a name="implementation-guidelines-for-subscriptions-and-accounts"></a>Рекомендации по реализации подписок и учетных записей
Решения

* Какие подписки и учетные записи необходимы для размещения рабочей нагрузки ИТ-среды или ИТ-инфраструктуры?
* Что должна включать в себя иерархия в соответствии с потребностями вашей организации?

Задачи

* Определите логическую иерархию организации с точки зрения управления на уровне подписки.
* В соответствии с этой логической иерархией определите необходимые учетные записи и подписки в каждой из них.
* Создайте набор подписок и учетных записей с использованием соглашения об именовании.

## <a name="subscriptions-and-accounts"></a>Подписки и учетные записи
Для работы с Azure требуется одна или несколько подписок Azure. В них размещаются такие ресурсы, как виртуальные машины или виртуальные сети.

* Как правило, у корпоративных клиентов есть Соглашение о регистрации Enterprise, которое считается ресурсом самого верхнего уровня в иерархии и сопоставлено с одной или несколькими учетными записями.
* Для клиентов без Соглашения о регистрации Enterprise ресурсом верхнего уровня считается учетная запись.
* Подписки сопоставлены с учетными записями. С одной учетной записью может быть связано несколько подписок. Azure хранит данные для выставления счетов на уровне подписки.

Так как количество уровней иерархии для связи между учетной записью и подпиской ограничено двумя, важно, чтобы соглашение об именовании для учетных записей и подписок соответствовало требованиям к выставлению счетов. Например, если в транснациональной компании используется Azure, то можно создать по одной учетной записи на регион и управлять подписками на уровне региона.

![](./media/virtual-machines-common-infrastructure-service-guidelines/sub01.png)

Например, можно использовать указанную ниже структуру.

![](./media/virtual-machines-common-infrastructure-service-guidelines/sub02.png)

Если нужно сопоставить несколько подписок для региона с определенной группой, то соглашение об именовании должно предусматривать способ указания дополнительных данных в имени учетной записи или подписки. Такая организация позволяет уплотнять данные для выставления счетов, чтобы можно было создавать уровни иерархии во время отправки отчетов о выставлении счетов.

![](./media/virtual-machines-common-infrastructure-service-guidelines/sub03.png)

Организация может выглядеть следующим образом.

![](./media/virtual-machines-common-infrastructure-service-guidelines/sub04.png)

Мы выставляем детализированные счета в виде скачиваемого файла для одной учетной записи или для всех учетных записей, включенных в Соглашение Enterprise.

## <a name="next-steps"></a>Дальнейшие действия
[!INCLUDE [virtual-machines-linux-infrastructure-guidelines-next-steps](../../includes/virtual-machines-linux-infrastructure-guidelines-next-steps.md)]


