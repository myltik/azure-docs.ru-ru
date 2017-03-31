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
ms.date: 03/17/2017
ms.author: iainfou
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: cea53acc33347b9e6178645f225770936788f807
ms.openlocfilehash: 44a82f3eaafa231c22429968fcac63872b74c75b
ms.lasthandoff: 03/03/2017


---
# <a name="azure-subscription-and-accounts-guidelines-for-linux-vms"></a>Рекомендации по подпискам и учетным записям Azure для виртуальных машин Linux

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

![](windows/media/virtual-machines-common-infrastructure-service-guidelines/sub01.png)

Например, можно использовать указанную ниже структуру.

![](windows/media/virtual-machines-common-infrastructure-service-guidelines/sub02.png)

Если нужно сопоставить несколько подписок для региона с определенной группой, то соглашение об именовании должно предусматривать способ указания дополнительных данных в имени учетной записи или подписки. Такая организация позволяет уплотнять данные для выставления счетов, чтобы можно было создавать уровни иерархии во время отправки отчетов о выставлении счетов.

![](windows/media/virtual-machines-common-infrastructure-service-guidelines/sub03.png)

Организация может выглядеть следующим образом.

![](windows/media/virtual-machines-common-infrastructure-service-guidelines/sub04.png)

Мы выставляем детализированные счета в виде скачиваемого файла для одной учетной записи или для всех учетных записей, включенных в Соглашение Enterprise.

## <a name="next-steps"></a>Дальнейшие действия
[!INCLUDE [virtual-machines-linux-infrastructure-guidelines-next-steps](../../includes/virtual-machines-linux-infrastructure-guidelines-next-steps.md)]


