<properties services="virtual-machines" title="Using the xplat-cli with Azure Resource Manager" authors="squillace" solutions="" manager="timlt" editor="tysonn" />

<tags
   ms.service="virtual-machine"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="linux"
   ms.workload="infrastructure"
   ms.date="04/13/2015"
   ms.author="rasquill" />

## Использование xplat-cli с диспетчером ресурсов Azure (ARM)

Перед использованием xplat-cli с командами диспетчера ресурсов и шаблонами для развертывания ресурсов Azure и рабочих нагрузок с помощью групп ресурсов потребуется учетная запись с Azure (разумеется). Если у вас нет учетной записи, бесплатную пробную версию Azure можно получить [здесь](http://azure.microsoft.com/pricing/free-trial/).

> [AZURE.NOTE]Если вас еще нет учетной записи Azure, но есть подписка MSDN, вы можете получать бесплатные кредиты Azure, активировав [здесь преимущества подписчика MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Также можно использовать бесплатную учетную запись. Для доступа к Azure подойдут оба способа.

### Шаг 1. Проверка версии xplat-cli

Чтобы использовать xplat-cli для использования безусловных команд и шаблонов ARM, необходимо установить версию не ниже 0.8.17. Чтобы проверить версию, введите `azure --version`. Вы увидите нечто вроде этого:

    $ azure --version
    0.8.17 (node: 0.10.25)
    
Если необходимо обновить версию xplat-cli, см. раздел [xplat-cli](https://github.com/Azure/azure-xplat-cli).

### Шаг 2. Проверка используемого рабочего удостоверения для Azure

Режим команд ARM можно использовать только в случае, если вы используете [клиент Azure Active Directory](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant) или [имя субъекта-службы](https://msdn.microsoft.com/library/azure/dn132633.aspx). (Они также называются *идентификаторами организаций*.)

Чтобы узнать, есть ли у вас такой идентификатор, войдите, введя `azure login`, и укажите рабочее имя пользователя и пароль при запросе. Если он у вас есть, вы увидите следующее:

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
    
Если вы не видите этого, необходимо создать новый клиент (или субъект-службу) с помощью вашего удостоверения учетной записи Майкрософт. (Это часто требуется в случае с личными подписками MSDN или бесплатными пробными подписками.) Чтобы создать рабочий идентификатор с помощью своей учетной записи Azure, созданной с помощью идентификатора Майкрософт, см. раздел [Связывание Azure AD Directory с новой подпиской Azure](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant). Если вы считаете, что у вас уже есть идентификатор организации, может потребоваться обратиться к лицу, создавшему учетную запись для вас.

### Шаг 3. Выбор подписки Azure

Если у вас только одна подписка в учетной записи Azure, xplat-cli сопоставляется с этой подпиской по умолчанию. Если у вас несколько подписок, необходимо выбрать подписку, которую следует использовать, введя `azure account set <subscription id or name> true`, где _идентификатор или имя подписки_ — это идентификатор или имя подписки, с которой вы хотите работать в текущем сеансе.

Вы должны увидеть нечто вроде этого:

    $ azure account set "Azure Free Trial" true
    info:    Executing command account set
    info:    Setting subscription to "Azure Free Trial" with id "2lskd82-434-4730-9df9-akd83lsk92sa".
    info:    Changes saved
    info:    account set command OK
    
### Шаг 4. Перевод xplat-cli в режим ARM

Чтобы использовать режим управления ресурсами Azure (ARM) с xplat-cli, введите `azure config mode arm`. Вы должны увидеть нечто вроде этого:

    $ azure config mode arm
    info:    New mode is arm

> [AZURE.NOTE]Вернуться к использованию команд управления службами Azure можно, введя `azure config mode asm`.



<!--HONumber=52-->
