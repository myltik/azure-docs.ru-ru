<properties
   pageTitle="Как добавить тег к виртуальной машине | Microsoft Azure"
   description="Узнайте, как добавлять теги к виртуальной машине Azure, созданной с помощью модели развертывания диспетчера ресурсов."
   services="virtual-machines"
   documentationCenter=""
   authors="mmccrory"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="infrastructure-services"
   ms.date="07/23/2015"
   ms.author="dkshir;memccror"/>

# Пометка виртуальной машины в Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Классическая модель развертывания.


В этой статье описываются различные способы маркировки виртуальной машины в Azure с помощью диспетчера ресурсов Azure. Теги — это определяемые пользователем пары "ключ-значение", которые можно помещать непосредственно в ресурс или группу ресурсов. В настоящий момент Azure поддерживает до 15 тегов на ресурс или группу ресурсов. Теги можно добавлять к ресурсу во время его создания или к уже существующему ресурсу. Обратите внимание, что теги поддерживаются только для тех ресурсов, которые созданы с помощью диспетчера ресурсов Azure.

## Пометка виртуальной машины с помощью шаблонов

Для начала рассмотрим пометку с помощью шаблонов. [Этот шаблон](https://github.com/Azure/azure-quickstart-templates/tree/master/101-tags-vm) добавляет теги к следующим ресурсам: Compute (виртуальная машина), Storage (учетная запись хранения) и Network (общедоступный IP-адрес, виртуальная сеть и сетевой интерфейс).

Нажмите кнопку **Развернуть в Azure**, после того как перейдете по [ссылке на шаблон](https://github.com/Azure/azure-quickstart-templates/tree/master/101-tags-vm). Откроется [портал предварительной версии Azure](http://portal.azure.com/), где можно развернуть шаблон.

![Простое развертывание с тегами](./media/virtual-machines-tagging-arm/deploy-to-azure-tags.png)

Этот шаблон включает следующие теги: *Отдел*, *Приложение* и *Создано*. Вы можете добавлять и редактировать эти теги непосредственно в шаблоне, если вам нужно изменить их имена.

![Теги Azure в шаблоне](./media/virtual-machines-tagging-arm/azure-tags-in-a-template.png)

Как видите, теги определяются как пары "ключ-значение", разделенные двоеточием (:). Теги должны быть определены в следующем формате:

        “tags”: {
            “Key1” : ”Value1”,
            “Key2” : “Value2”
        }

Завершив добавление нужных тегов в файл шаблона, сохраните его.

Затем вы можете указать значения для тегов в разделе **Редактирование параметров**.

![Редактирование тегов на портале Azure](./media/virtual-machines-tagging-arm/edit-tags-in-azure-portal.png)

Нажмите **Создать**, чтобы развернуть шаблон со значениями тегов.


## Пометка с помощью портала

После создания ресурсов с тегами вы можете просматривать, добавлять и удалять теги на портале.

Выберите значок тегов, чтобы просмотреть свои теги:

![Значок тегов на портале Azure](./media/virtual-machines-tagging-arm/azure-portal-tags-icon.png)

Добавьте новый тег с помощью портала, определив собственную пару "ключ-значение", и сохраните его.

![Добавление тега на портале Azure](./media/virtual-machines-tagging-arm/azure-portal-add-new-tag.png)

Новый тег должен появиться в списке тегов для ресурса.

![Новый тег, сохраненный на портале Azure](./media/virtual-machines-tagging-arm/azure-portal-saved-new-tag.png)


## Маркировка с помощью PowerShell

Чтобы создавать, добавлять и удалять теги с помощью PowerShell, необходимо сначала настроить [среду PowerShell с диспетчером ресурсов Azure][]. Завершив настройку, вы можете добавлять теги к ресурсам вычисления, сети и хранения во время создания или после него с помощью PowerShell. Эта статья в основном посвящена просмотру и редактированию тегов виртуальных машин.

Для начала перейдите к виртуальной машине с помощью командлета `Get-AzureVM`.

        PS C:\> Get-AzureVM -ResourceGroupName "MyResourceGroup" -Name "MyWindowsVM"

Если виртуальная машина уже содержит теги, вы сможете просмотреть все теги на ресурсе:

        Tags : {
                "Application": "MyApp1",
                "Created By": "MyName",
                "Department": "MyDepartment",
                "Environment": "Production"
               }

Для добавления тегов с помощью PowerShell можно использовать команду `Set-AzureResource`. Обратите внимание, что при изменении тегов с помощью PowerShell обновляются все теги. Поэтому, если вы добавляете один тег к ресурсу, который уже содержит теги, вам потребуется включить все теги, которые нужно добавить к ресурсу. Ниже представлен пример добавления тегов к ресурсу с помощью командлетов PowerShell.

Первый командлет задает для всех тегов в виртуальной машине *MyWindowsVM* переменную *tags* с помощью функций `Get-AzureResource` и `Tags`.

        PS C:\> $tags = (Get-AzureResource -Name MyWindowsVM -ResourceGroupName MyResourceGroup -ResourceType "Microsoft.Compute/virtualmachines" -ApiVersion 2015-05-01-preview).Tags

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

Четвертая команда устанавливает все теги, определенные в переменной *tags*, на данном ресурсе. В данном случае это MyWindowsVM.

        PS C:\> Set-AzureResource -Name MyWindowsVM -ResourceGroupName MyResourceGroup -ResourceType "Microsoft.Compute/VirtualMachines" -ApiVersion 2015-05-01-preview -Tag $tags

Пятая команда выводит на экран все теги на ресурсе. Как видите, теперь *Расположение* определяется как тег со значением *MyLocation*.

        PS C:\> (Get-AzureResource -ResourceName "MyWindowsVM" -ResourceGroupName "MyResourceGroup" -ResourceType "Microsoft.Compute/VirtualMachines" -ApiVersion 2015-05-01-preview).Tags

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


## Отметка тегами с помощью интерфейса командной строки Azure

Теги также поддерживаются для ресурсов, которые уже созданы с помощью интерфейса командной строки Azure. Для начала настройте [среду интерфейса командной строки Azure][]. Войдите в свою подписку с помощью интерфейса командной строки Azure и переключитесь в режим ARM. Вы можете просмотреть все свойства определенной виртуальной машины, включая теги, с помощью следующей команды:

        azure vm show -g MyResourceGroup -n MyVM

В отличие от PowerShell, при добавлении тегов к ресурсу, который уже содержит теги, не требуется указывать все теги (старые и новые), прежде чем использовать команду `azure vm set`. Эта команда позволяет присоединить тег к ресурсу. Чтобы добавить новый тег виртуальной машины через интерфейс командной строки Azure, можно использовать команду `azure vm set` с параметром тега **-t**:

        azure vm set -g MyResourceGroup -n MyVM –t myNewTagName1=myNewTagValue1;myNewTagName2=myNewTagValue2

Чтобы удалить все теги, можно использовать параметр **–T** в команде `azure vm set`.

        azure vm set – g MyResourceGroup –n MyVM -T


Теперь, когда мы применили теги к ресурсам с помощью PowerShell, интерфейса командной строки Azure и портала, рассмотрим сведения об использовании, чтобы увидеть теги на портале выставления счетов.


## Просмотр тегов в сведениях об использовании

Теги, добавленные к ресурсам вычисления, сети и хранения с помощью диспетчера ресурсов Azure, будут указаны в сведениях об использовании на портале выставления счетов.

Нажмите **Скачать сведения об использовании**, чтобы просмотреть сведения об использовании подписки.

![Сведения об использовании на портале Azure](./media/virtual-machines-tagging-arm/azure-portal-tags-usage-details.png)

Выберите платежный документ и **версию 2** сведений об использовании:

![Сведения об использовании предварительной версии 2 на портале Azure](./media/virtual-machines-tagging-arm/azure-portal-version2-usage-details.png)

В сведениях об использовании все теги отображаются в столбце **Теги**:

![Столбец "Теги" на портале Azure](./media/virtual-machines-tagging-arm/azure-portal-tags-column.png)

Проанализировав эти теги и сведения об использовании, организации смогут узнать больше из данных о потреблении.


## Дополнительные ресурсы

* [Общие сведения о диспетчере ресурсов Azure][]
* [Использование тегов для упорядочивания ресурсов Azure][]
* [Общие сведения о счете Azure][]
* [Получение ценных сведений о потреблении ресурсов Microsoft Azure][]




[среду PowerShell с диспетчером ресурсов Azure]: ../powershell-azure-resource-manager.md
[Командлеты ресурсов Azure]: https://msdn.microsoft.com/ru-RU/library/azure/dn757692.aspx
[среду интерфейса командной строки Azure]: ./xplat-cli-azure-resource-manager.md
[Общие сведения о диспетчере ресурсов Azure]: ../resource-group-overview.md
[Использование тегов для упорядочивания ресурсов Azure]: ../resource-group-using-tags.md
[Общие сведения о счете Azure]: ../billing-understand-your-bill.md
[Получение ценных сведений о потреблении ресурсов Microsoft Azure]: ../billing-usage-rate-card-overview.md

<!---HONumber=Oct15_HO3-->