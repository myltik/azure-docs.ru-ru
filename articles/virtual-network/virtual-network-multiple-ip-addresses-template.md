---
title: Назначение виртуальным машинам Azure нескольких IP-адресов с помощью шаблона | Документация Майкрософт
description: Узнайте, как назначить виртуальной машине несколько IP-адресов с использованием шаблона Azure Resource Manager.
documentationcenter: ''
author: jimdial
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/08/2016
ms.author: jdial
ms.openlocfilehash: d4b189fb23dda1167c4f6b17b618c718d32dd98f
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-an-azure-resource-manager-template"></a>Назначение виртуальным машинам Azure нескольких IP-адресов с помощью шаблона Azure Resource Manager

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

В этой статье описывается создание виртуальной машины с помощью модели развертывания Azure Resource Manager и шаблона Azure Resource Manager. Несколько общедоступных и частных IP-адресов нельзя назначить одной и той же сетевой карте при развертывании виртуальной машины с помощью классической модели развертывания. Дополнительные сведения о моделях развертывания Azure см. в статье [Azure Resource Manager vs. classic deployment: Understand deployment models and the state of your resources](../resource-manager-deployment-model.md) (Развертывание с помощью Azure Resource Manager и классическое развертывание. Общие сведения о моделях развертывания и состоянии ресурсов).

