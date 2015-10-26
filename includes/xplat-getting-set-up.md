<properties services="virtual-machines" title="Setting up Azure CLI for service management" authors="squillace" solutions="" manager="timlt" editor="tysonn" />

<tags
   ms.service="virtual-machine"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="linux"
   ms.workload="infrastructure"
   ms.date="04/13/2015"
   ms.author="rasquill" />

## Использование Azure CLI

Описанные ниже действия помогут вам установить последнюю версию Azure CLI и выбрать подходящую подписку. Если сначала вам необходимо установить интерфейс Azure CLI и подключить его к своей учетной записи, см. статью [Интерфейс командной строки Azure (Azure CLI)](xplat-cli-install.md).

### Шаг 1. Обновление версии Azure CLI

Чтобы использовать Azure CLI для выполнения безусловных команд в режиме управления службами, установите последнюю версию, если это возможно. Чтобы проверить версию, введите `azure --version`. Вы увидите нечто вроде этого:

    $ azure --version
    0.8.17 (node: 0.10.25)

Инструкции по обновлению Azure CLI см. в разделе [Azure CLI](https://github.com/Azure/azure-xplat-cli).

### Шаг 2. Настройка учетной записи и подписки Azure

В учетной записи, к которой вы подключили Azure CLI, может быть несколько подписок. Если это так, необходимо просмотреть имеющиеся подписки для учетной записи, введя `azure account list`, и выбрать подписку, которую следует использовать, введя `azure account set <subscription id or name> true`, где _идентификатор или имя подписки_ — это идентификатор или имя подписки, с которой вы хотите работать в текущем сеансе. Вы должны увидеть нечто вроде этого:

    $ azure account set "Visual Studio Ultimate with MSDN" true
    info:    Executing command account set
    info:    Setting subscription to "Visual Studio Ultimate with MSDN" with id "0e220bf6-5caa-4e9f-8383-51f16b6c109f".
    info:    Changes saved
    info:    account set command OK

> [AZURE.NOTE]Если вас еще нет учетной записи Azure, но есть подписка MSDN, вы можете получать бесплатные кредиты Azure, активировав [здесь преимущества подписчика MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Также можно использовать бесплатную учетную запись. Для доступа к Azure подойдут оба способа.

<!---HONumber=Oct15_HO3-->