---
title: "Создание ферм серверов SharePoint | Документация Майкрософт"
description: "Быстрое создание новой фермы SharePoint 2013 или SharePoint 2016 в Azure."
services: virtual-machines-windows
documentationcenter: 
author: JoeDavies-MSFT
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 89b124da-019d-4179-86dd-ad418d05a4f2
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/30/2016
ms.author: josephd
translationtype: Human Translation
ms.sourcegitcommit: 0f8bc125855bc5a5b67fde5b0b742c73b5da7610
ms.openlocfilehash: 7c0746840dd45229dac491b03925349e6d9a44de


---
# <a name="create-sharepoint-server-farms"></a>Создание ферм серверов SharePoint

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="sharepoint-2013-farms"></a>Фермы SharePoint 2013
С помощью Marketplace на портале Microsoft Azure можно быстро создавать предварительно настроенные фермы SharePoint Server 2013. Такой подход может сэкономить много времени, когда в среде разработки и тестирования нужна базовая или высокодоступная ферма SharePoint или когда SharePoint Server 2013 рассматривается в качестве решения для совместной работы в рамках организации.

> [!NOTE]
> Пункт **Ферма серверов SharePoint** удален из Azure Marketplace на портале Azure. Он был заменен на **SharePoint 2013 non-HA Farm** (Ферма SharePoint 2013 без высокой доступности) и **SharePoint 2013 HA Farm** (Высокодоступная ферма SharePoint 2013).
>
>

Базовая ферма SharePoint в этой конфигурации состоит из трех виртуальных машин.

![sharepointfarm](./media/virtual-machines-windows-sharepoint-farm/Non-HAFarm.png)

Ферму такой конфигурации можно использовать в качестве упрощенной установки для разработки приложений SharePoint или первоначальной оценки SharePoint 2013.

Создание базовой фермы SharePoint (из трех серверов):

1. Щелкните [здесь](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-nonha/).
2. Щелкните **Развернуть**.
3. В области **SharePoint 2013 non-HA Farm** (Ферма SharePoint 2013 без высокой доступности) нажмите кнопку **Создать**.
4. В области **Create SharePoint 2013 non-HA Farm** (Создание фермы SharePoint 2013 без высокой доступности) укажите параметры и нажмите кнопку **Создать**.

Ферма SharePoint с высоким уровнем доступности в этой конфигурации состоит из девяти виртуальных машин.

![sharepointfarm](./media/virtual-machines-windows-sharepoint-farm/HAFarm.png)

Ферму такой конфигурации можно использовать для тестирования более высоких клиентских нагрузок, высокой доступности внешнего сайта SharePoint и групп доступности SQL Server AlwaysOn для фермы SharePoint. Кроме того, такая конфигурация подходит для разработки приложений SharePoint в высокодоступной среде.

Создание фермы высокой доступности SharePoint (9 серверов):

1. Щелкните [здесь](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-ha/).
2. Щелкните **Развернуть**.
3. В области **SharePoint 2013 HA Farm** (Высокодоступная ферма SharePoint 2013) нажмите кнопку **Создать**.
4. Укажите параметры в семи разделах в области **Create SharePoint 2013 HA Farm** (Создание высокодоступной фермы SharePoint 2013) и нажмите кнопку **Создать**.

> [!NOTE]
> Нельзя создать **ферму SharePoint 2013 без высокой доступности** или **высокодоступную ферму SharePoint 2013** с помощью бесплатной пробной версии Azure.
>
>

Портал Azure создает обе эти фермы только в облачной виртуальной сети с веб-присутствием. Подключение VPN типа "сеть-сеть" или ExpressRoute обратно к сети организации не создается.

> [!NOTE]
> При создании базовых или высокодоступных ферм SharePoint с помощью портала Azure невозможно указать существующую группу ресурсов. Чтобы обойти это ограничение, создайте фермы с помощью Azure PowerShell. Дополнительные сведения см. в разделе [Создание ферм разработки и тестирования SharePoint 2013 с помощью Azure PowerShell](https://technet.microsoft.com/library/mt743093.aspx#powershell).
>
>

## <a name="sharepoint-2016-farms"></a>Фермы SharePoint 2016
Инструкции по созданию описанной ниже фермы SharePoint Server 2016 с одним сервером см. в [этой статье](https://technet.microsoft.com/library/mt723354.aspx).

![sharepointfarm](./media/virtual-machines-windows-sharepoint-farm/SP2016Farm.png)

## <a name="managing-the-sharepoint-farms"></a>Управление фермами SharePoint
Для администрирования серверов этих ферм можно использовать подключения к удаленному рабочему столу. Дополнительные сведения см. в разделе [Подключение к виртуальной машине и вход](virtual-machines-windows-hero-tutorial.md#connect-to-the-virtual-machine-and-sign-on).

На сайте Центра администрирования SharePoint можно настраивать "Мои сайты", приложения SharePoint и другие функции. Дополнительные сведения см. в статье [Настройка SharePoint 2013](http://technet.microsoft.com/library/ee836142.aspx).

## <a name="next-steps"></a>Дальнейшие действия
* Ознакомьтесь с дополнительными [конфигурациями SharePoint](https://technet.microsoft.com/library/dn635309.aspx) в службах инфраструктуры Azure.



<!--HONumber=Nov16_HO3-->


