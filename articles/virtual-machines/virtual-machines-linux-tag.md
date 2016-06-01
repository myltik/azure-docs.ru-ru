<properties
   pageTitle="Пометка виртуальной машины Linux | Microsoft Azure"
   description="Узнайте, как добавлять теги к виртуальной машине Linux, созданной в Azure с помощью модели развертывания на основе диспетчера ресурсов."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="mmccrory"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="04/06/2016"
   ms.author="iainfour;memccror"/>

# Пометка виртуальной машины Linux в Azure

В этой статье описываются разные способы назначения тегов виртуальной машине Linux в Azure с использованием модели развертывания Resource Manager. Теги — это определяемые пользователем пары "ключ-значение", которые можно помещать непосредственно в ресурс или группу ресурсов. В настоящий момент Azure поддерживает до 15 тегов на ресурс или группу ресурсов. Теги можно добавлять к ресурсу во время его создания или к уже существующему ресурсу. Обратите внимание, что теги поддерживаются только для тех ресурсов, которые созданы с помощью модели развертывания Resource Manager.

[AZURE.INCLUDE [virtual-machines-common-tag](../../includes/virtual-machines-common-tag.md)]

## Отметка тегами с помощью интерфейса командной строки Azure

Теги также поддерживаются для ресурсов, которые уже созданы с помощью интерфейса командной строки Azure. Для начала настройте [среду интерфейса командной строки Azure][]. Войдите в свою подписку с помощью интерфейса командной строки Azure и переключитесь в режим Resource Manager (`azure config mode arm`).

Вы можете просмотреть все свойства определенной виртуальной машины, включая теги, с помощью следующей команды:

        azure vm show -g MyResourceGroup -n MyTestVM

Чтобы добавить новый тег виртуальной машины через интерфейс командной строки Azure, можно применить команду `azure vm set` с параметром тега **-t**:

        azure vm set -g MyResourceGroup -n MyTestVM –t myNewTagName1=myNewTagValue1;myNewTagName2=myNewTagValue2

Чтобы удалить все теги, можно использовать параметр **–T** в команде `azure vm set`.

        azure vm set – g MyResourceGroup –n MyTestVM -T


Теперь, когда мы применили теги к ресурсам с помощью Azure CLI и портала, рассмотрим сведения об использовании, чтобы увидеть теги на портале выставления счетов.

[AZURE.INCLUDE [virtual-machines-common-tag-usage](../../includes/virtual-machines-common-tag-usage.md)]

## Дальнейшие действия

* Дополнительные сведения о добавлении тегов для ресурсов Azure см. в статьях [Общие сведения об Azure Resource Manager][] и [Использование тегов для организации ресурсов в Azure][].
* Сведения о том, как теги могут помочь в управлении использованием ресурсов Azure, см. в статьях [Расшифровка счета за использование Microsoft Azure][] и [Получение ценных сведений о потреблении ресурсов Microsoft Azure][].





[среду интерфейса командной строки Azure]: ./xplat-cli-azure-resource-manager.md
[Общие сведения об Azure Resource Manager]: ../resource-group-overview.md
[Использование тегов для организации ресурсов в Azure]: ../resource-group-using-tags.md
[Расшифровка счета за использование Microsoft Azure]: ../billing-understand-your-bill.md
[Получение ценных сведений о потреблении ресурсов Microsoft Azure]: ../billing-usage-rate-card-overview.md

<!---HONumber=AcomDC_0518_2016-->