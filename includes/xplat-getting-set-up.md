---
services: virtual-machines
title: "Настройка интерфейса командной строки Azure для управления службами"
author: squillace
solutions: 
manager: timlt
editor: tysonn
ms.service: virtual-machine
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: linux
ms.workload: infrastructure
ms.date: 04/13/2015
ms.author: rasquill
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 737e4be21eefcbd6fbd31d15a6f77770cd59ca67
ms.lasthandoff: 03/21/2017


---
## <a name="using-azure-cli"></a>Использование Azure CLI
Описанные здесь шаги помогут вам использовать интерфейс командной строки Azure (Azure CLI) самой последней версии и выбрать подходящую подписку. Если сначала вам необходимо установить интерфейс Azure CLI и подключить его к своей учетной записи, см. статью об [установке интерфейса командной строки Azure](../articles/cli-install-nodejs.md).

### <a name="step-1-update-azure-cli-version"></a>Шаг 1. Обновление версии Azure CLI
Чтобы использовать Azure CLI для выполнения безусловных команд в режиме управления службами, установите последнюю версию, если это возможно. Чтобы проверить версию, введите `azure --version`. Вы увидите нечто вроде этого:

    $ azure --version
    0.8.17 (node: 0.10.25)

Инструкции по обновлению Azure CLI см. в разделе об [Azure CLI](https://github.com/Azure/azure-xplat-cli).

### <a name="step-2-set-the-azure-account-and-subscription"></a>Шаг 2. Настройка учетной записи и подписки Azure
В учетной записи, к которой вы подключили Azure CLI, может быть несколько подписок. Если это ваш случай, необходимо просмотреть подписки, доступные для вашей учетной записи. Для этого введите `azure account list` и выберите нужную подписку, введя `azure account set <subscription id or name> true`, где *subscription id or name* — это идентификатор или имя подписки, с которой вы хотите работать в текущем сеансе. Вы должны увидеть нечто вроде этого:

    $ azure account set "Visual Studio Ultimate with MSDN" true
    info:    Executing command account set
    info:    Setting subscription to "Visual Studio Ultimate with MSDN" with id "0e220bf6-5caa-4e9f-8383-51f16b6c109f".
    info:    Changes saved
    info:    account set command OK

> [!NOTE]
> Если у вас еще нет учетной записи Azure, но есть подписка MSDN, вы можете бесплатно получить деньги на счете в Azure, активировав [преимущества для подписчиков MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Также можно использовать бесплатную учетную запись. Для доступа к Azure подойдут оба способа.
> 
> 


