---
title: Создание набора масштабирования виртуальных машин с помощью портала Azure | Microsoft Docs
description: Развертывание наборов масштабирования с помощью портала Azure.
keywords: наборы масштабирования виртуальных машин
services: virtual-machine-scale-sets
documentationcenter: ''
author: gatneil
manager: madhana
editor: tysonn
tags: azure-resource-manager

ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.devlang: na
ms.topic: article
ms.date: 09/15/2016
ms.author: gatneil

---
# <a name="create-a-virtual-machine-scale-set-using-the-azure-portal"></a>Создание набора масштабирования виртуальных машин с помощью портала Azure
В этом руководстве мы покажем, как на портале Azure всего за несколько минут можно создать набор масштабирования виртуальных машин. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/) , прежде чем начинать работу.

## <a name="choose-the-vm-image-from-the-marketplace"></a>Выбор образа виртуальной машины из Marketplace
На портале можно легко развернуть набор масштабирования с образами CentOS, CoreOS, Debian, OpenSUSE, Red Hat Enterprise Linux, SUSE Linux Enterprise Server, Ubuntu Server или Windows Server.

Сначала откройте в веб-браузере [портал Azure](https://portal.azure.com) . Щелкните `New`, выполните поиск по запросу `scale set`, а затем выберите `Virtual machine scale set`:

![ScaleSetPortalOverview](./media/virtual-machine-scale-sets-portal-create/ScaleSetPortalOverview.PNG)

## <a name="create-the-linux-virtual-machine"></a>Создание виртуальной машины Linux
Теперь можно оставить значения, используемые по умолчанию, и быстро создать виртуальную машину.

* В колонке `Basics` (Основы) введите имя для набора масштабирования. Это имя станет основой полного доменного имени балансировщика нагрузки для набора масштабирования, поэтому оно должно быть уникальным в Azure.
* Выберите нужный тип ОС, введите имя пользователя и выберите тип проверки подлинности. Если выбран пароль, он должен содержать не менее 12 символов и включать по меньшей мере три из следующих символов: одна строчная буква, одна прописная буква, одна цифра и один специальный символ. См. дополнительные сведения о [требованиях к имени пользователя и паролю](../virtual-machines/virtual-machines-windows-faq.md#what-are-the-username-requirements-when-creating-a-vm). Если выбран вариант `SSH public key` (Открытый ключ SSH), следует вставить открытый, а НЕ закрытый ключ:

![ScaleSetPortalBasics](./media/virtual-machine-scale-sets-portal-create/ScaleSetPortalBasics.PNG)

* Введите имя и расположение нужной группы ресурсов и щелкните `OK`.
* В колонке `Virtual machine scale set service settings` (Параметры службы набора масштабирования виртуальных машин) введите метку имени нужного домена (основа полного доменного имени балансировщика нагрузки для набора масштабирования). Эта метка должна быть уникальной в Azure.
* Выберите образ диска требуемой операционной системы, число экземпляров и размер виртуальной машины.
* Включите или отключите автомасштабирование и настройте его (если включено):

![ScaleSetPortalService](./media/virtual-machine-scale-sets-portal-create/ScaleSetPortalService.PNG)

* После завершения проверки нажмите кнопку `OK` в колонке `Summary`.
* Наконец, в колонке `Purchase` (Приобретение) щелкните `Purchase` (Приобрести), чтобы начать развертывание набора масштабирования.

## <a name="connect-to-a-vm-in-the-scale-set"></a>Подключение к виртуальной машине в наборе масштабирования
После развертывания набора масштабирования перейдите к вкладке `Inbound NAT Rules` (Правила NAT для входящего трафика) балансировщика нагрузки для набора масштабирования:

![ScaleSetPortalNatRules](./media/virtual-machine-scale-sets-portal-create/ScaleSetPortalNatRules.PNG)

С помощью этих правил преобразования сетевых адресов можно подключиться к каждой виртуальной машине в наборе масштабирования. Например, если в наборе масштабирования Windows имеется правило NAT на порту 50000 для входящих подключений, к этой виртуальной машине можно подключиться через удаленный рабочий стол по адресу `<load-balancer-ip-address>:50000`. В случае с набором масштабирования Linux установить подключение можно с помощью команды `ssh -p 50000 <username>@<load-balancer-ip-address>`.

## <a name="next-steps"></a>Дальнейшие действия
Сведения о развертывании наборов масштабирования в командной строке см. в [следующей документации](virtual-machine-scale-sets-cli-quick-create.md).

Сведения о развертывании наборов масштабирования с помощью PowerShell см. в [следующей документации](virtual-machine-scale-sets-windows-create.md).

Сведения о развертывании наборов масштабирования с помощью Visual Studio см. в [следующей документации](virtual-machine-scale-sets-vs-create.md).

Общая документация доступна на [странице обзора документации по наборам масштабирования](virtual-machine-scale-sets-overview.md).

Общие сведения см. на [главной целевой странице по наборам масштабирования](https://azure.microsoft.com/services/virtual-machine-scale-sets/).

<!--HONumber=Oct16_HO2-->


