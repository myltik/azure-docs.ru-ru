---
title: Часто задаваемые вопросы по переходу с классического портала на портал Azure | Документация Майкрософт
description: Содержит ответы на часто задаваемые вопросы о перемещении устройства StorSimple с классического портала на портал Azure.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/28/2017
ms.author: alkohli
ms.openlocfilehash: ac57894e4f180f42f80479d2031f4dd5ddfdb1be
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/19/2018
ms.locfileid: "27785185"
---
# <a name="move-storsimple-device-manager-service-from-classic-to-azure-portal-frequently-asked-questions-faq"></a>Перемещение службы диспетчера устройств StorSimple с классического портала на портал Azure. Часто задаваемые вопросы

## <a name="overview"></a>Обзор

Ниже приведены вопросы, которые могут возникнуть при перемещении работающей службы диспетчера устройств StorSimple с классического портала Azure на портал Azure, и ответы на них.

Вопросы и ответы упорядочены по следующим категориям:

* перемещение службы диспетчера устройств StorSimple;
* перемещение записей хранилища BLOB-объектов;
* использование командлетов на основе Azure Resource Manager;
* использование службы диспетчера данных StorSimple;
* Разное

## <a name="moving-storsimple-device-manager-service"></a>перемещение службы диспетчера устройств StorSimple;

### <a name="once-i-have-moved-to-azure-portal-can-i-still-create-a-storsimple-manager-service-in-the-classic-portal"></a>После перехода на портал Azure можно ли все еще создать службу диспетчера StorSimple на классическом портале?