[!INCLUDE [virtual-network-multiple-ip-addresses-template-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name="template-description"></a>Описание шаблона

Развертывание шаблона позволяет быстро и согласованно создавать ресурсы Azure с использованием разных значений конфигурации. Если вы не работали с шаблонами Azure Resource Manager, ознакомьтесь со статьей [Пошаговое руководство по созданию шаблона Resource Manager](../azure-resource-manager/resource-manager-template-walkthrough.md?toc=%2fazure%2fvirtual-network%2ftoc.json). В этой статье используется шаблон [развертывания виртуальной машины с использованием нескольких IP-адресов](https://azure.microsoft.com/resources/templates/101-vm-multiple-ipconfig).

<a name="resources"></a>При развертывании шаблона создаются следующие ресурсы:

|Ресурс|ИМЯ|ОПИСАНИЕ|
|---|---|---|
|сетевому интерфейсу|*myNic1*|Три IP-конфигурации (см. раздел этой статьи с описанием сценария) создаются и назначаются этой сетевой карте.|
|Ресурс общедоступного IP-адреса|Создаются два имени: *myPublicIP* и *myPublicIP2*|Эти ресурсы представлены общедоступными статическими IP-адресами. Они назначаются IP-конфигурациям *IPConfig 1* и *IPConfig 2*, описанным в сценарии.|
|ВМ|*myVM1*|Виртуальная машина Standard DS3.|
|Виртуальная сеть|*myVNet1*|Виртуальная сеть с одной подсетью с именем *mySubnet*.|
|Учетная запись хранения|Уникальная для развертывания|Учетная запись хранения.|

<a name="parameters"></a>При развертывании шаблона необходимо указать значения для следующих параметров:

|ИМЯ|ОПИСАНИЕ|
|---|---|
|adminUsername|Имя пользователя администратора. Оно должно удовлетворять [соответствующим требованиям Azure](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json).|
|adminPassword|Пароль администратора. Он должен удовлетворять соответствующим [требованиям Azure](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
|dnsLabelPrefix|DNS-имя для PublicIPAddressName1. DNS-имя будет разрешено на один из общедоступных IP-адресов, назначенных виртуальной машине. Имя должно быть уникальным в пределах региона Azure (расположения), в котором создается виртуальная машина.|
|dnsLabelPrefix1|DNS-имя для PublicIPAddressName2. DNS-имя будет разрешено на один из общедоступных IP-адресов, назначенных виртуальной машине. Имя должно быть уникальным в пределах региона Azure (расположения), в котором создается виртуальная машина.|
|OSVersion|Версия ОС Windows или Linux для виртуальной машины. Операционная система — это исправленный образ выбранной версии ОС Windows или Linux.|
|imagePublisher|Издатель образа Windows и Linux для выбранной виртуальной машины.|
|imageOffer|Образ Windows и Linux для выбранной виртуальной машины.|

Каждый из ресурсов, развернутый с помощью шаблона, настраивается с несколькими параметрами по умолчанию. Просмотреть эти параметры можно одним из следующих способов.

- **Просмотрев шаблон на GitHub.** Если вы работали с шаблонами, вы можете просмотреть параметры в самом [шаблоне](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.json).
- **Просмотрев параметры после развертывания.** Если вы не работали с шаблонами, вы можете развернуть шаблон, выполнив действия, описанные в следующих разделах, а затем просмотреть параметры после развертывания.

Чтобы развернуть шаблон, можно использовать портал Azure или интерфейс командной строки Azure. Все методы приводят к одному результату. Выполните действия, описанные в одном из следующих разделов.

## <a name="deploy-using-the-azure-portal"></a>Развертывание с помощью портала Azure

Чтобы развернуть шаблон с помощью портала Azure, выполните следующие действия.

1. При желании шаблон можно изменить. Шаблон развертывает ресурсы и параметры, перечисленные в разделе с описанием [ресурсов](#resources). Дополнительные сведения о шаблонах и способах их создания см. в статье [Создание шаблонов Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Разверните шаблон одним из следующих способов.
    - **Выбрав шаблон на портале.** Выполните действия описанные в [этом разделе](../azure-resource-manager/resource-group-template-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-resources-from-custom-template). Выберите существующий шаблон с именем *101-vm-multiple-ipconfig*.
    - **Непосредственно.** Нажмите эту кнопку, чтобы открыть шаблон прямо на портале: <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-multiple-ipconfig%2Fazuredeploy.json" target="_blank">.<img src="http://azuredeploy.net/deploybutton.png"/></a>

Независимо от выбранного варианта вам нужно указать значения для [параметров](#parameters), перечисленных выше в этой статье. Развернув виртуальную машину, подключитесь к ней и добавьте в развернутую операционную систему частные IP-адреса, выполнив действия, описанные в этой статье в разделе [Добавление IP-адреса в операционную систему виртуальной машины](#os-config). Не добавляйте в операционную систему общедоступные IP-адреса.

## <a name="deploy-using-powershell"></a>Развертывание с помощью PowerShell

Чтобы развернуть шаблон с помощью PowerShell, выполните следующие действия.

1. Разверните шаблон, выполнив действия, описанные в [этом разделе](../azure-resource-manager/resource-group-template-deploy-cli.md). В статье описывается несколько вариантов развертывания шаблона. Если нужно выполнить развертывание с помощью `-TemplateUri parameter`, URI для этого шаблона будет следующим: *https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.json*. Если требуется выполнить развертывание с помощью параметра `-TemplateFile`, скопируйте содержимое [файла шаблона](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.json) из GitHub в новый файл на компьютере. При желании можно изменить содержимое шаблона. Шаблон развертывает ресурсы и параметры, перечисленные в разделе с описанием [ресурсов](#resources). Дополнительные сведения о шаблонах и способах их создания см. в статье [Создание шаблонов Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

    Независимо от выбранного варианта развертывания шаблона вам нужно указать значения для параметров, перечисленных в разделе с описанием [параметров](#parameters). Если вы хотите указать параметры с помощью [файла параметров](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.parameters.json), скопируйте его содержимое из GitHub в новый файл на компьютере. Измените значения в файле. Используйте созданный файл в качестве значения для параметра `-TemplateParameterFile`.

    Чтобы определить допустимые значения для параметров OSVersion, ImagePublisher и imageOffer, выполните шаги, описанные в статье [Просмотр и выбор образов виртуальных машин Windows в Azure с помощью оболочки PowerShell или интерфейса командной строки](../virtual-machines/windows/cli-ps-findimage.md).

    >[!TIP]
    >Если вы не знаете, доступен ли параметр dnslabelprefix, введите `Test-AzureRmDnsAvailability -DomainNameLabel <name-you-want-to-use> -Location <location>` команду. Если параметр доступен, команда вернет значение `True`.

2. Развернув виртуальную машину, подключитесь к ней и добавьте в развернутую операционную систему частные IP-адреса, выполнив действия, описанные в этой статье в разделе [Добавление IP-адреса в операционную систему виртуальной машины](#os-config). Не добавляйте в операционную систему общедоступные IP-адреса.

## <a name="deploy-using-the-azure-cli"></a>Развертывание с помощью интерфейса командной строки Azure

Чтобы развернуть шаблон с помощью Azure CLI 1.0, выполните следующие действия.

1. Разверните шаблон, выполнив действия, описанные в [этом разделе](../azure-resource-manager/resource-group-template-deploy-cli.md). В статье описывается несколько вариантов развертывания шаблона. Если нужно выполнить развертывание с помощью `--template-uri` (-f), URI для этого шаблона будет следующим: *https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.json*. Если требуется выполнить развертывание с помощью параметра `--template-file` (-f) , скопируйте содержимое [файла шаблона](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.json) из GitHub в новый файл на компьютере. При желании можно изменить содержимое шаблона. Шаблон развертывает ресурсы и параметры, перечисленные в разделе с описанием [ресурсов](#resources). Дополнительные сведения о шаблонах и способах их создания см. в статье [Создание шаблонов Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

    Независимо от выбранного варианта развертывания шаблона вам нужно указать значения для параметров, перечисленных в разделе с описанием [параметров](#parameters). Если вы хотите указать параметры с помощью [файла параметров](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.parameters.json), скопируйте его содержимое из GitHub в новый файл на компьютере. Измените значения в файле. Используйте созданный файл в качестве значения для параметра `--parameters-file` (-e).

    Чтобы определить допустимые значения для параметров OSVersion, ImagePublisher и imageOffer, выполните шаги, описанные в статье [Просмотр и выбор образов виртуальных машин Windows в Azure с помощью оболочки PowerShell или интерфейса командной строки](../virtual-machines/windows/cli-ps-findimage.md).

2. Развернув виртуальную машину, подключитесь к ней и добавьте в развернутую операционную систему частные IP-адреса, выполнив действия, описанные в этой статье в разделе [Добавление IP-адреса в операционную систему виртуальной машины](#os-config). Не добавляйте в операционную систему общедоступные IP-адреса.

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]
