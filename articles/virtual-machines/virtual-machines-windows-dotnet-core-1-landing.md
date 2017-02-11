---
title: "Руководство 1. .NET Core для виртуальных машин Azure | Документация Майкрософт"
description: "Руководство по .NET Core для виртуальных машин Azure"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 14d5f250-1f76-49d4-898f-07b58fd39e7c
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 11/21/2016
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: cc534bbf4eb282fe3b675f1783e05e2d3357405e


---
# <a name="automating-application-deployments-to-azure-virtual-machines"></a>Автоматизация развертывания приложений в виртуальных машинах Azure
Эта серия из четырех статей подробно описывает развертывание и настройку ресурсов и приложений Azure с помощью шаблонов Azure Resource Manager. В этой серии мы развернем пример шаблона и рассмотрим шаблон развертывания. Цель этой серии — рассказать о взаимосвязях между ресурсами Azure и показать практические примеры развертывания полностью интегрированных шаблонов Azure Resource Manager. Предполагается, что у читателя есть базовый уровень знаний об Azure Resource Manager. Прежде чем работать с этим руководством, предлагаем вам ознакомиться с основными понятиями Azure Resource Manager.

## <a name="music-store-application"></a>Приложение музыкального магазина
В этой серии используется пример приложения .NET Core, которое моделирует работу музыкального магазина. Это приложение можно развернуть в виртуальную систему Windows или Linux. Мы предоставим примеры развертывания для обеих систем. Приложение состоит из веб-приложения и базы данных SQL. Прежде чем переходить к статьям этой серии, разверните приложение с помощью кнопки развертывания, представленной на этой странице. Архитектура полностью развернутого приложения и взаимодействующих с ним структур Azure изображена на следующей схеме. 

Шаблон Resource Manager для музыкального магазина Windows вы найдете [здесь](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows)

![Приложение музыкального магазина](./media/virtual-machines-windows-dotnet-core/music-store.png)

В следующих четырех статьях подробно рассматривается каждый из этих компонентов и соответствующие объекты JSON из шаблона.

* [**Архитектура приложения**](virtual-machines-windows-dotnet-core-2-architecture.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) — компоненты приложения, такие как веб-сайты и базы данных, должны размещаться на вычислительных ресурсах Azure, например на виртуальных машинах и в базах данных Azure SQL. В этом документе рассматриваются процессы сопоставления вычислительных потребностей с ресурсами Azure и развертывания ресурсов с помощью шаблона Azure Resource Manager. 
* [**Доступ и безопасность**](virtual-machines-windows-dotnet-core-3-access-security.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) — при размещении приложения в Azure важно продумать, как выполняется доступ к приложению и как различные компоненты приложения взаимодействуют друг с другом. В этом документе объясняется, как предоставить и защитить доступ к приложению из Интернета и доступ компонентов приложения друг к другу.
* [**Доступность и масштабирование**](virtual-machines-windows-dotnet-core-4-availability-scale.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) — здесь обсуждается способность приложения сохранять работоспособность при отключении инфраструктурных ресурсов и возможность изменять количество доступных вычислительных ресурсов в соответствии с потребностями приложения. Этот документ описывает компоненты, необходимые для развертывания приложения с балансировкой нагрузки и высоким уровнем доступности.
* [**Развертывание приложения**](virtual-machines-windows-dotnet-core-5-app-deployment.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) — при развертывании приложений на виртуальных машинах Azure следует обдумать, как двоичные файлы приложения будут устанавливаться на виртуальную машину. В этом документе описаны возможности по автоматизации установки приложений, которые предоставляют расширения пользовательских сценариев для виртуальных машин Azure.

Шаблоны Azure Resource Manager разработаны для автоматизации развертывания для инфраструктуры Azure, а также установки и настройки любого приложения, размещенного в этой инфраструктуре Azure. Ознакомившись с этими статьями, вы научитесь решать эти задачи.

## <a name="deploy-the-music-store-application"></a>Развертывание приложения музыкального магазина
Чтобы развернуть музыкальный магазин, нажмите эту кнопку.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fdotnet-core-sample-templates%2Fmaster%2Fdotnet-core-music-windows%2Fazuredeploy.json" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/>
</a>

В шаблоне Azure Resource Manager должны присутствовать несколько обязательных параметров и значений.

| Имя параметра | Описание |
| --- | --- |
| ADMINUSERNAME |Имя пользователя администратора, которое будет использоваться для виртуальной машины и базы данных SQL Azure. |
| ADMINPASSWORD |Пароль, который будет использоваться для виртуальной машины и базы данных SQL Azure. |
| NUMBEROFINSTANCES |Количество создаваемых виртуальных машин. На каждой из этих виртуальных машин будет размещено веб-приложение музыкального магазина, и трафик будет равномерно распределяться между ними. |
| PUBLICIPADDRESSDNSNAME |Глобально уникальное DNS-имя, связанное с общедоступным IP-адресом приложения. |

Когда развертывание шаблона завершится, попробуйте открыть общедоступный IP-адрес в любом интернет-браузере. Вы должны увидеть сайт музыкального магазина .NET.

## <a name="next-steps"></a>Дальнейшие действия
<hr>

[Шаг 1. Архитектура приложений с использованием шаблонов Azure Resource Manager](virtual-machines-windows-dotnet-core-2-architecture.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Шаг 2. Доступ и безопасность в шаблонах Azure Resource Manager](virtual-machines-windows-dotnet-core-3-access-security.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Шаг 3. Доступность и масштабирование в шаблонах Azure Resource Manager](virtual-machines-windows-dotnet-core-4-availability-scale.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Шаг 4. Развертывание приложений с использованием шаблонов Azure Resource Manager](virtual-machines-windows-dotnet-core-5-app-deployment.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)




<!--HONumber=Nov16_HO3-->


