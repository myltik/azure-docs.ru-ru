<properties
   pageTitle="Как добавить тег к виртуальной машине | Microsoft Azure"
   description="Узнайте, как добавлять теги к виртуальной машине Windows, созданной в Azure с помощью модели развертывания на основе диспетчера ресурсов."
   services="virtual-machines-windows"
   documentationCenter=""
   authors="mmccrory"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="07/05/2016"
   ms.author="memccror"/>

# Пометка виртуальной машины Windows в Azure


В этой статье описываются разные способы назначения тегов виртуальной машине Windows в Azure с использованием модели развертывания Resource Manager. Теги — это определяемые пользователем пары "ключ-значение", которые можно помещать непосредственно в ресурс или группу ресурсов. В настоящий момент Azure поддерживает до 15 тегов на ресурс или группу ресурсов. Теги можно добавлять к ресурсу во время его создания или к уже существующему ресурсу. Обратите внимание, что теги поддерживаются только для тех ресурсов, которые созданы с помощью модели развертывания Resource Manager. Чтобы назначить тег виртуальной машине Linux, см. статью [Пометка виртуальной машины Linux в Azure](virtual-machines-linux-tag.md).

[AZURE.INCLUDE [virtual-machines-common-tag](../../includes/virtual-machines-common-tag.md)]

## Маркировка с помощью PowerShell

Чтобы создавать, добавлять и удалять теги с помощью PowerShell, необходимо сначала настроить [среду PowerShell с диспетчером ресурсов Azure][]. Завершив настройку, вы можете добавлять теги к ресурсам вычисления, сети и хранения во время создания или после него с помощью PowerShell. Эта статья в основном посвящена просмотру и редактированию тегов виртуальных машин.

Для начала перейдите к виртуальной машине с помощью командлета `Get-AzureRmVM`.

        PS C:\> Get-AzureRmVM -ResourceGroupName "MyResourceGroup" -Name "MyTestVM"

Если виртуальная машина уже содержит теги, вы сможете просмотреть все теги на ресурсе:

        Tags : {
                "Application": "MyApp1",
                "Created By": "MyName",
                "Department": "MyDepartment",
                "Environment": "Production"
               }

Для добавления тегов с помощью PowerShell можно использовать команду `Set-AzureRmResource`. Обратите внимание, что при изменении тегов с помощью PowerShell обновляются все теги. Поэтому, если вы добавляете один тег к ресурсу, который уже содержит теги, вам потребуется включить все теги, которые нужно добавить к ресурсу. Ниже представлен пример добавления тегов к ресурсу с помощью командлетов PowerShell.

Первый командлет определяет переменную *tags* для всех тегов в виртуальной машине *MyTestVM* с помощью функций `Get-AzureRmResource` и `Tags`.

        PS C:\> $tags = (Get-AzureRmResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

Вторая команда выводит на экран теги для данной переменной.

        PS C:\> $tags

        Name		Value
        ----                           -----
        Value		MyDepartment
        Name		Department
        Value		MyApp1
        Name		Application
        Value		MyName
        Name		Created By
        Value		Production
        Name		Environment

Третья команда добавляет тег к переменной *tags*. Обратите внимание, что оператор **+=** используется для присоединения пары "ключ-значение" к списку *tags*.

        PS C:\> $tags +=@{Name="Location";Value="MyLocation"}

Четвертая команда устанавливает все теги, определенные в переменной *tags*, на данном ресурсе. В нашем примере это MyTestVM.

        PS C:\> Set-AzureRmResource -ResourceGroupName MyResourceGroup -Name MyTestVM -ResourceType "Microsoft.Compute/VirtualMachines" -Tag $tags

Пятая команда выводит на экран все теги на ресурсе. Как видите, теперь *Расположение* определяется как тег со значением *MyLocation*.

        PS C:\> (Get-AzureRmResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

        Name		Value
        ----                           -----
        Value		MyDepartment
        Name		Department
        Value		MyApp1
        Name		Application
        Value		MyName
        Name		Created By
        Value		Production
        Name		Environment
        Value		MyLocation
        Name		Location

Дополнительные сведения об отметке тегами с помощью PowerShell см. в статье [Командлеты ресурсов Azure][].

[AZURE.INCLUDE [virtual-machines-common-tag-usage](../../includes/virtual-machines-common-tag-usage.md)]

## Дальнейшие действия

* Дополнительные сведения о добавлении тегов для ресурсов Azure см. в статьях [Общие сведения об Azure Resource Manager][] и [Использование тегов для организации ресурсов в Azure][].
* Сведения о том, как теги могут помочь в управлении использованием ресурсов Azure, см. в статьях [Расшифровка счета за использование Microsoft Azure][] и [Получение ценных сведений о потреблении ресурсов Microsoft Azure][].

[среду PowerShell с диспетчером ресурсов Azure]: ../powershell-azure-resource-manager.md
[Командлеты ресурсов Azure]: https://msdn.microsoft.com/library/azure/dn757692.aspx
[Общие сведения об Azure Resource Manager]: ../resource-group-overview.md
[Использование тегов для организации ресурсов в Azure]: ../resource-group-using-tags.md
[Расшифровка счета за использование Microsoft Azure]: ../billing-understand-your-bill.md
[Получение ценных сведений о потреблении ресурсов Microsoft Azure]: ../billing-usage-rate-card-overview.md

<!---HONumber=AcomDC_0706_2016-->