Нет. После переноса службы диспетчера StorSimple на портал Azure создать новую службу на классическом портале нельзя. Кроме того, [классический портал будет недоступен с 8 января 2018 года](https://azure.microsoft.com/updates/azure-portal-updates-for-classic-portal-users). 

### <a name="i-have-multiple-storsimple-managers-running-in-the-classic-portal-can-i-choose-which-ones-to-move-to-the-azure-portal"></a>У меня есть несколько диспетчеров StorSimple, работающих на классическом портале. Можно ли выбрать какие из них следует переместить на портал Azure?

Нет. Вы не можете выбрать, какие диспетчеры StorSimple будут перемещены на портал Azure. Все диспетчеры StorSimple в подписке будут перемещены.


### <a name="i-initiated-the-migration-of-my-service-to-the-new-azure-portal-should-i-delete-the-storsimple-manager-from-the-classic-portal"></a>Была запущена миграция службы на новый портал Azure. Следует удалить диспетчеры StorSimple с классического портала? 

Нет. Не удаляйте никакие службы, которые были перемещены с классического портала. Дождитесь завершения миграции, как только все диспетчеры StorSimple перейдут на новый портал, вам не нужно будет удалять какие-либо службы с классического портала. В конечном итоге классический портал является устаревшим.

### <a name="can-i-rename-my-storsimple-device-manager-service-in-the-azure-portal"></a>Можно ли переименовать службу диспетчера устройств StorSimple на портале Azure?

Нет. Имя службы невозможно изменить после ее создания на портале Azure. Та же это верно для других сущностей, таких как устройства, тома, контейнеры томов и политики резервного копирования.

### <a name="can-i-create-the-80108020-storsimple-cloud-appliances-with-azure-resource-manager-deployment-model"></a>Можно ли создавать облачное устройство StorSimple 8010/8020 с помощью модели развертывания Azure Resource Manager?

Да. Вы можете создать облачное устройство StorSimple 8010/8020 с помощью модели развертывания Azure Resource Manager. Базовые виртуальные машины для этих облачных устройств также имеются в модели развертывания диспетчера ресурсов.

### <a name="when-we-migrate-subscriptions-to-the-azure-portal-will-the-underlying-vms-for-the-storsimple-cloud-appliances-also-migrate-to-azure-resource-management-deployment-model"></a>При миграции подписок на портал Azure базовые виртуальные машины для облачных устройств StorSimple также переносятся в модель развертывания управления ресурсами Azure?

Перемещение службы StorSimple не зависит от управления виртуальными машинами для облачных устройств StorSimple. Даже сегодня можно полностью управлять виртуальными машинами для облачных Устройств StorSimple, как на классическом портале, так на и портале Azure.

### <a name="can-i-manage-existing-classic-80108020-storsimple-cloud-appliance-from-the-azure-portal"></a>Можно ли управлять другими имеющимися классическими облачными устройствами StorSimple 8010/8020 с портала Azure?

Да. Виртуальные машины, связанные с имеющимися облачными устройствами 8010/8020, могут управляться с портала Azure.

При создании модели облачного устройства StorSimple Cloud 8010/8020, работающей под управлением версии 3.0 или более поздней, вы не подвержены влиянию службы, перемещаемой на новый портал Azure. Вы можете полностью управлять облачными устройствами без каких-либо проблем. 

При наличии на классическом портале облачных устройств под управлением версий более ранних, чем обновление 3.0, возможности облака будут ограничены. Дополнительные сведения см. в разделе [Перемещение службы на портал Azure](storsimple-8000-manage-service.md##supported-operations-on-devices-running-versions-prior-to-update-50).

Вы не можете обновить облачное устройство. Используйте последнюю версию программного обеспечения для создания облачного устройства, а затем выполните отработку отказа в созданное облачное устройство. Дополнительные сведения см. в разделе [Отработка отказа на облачное устройство](storsimple-8000-cloud-appliance-u2.md#fail-over-to-the-cloud-appliance).


### <a name="my-storsimple-8000-series-device-is-running-update-20-i-migrated-my-service-to-new-azure-portal-my-device-connected-successfully-but-it-seems-that-i-am-not-able-to-fully-manage-my-device-how-do-i-resolve-this-behavior"></a>Мое устройство серии StorSimple 8000 выполняется с обновлением 2.0. Я перенес свою службу на портал Azure. Мое устройство успешно подключено, но похоже, что я не могу полностью управлять им. Есть ли способ решения этой проблемы?

На новом портале Azure поддерживаются только устройства StorSimple с обновлениями 3.0 и более поздних версий. Если устройство выполнялось с обновлением 2.0, для этого устройства будут доступны только ограниченные функции. Дополнительные сведения см. в разделе [Перемещение службы на портал Azure](storsimple-8000-manage-service.md##supported-operations-on-devices-running-versions-prior-to-update-50).

Чтобы полностью управлять устройством, установите последнее обновление на нем. Дополнительные сведения см. в статье [Установка обновления 5 на устройство StorSimple](storsimple-8000-install-update-5.md).

### <a name="i-just-moved-my-storsimple-manager-service-to-the-azure-portal-i-am-seeing-some-alerts-related-to-my-device-is-this-behavior-related-to-the-move"></a>Служба диспетчера StorSimple была недавно перемещена на портал Azure. Я вижу несколько оповещений, связанных с устройством. Это реакция на перемещение?

Нет. Перемещение само по себе не должно вызывать сообщения об ошибках или оповещения. Следуйте рекомендациям об оповещениях для их разрешения.

### <a name="i-am-using-a-storsimple-50007000-series-device-are-these-also-supported-in-the-azure-portal"></a>Я использую устройства StorSimple серии 5000 или 7000. Поддерживаются ли они на портале Azure?

Нет. Устройства StorSimple серии 5000 или 7000 не поддерживаются на портале Azure.

### <a name="i-am-planning-to-migrate-data-from-storsimple-50007000-series-device-to-a-storsimple-8000-series-device-how-does-moving-a-service-to-azure-portal-impact-my-data-migration"></a>Я планирую перенести данные с устройства StorSimple серии 5000 или 7000 на устройства StorSimple серии 8000. Как перемещение службы на портал Azure повлияет на перемещение данных? 

Вы можете перенести данные с устройства StorSimple серии 5000 или 7000 на устройства StorSimple серии 8000 на портале Azure. Чтобы включить перенос данных из серии 5000 или 7000 в серию 8000, необходимо [зарегистрировать запрос в службу поддержки Майкрософт](storsimple-8000-contact-microsoft-support.md).


## <a name="moving-subscriptions-storage-accounts-resource-groups"></a>Перемещение подписок, учетных записей хранения, групп ресурсов

### <a name="can-i-move-storsimple-device-manager-from-one-subscription-to-another-subscription-in-the-azure-portal"></a>Можно ли перемещать диспетчер устройств StorSimple с одной подписки в другую на портале Azure?

Нет. Перемещение службы диспетчера устройств StorSimple с одной подписки в другую не поддерживается. Процесс, состоящий из следующих шагов, можно выполнить вручную:

* Перенесите данные с устройства StorSimple.
* Выполните для устройства сброс до параметров по умолчанию. При этом все локальные данные на устройстве будут удалены.
* Зарегистрируйте устройство с новой подпиской в службе диспетчера устройств StorSimple.
* Верните данные на устройство.

### <a name="do-i-have-to-migrate-the-storage-account-to-azure-resource-manager-deployment-model"></a>Нужно ли переносить учетную запись хранения в модели развертывания Azure Resource Manager?

Нет. Классические учетные записи хранения могут полностью управляться на портале Azure. При перемещении службы StorSimple на портал Azure учетная записи хранения продолжает функционировать без изменений.

### <a name="what-happens-to-the-storage-account-after-the-service-is-migrated-to-the-azure-portal"></a>Что происходит с учетной записью хранения после переноса службы на портал Azure?

Перемещение службы не связано с управлением учетной записью хранения. Классическая учетная запись хранения и учетная запись хранения Azure Resource Manager могут полностью управляться с портала Azure. После перемещения службы на портал Azure устройство продолжит работать с этой учетной записью хранения и не повлияет на данные.

### <a name="can-i-migrate-storsimple-device-manager-from-one-resource-group-to-another"></a>Можно ли выполнить миграцию диспетчера устройств StorSimple из одной группы ресурсов в другую?

Нет. Диспетчер устройств StorSimple, созданный в одной группе ресурсов, нельзя переместить в другую.

## <a name="using-azure-resource-manager-based-cmdlets"></a>Использование командлетов на основе Azure Resource Manager

### <a name="i-moved-to-the-new-azure-portal-now-my-scripts-based-on-azure-classic-deployment-model-powershell-cmdlets-are-not-working-what-do-i-need-to-do"></a>Была выполнена миграция на новый портал Azure. Теперь мои сценарии на основе командлетов PowerShell классической модели развертывания Azure не работают? Что нужно сделать?

Имеющиеся командлеты PowerShell классической модели развертывания не поддерживаются на портале Azure. Обновите скрипты для управления устройствами через Azure Resource Manager. Дополнительные сведения об обновлении сценариев см. на странице [примеров для нового портала Azure](https://github.com/anoobbacker/storsimpledevicemgmttools).

### <a name="i-just-moved-to-the-azure-portal-and-needed-to-roll-over-the-service-data-encryption-key-where-is-this-option-in-the-azure-portal"></a>Недавно была выполнена миграция на портал Azure и мне нужно сменить ключ шифрования данных службы. Где находится этот параметр на портале Azure?

Параметр смены ключа шифрования данных службы находится не на портале Azure. Дополнительные сведения о том, как выполнить выделение на портале Azure см. в разделе [Изменение ключа шифрования данных службы](storsimple-8000-manage-service.md#change-the-service-data-encryption-key).

### <a name="i-am-using-windows-powershell-for-storsimple-cmdlets-on-the-storsimple-device-to-perform-operations-such-extract-a-support-package-are-these-cmdlets-affected-when-i-move-to-the-new-azure-portal"></a>Я использую командлеты Windows PowerShell для StorSimple на устройстве StorSimple для выполнения операций, таких как извлечение пакета поддержки. Эти командлеты будут затронуты при переходе на новый портал Azure?

Нет. При перемещении службы на новый портал Azure командлеты Windows PowerShell для StorSimple, связанные с локальным устройством StorSimple (которое также не будет затронуто перемещением), не будут затронуты. Вы можете продолжать использовать эти командлеты для создания пакета поддержки без каких-либо проблем даже на портале Azure. Переход затрагивает только командлеты PowerShell классической модели развертывания Azure.

## <a name="moving-storsimple-data-manager-service"></a>Перемещение службы диспетчера данных StorSimple

### <a name="i-am-using-storsimple-data-manager-service-in-classic-azure-portal-how-should-i-proceed-with-this-move"></a>Я использую службу диспетчера данных StorSimple на классическом портале Azure. Как мне следует продолжать перенос?

Если используется служба диспетчера данных StorSimple, вы будете автоматически перемещены на портал Azure.

## <a name="miscellaneous"></a>Разное

### <a name="i-am-running-storsimple-snapshot-manager-for-my-8000-series-device-is-there-any-impact-on-storsimple-snapshot-manager-when-i-move-to-azure-portal"></a>Я использую StorSimple Snapshot Manager на своих устройствах серии 8000. Будет ли StorSimple Snapshot Manager затронут при перемещении на портал Azure?

Нет. StorSimple Snapshot Manager не будет затронут при перемещении на портал Azure. Устройство и StorSimple Snapshot Manager продолжает функционировать без изменений.

### <a name="can-i-rename-my-storsimple-device-volume-containers-or-volumes"></a>Можно ли переименовать устройство StorSimple, контейнеры томов или тома?

Нет. Устройства, тома, контейнеры томов или политики резервного копирования нельзя переименовать на портале Azure.

## <a name="next-steps"></a>Дополнительная информация

Узнайте подробнее о [поддерживаемых операциях на устройствах под управлением версий, предшествующих обновлению 5.0](storsimple-8000-manage-service.md#supported-operations-on-devices-running-versions-prior-to-update-50).



