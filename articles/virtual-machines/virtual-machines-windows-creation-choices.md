<properties
    pageTitle="Разные способы создания виртуальной машины Linux | Microsoft Azure"
    description="Содержит перечисление различных способов создания виртуальной машины Windows с помощью диспетчера ресурсов."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows"
    ms.workload="infrastructure-services"
    ms.date="09/27/2016"
    ms.author="cynthn"/>


# <a name="different-ways-to-create-a-windows-virtual-machine-with-resource-manager"></a>Различные способы создания виртуальной машины Windows с помощью диспетчера ресурсов

Поскольку виртуальные машины подходят для различных пользователей и целей, в Azure их можно создавать несколькими способами. Это означает, что вам потребуется выбрать виртуальную машину и способ ее создания. В настоящей статье рассматриваются эти варианты и приводятся ссылки на инструкции.

## <a name="azure-portal"></a>Портал Azure

Портал Azure — это простой способ опробовать виртуальную машину, особенно если вы только начинаете работать с Azure. 

[Создание виртуальной машины под управлением Windows на портале](virtual-machines-windows-hero-tutorial.md)

## <a name="template"></a>Шаблон

Для виртуальных машин требуется сочетание ресурсов (таких как группы доступности и учетные записи хранения). Вместо развертывания и управления каждым ресурсом по отдельности можно создать шаблон Azure Resource Manager, который развертывает и подготавливает все ресурсы в ходе одной скоординированной операции.

- [Создание виртуальной машины Windows с использованием шаблона диспетчера ресурсов](virtual-machines-windows-ps-template.md)


## <a name="azure-powershell"></a>Azure PowerShell

Если вы предпочитаете работать в командной строке, можно использовать Azure PowerShell.

- [Создание виртуальной машины Windows с помощью PowerShell](virtual-machines-windows-ps-create.md)


## <a name="visual-studio"></a>Visual Studio

Используйте Visual Studio для создания и развертывания виртуальных машин, а также для управления ими, с помощью инструментов Azure для Visual Studio и пакета SDK Azure.

[Инструменты Azure для Visual Studio](https://www.visualstudio.com/features/azure-tools-vs)




<!--HONumber=Oct16_HO2-->


