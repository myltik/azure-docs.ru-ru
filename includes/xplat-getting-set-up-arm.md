---
services: virtual-machines
title: "Использование Azure CLI с Azure Resource Manager"
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
ms.sourcegitcommit: e664ce9426a2852a35dfdade5d41a9ce8b37a3b7
ms.openlocfilehash: e2f9d2c74e5dfa0a08f25685062903a985ba641c


---
## <a name="using-azure-cli-with-azure-resource-manager-arm"></a>Использование Azure CLI с диспетчером ресурсов Azure (ARM)
Чтобы использовать интерфейс Azure CLI с командами диспетчера ресурсов и шаблонами для развертывания ресурсов Azure и рабочих нагрузок с помощью групп ресурсов, вам, разумеется, потребуется учетная запись Azure. Если у вас нет учетной записи, бесплатную пробную версию Azure можно получить [здесь](https://azure.microsoft.com/pricing/free-trial/).

> [!NOTE]
> Если у вас еще нет учетной записи Azure, но есть подписка MSDN, вы можете бесплатно получить деньги на счете в Azure, активировав [преимущества для подписчиков MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Также можно использовать бесплатную учетную запись. Для доступа к Azure подойдут оба способа.
> 
> 

### <a name="step-1-verify-the-azure-cli-version"></a>Шаг 1. Проверка версии Azure CLI
Чтобы использовать Azure CLI для выполнения безусловных команд и шаблонов ARM, вам потребуется версия не ниже 0.8.17. Чтобы проверить версию, введите `azure --version`. Вы увидите нечто вроде этого:

    $ azure --version
    0.8.17 (node: 0.10.25)

При необходимости см. инструкции по обновлению [Azure CLI](https://github.com/Azure/azure-xplat-cli).

### <a name="step-2-verify-you-are-using-a-work-or-school-identity-with-azure"></a>Шаг 2. Проверка используемого рабочего удостоверения для Azure
Режим команд ARM доступен, только если вы используете [клиент Azure Active Directory](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant) или [имя субъекта-службы](https://msdn.microsoft.com/library/azure/dn132633.aspx). (Они также называются *идентификаторами организаций*.)

Чтобы узнать, есть ли у вас такой идентификатор, войдите, введя `azure login` , и укажите рабочее имя пользователя и пароль при запросе. Если он у вас есть, вы увидите следующее:

    $ azure login
    info:    Executing command login
    warn:    Please note that currently you can login only via Microsoft organizational account or service principal. For instructions on how to set them up, please read http://aka.ms/Dhf67j.
    Username: ahmet@contoso.onmicrosoft.com
    Password: *********
    |info:    Added subscription Visual Studio Ultimate with MSDN
    info:    Setting subscription Visual Studio Ultimate with MSDN as default
    info:    Added subscription Azure Free Trial
    +
    info:    login command OK

Если вы не видите этого, необходимо создать новый клиент (или субъект-службу) с помощью вашего удостоверения учетной записи Майкрософт. (Это распространенная ситуация при использовании личных подписок MSDN или подписок на бесплатную пробную версию.) Чтобы создать рабочий идентификатор с учетной записью, созданной с помощью идентификатора Microsoft Azure, см. инструкции по [привязке каталога Azure AD к новой подпиской Azure](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant). Если вы считаете, что у вас уже есть идентификатор организации, может потребоваться обратиться к лицу, создавшему учетную запись для вас.

### <a name="step-3-choose-your-azure-subscription"></a>Шаг 3. Выбор подписки Azure
Если в вашей учетной записи Azure только одна подписка, Azure CLI связывается с ней по умолчанию. Если у вас несколько подписок, необходимо выбрать одну. Для этого введите `azure account set <subscription id or name> true`, где *subscription id or name* — это идентификатор или имя подписки, которая будет использоваться в рамках текущего сеанса.

Вы должны увидеть нечто вроде этого:

    $ azure account set "Azure Free Trial" true
    info:    Executing command account set
    info:    Setting subscription to "Azure Free Trial" with id "2lskd82-434-4730-9df9-akd83lsk92sa".
    info:    Changes saved
    info:    account set command OK

### <a name="step-4-place-your-azure-cli-in-the-arm-mode"></a>Шаг 4. Перевод Azure CLI в режим ARM
Чтобы использовать режим управления ресурсами Azure (ARM) с Azure CLI, введите `azure config mode arm`. Вы должны увидеть нечто вроде этого:

    $ azure config mode arm
    info:    New mode is arm

> [!NOTE]
> Вернуться к использованию команд управления службами Azure можно, введя `azure config mode asm`.
> 
> 




<!--HONumber=Jan17_HO3-->


