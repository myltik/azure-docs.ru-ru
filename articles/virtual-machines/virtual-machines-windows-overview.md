---
title: "Обзор виртуальных машин Windows | Документация Майкрософт"
description: "Узнайте о создании виртуальных машинах Windows в Azure и управлении ими."
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: fbae9c8e-2341-4ed0-bb20-fd4debb2f9ca
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/01/2017
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: 9841096da2d16e35878a1c0013a33e9f051e5932
ms.openlocfilehash: 37a966d15caba073dcfda77d4d0aaf32be0199ac
ms.lasthandoff: 03/02/2017


---
# <a name="overview-of-windows-virtual-machines-in-azure"></a>Обзор виртуальных машин Windows в Azure
Виртуальные машины Azure — один из нескольких типов [запрашиваемых масштабируемых вычислительных ресурсов](../app-service-web/choose-web-site-cloud-service-vm.md), которые предоставляет Azure. Обычно виртуальную машину выбирают, когда требуется более строгий контроль за вычислительной средой, чем в других вариантах. В этой статье содержатся сведения о том, что следует учитывать перед созданием виртуальной машины, а также инструкции по созданию виртуальной машины и управлению ею.

Виртуальная машина Azure предоставляет гибкие возможности виртуализации без необходимости приобретать и обслуживать физическое оборудование, на котором она выполняется. Однако вам по-прежнему приходится обслуживать виртуальную машину, выполняя разные задачи — настройка, установка исправлений и программного обеспечения, работающего на виртуальной машине.

Виртуальные машины Azure можно использовать разными способами. Ниже приведены некоторые примеры.

* **Разработка и тестирование.** Виртуальные машины Azure обеспечивают быстрый и простой способ создания компьютера с определенными конфигурациями, необходимыми для написания кода и тестирования приложения.
* **Приложения в облаке.** Так как уровень спроса на приложение может меняться, с экономической точки зрения разумно запускать его на виртуальной машине в Azure. Вы платите за дополнительные виртуальные машины, если они вам нужны, и отключаете их, если они не нужны.
* **Расширенный центр обработки данных.** Виртуальные машины в виртуальной сети Azure можно легко подключить к корпоративной сети.

Вы можете увеличить масштаб виртуальных машин, используемых приложением, а также развернуть дополнительные виртуальные машины в соответствии с требованиями.

## <a name="what-do-i-need-to-think-about-before-creating-a-vm"></a>О чем следует подумать перед созданием виртуальной машины?
При создании инфраструктуры приложения в Azure всегда нужно учитывать множество [рекомендаций по проектированию](virtual-machines-windows-infrastructure-virtual-machine-guidelines.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Перед началом работы следует подумать о следующих аспектах для виртуальной машины:

* имена ресурсов приложения;
* расположение, в котором хранятся ресурсы;
* размер виртуальной машины;
* максимальное число виртуальных машин, которые можно создать;
* операционная система, под управлением которой будет работать виртуальная машина;
* конфигурация виртуальной машины после ее запуска;
* связанные ресурсы, необходимые для виртуальной машины.

### <a name="naming"></a>Именование
Виртуальной машине назначается [имя](virtual-machines-windows-infrastructure-naming-guidelines.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Кроме того, для нее настраивается имя компьютера как часть операционной системы. Имя виртуальной машины может содержать не более 15 символов.

Если диск операционной системы создается с помощью Azure, имя компьютера и имя виртуальной машины совпадают. При [передаче и использовании собственного образа](virtual-machines-windows-upload-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), содержащего ранее настроенную операционную систему, а также создании виртуальной машины с его помощью имена могут различаться. При передаче файла собственного образа мы советуем указать одинаковые имя компьютера в операционной системе и имя виртуальной машины.

### <a name="locations"></a>Расположения
Все ресурсы, созданные в Azure, распределяются по нескольким [географическим регионам](https://azure.microsoft.com/regions/) во всем мире. Как правило, при создании виртуальной машины регион называется **расположением**. Для виртуальной машины расположение указывает на место хранения виртуальных жестких дисков.

В этой таблице приведены некоторые способы, с помощью которых можно получить список доступных расположений.

| Метод | Описание |
| --- | --- |
| Портал Azure |Выберите расположение из списка при создании виртуальной машины. |
| Azure PowerShell |Используйте команду [Get-AzureRmLocation](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.5.0/get-azurermlocation). |
| Интерфейс REST API |Используйте операцию [вывода списка расположений](https://docs.microsoft.com/rest/api/resources/subscriptions#Subscriptions_ListLocations). |

### <a name="vm-size"></a>Размер виртуальной машины
Используемый [размер](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) виртуальной машины зависит от рабочей нагрузки, которую требуется выполнить. Позже выбранный размер определяет разные факторы, например вычислительную мощность, объем памяти и хранилища. Azure предлагает широкий спектр размеров для поддержки разных вариантов использования.

Azure взимает [почасовую оплату](https://azure.microsoft.com/pricing/details/virtual-machines/windows/), исходя из размера и операционной системы виртуальной машины. При частичном использовании Azure взимает плату только за использованные минуты. Плата за использование хранилища взимается отдельно.

### <a name="vm-limits"></a>Ограничения виртуальной машины
Для подписки Azure предусмотрена [квота](../azure-subscription-service-limits.md) по умолчанию, от которой зависит возможность развертывания большого количества виртуальных машин для проекта. Текущее ограничение для каждой подписки составляет 20 виртуальных машин на регион. Чтобы увеличить квоту, следует отправить соответствующий запрос в службу поддержки.

### <a name="operating-system-disks-and-images"></a>Диски и образы операционной системы
Для хранения операционной системы и данных виртуальные машины используют [виртуальные жесткие диски](../storage/storage-about-disks-and-vhds-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Они также используются для образов, которые доступны для установки операционной системы. 

Azure предоставляет множество [образов из Marketplace](https://azure.microsoft.com/marketplace/virtual-machines/) для различных версий и типов операционных систем Windows Server. Образы из Marketplace определяются по издателю, предложению, SKU и версии (обычно указывается последняя версия). 

В этой таблице указано, как можно найти сведения об образе.

| Метод | Описание |
| --- | --- |
| Портал Azure |При выборе используемого образа значения задаются автоматически. |
| Azure PowerShell |[Get-AzureRMVMImagePublisher](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.5.0/get-azurermvmimagepublisher). Параметр -Location указывает расположение.<BR>[Get-AzureRMVMImageOffer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.5.0/get-azurermvmimageoffer). Параметр -Location указывает расположение, -PublisherName — издателя.<BR>[Get-AzureRMVMImageSku](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.5.0/get-azurermvmimagesku). Параметр -Location указывает расположение, -PublisherName — издателя, а -Offer — предложение. |
| Интерфейсы API REST |[Получение списка издателей образов](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publishers)<BR>[Получение списка предложений для образа](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publisher-offers)<BR>[Получение списка SKU для образа](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publisher-offer-skus) |

Вы можете [передать и использовать собственный образ](virtual-machines-windows-upload-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). В этом случае имя издателя, предложение и SKU не используются.

### <a name="extensions"></a>расширения.
[Расширения](virtual-machines-windows-extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) виртуальных машин предоставляют дополнительные возможности за счет настройки после развертывания и автоматизированных задач.

С помощью расширений можно выполнить такие стандартные задачи:

* **Выполнение пользовательских скриптов.** [Расширение пользовательских скриптов](virtual-machines-windows-extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) помогает настроить рабочие нагрузки на виртуальной машине, выполняя скрипт при подготовке виртуальной машины.
* **Развертывание конфигураций и управление ими.** [Расширение настройки требуемого состояния (DSC) PowerShell](virtual-machines-windows-extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) позволяет настроить DSC на виртуальной машине для управления конфигурациями и средами.
* **Сбор данных диагностики.** [Расширение системы диагностики Azure](virtual-machines-windows-extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) позволяет настроить виртуальную машину для сбора данных диагностики, которые можно использовать для мониторинга состояния работоспособности приложения.

### <a name="related-resources"></a>Связанные ресурсы
Ресурсы в этой таблице используются в виртуальной машине. Они должны существовать или создаваться вместе с виртуальной машиной.

| Ресурс | Обязательно | Description (Описание) |
| --- | --- | --- |
| [Группа ресурсов](../azure-resource-manager/resource-group-overview.md) |Да |Виртуальная машина должна входить в группу ресурсов. |
| [Учетная запись хранения](../storage/storage-create-storage-account.md) |Да |Виртуальной машине требуется учетная запись хранения для хранения виртуальных жестких дисков. |
| [Виртуальная сеть](../virtual-network/virtual-networks-overview.md) |Да |Виртуальная машина должна быть подключена к виртуальной сети. |
| [Общедоступный IP-адрес](../virtual-network/virtual-network-ip-addresses-overview-arm.md) |Нет |Для удаленного доступа к виртуальной машине ей можно назначить общедоступный IP-адрес. |
| [Сетевой интерфейс](../virtual-network/virtual-network-network-interface-overview.md) |Да |Для обмена данными в сети виртуальной машине нужен сетевой интерфейс. |
| [Диски данных](virtual-machines-windows-attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |Нет |Виртуальная машина может содержать диски данных для расширения объема ресурсов хранения. |

## <a name="how-do-i-create-my-first-vm"></a>Как создать первую виртуальную машину?
Существует несколько способов создания виртуальной машины. Выбор зависит от используемой среды. 

Эта таблица содержит сведения, которые помогут приступить к созданию виртуальной машины.

| Метод | Статья |
| --- | --- |
| Портал Azure |[Создание виртуальной машины под управлением Windows на портале](virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| Шаблоны |[Создание виртуальной машины Windows с использованием шаблона диспетчера ресурсов](virtual-machines-windows-ps-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| Azure PowerShell |[Создание виртуальной машины Windows с помощью PowerShell](virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| Клиентские пакеты SDK |[Развертывание ресурсов Azure с помощью языка C#](virtual-machines-windows-csharp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| Интерфейсы API REST |[Создание или обновление виртуальной машины](https://docs.microsoft.com/rest/api/compute/virtualmachines/virtualmachines-create-or-update) |

Так или иначе, иногда вы будете сталкиваться с проблемами. В этом случае см. сведения в статье [Устранение неполадок в развертывании Resource Manager при создании виртуальной машины Windows в Azure](virtual-machines-windows-troubleshoot-deployment-new-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="how-do-i-manage-the-vm-that-i-created"></a>Как управлять созданной виртуальной машиной?
Управление виртуальными машинами осуществляется с помощью браузерного портала, программ командной строки с поддержкой создания скриптов или напрямую с помощью интерфейсов API. Некоторые стандартные задачи администрирования включают в себя получение сведений о виртуальной машине, вход в виртуальную машину, управление доступностью и архивацию.

### <a name="get-information-about-a-vm"></a>Получение информации о виртуальной машине
В этой таблице представлены некоторые из способов, которыми можно получить сведения о виртуальной машине.

| Метод | Описание |
| --- | --- |
| Портал Azure |В меню концентратора щелкните **Виртуальные машины** и выберите виртуальную машину из списка. В колонке виртуальной машины доступны общие сведения, значения параметров и метрики мониторинга. |
| Azure PowerShell |Сведения об управлении виртуальными машинами с помощью PowerShell см. в статье [Управление виртуальными машинами Azure с помощью диспетчера ресурсов и PowerShell](virtual-machines-windows-ps-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). |
| Интерфейс REST API |Используйте операцию [получения сведений о виртуальной машине](https://docs.microsoft.com/rest/api/compute/virtualmachines/virtualmachines-get) для выполнения соответствующего действия. |
| Клиентские пакеты SDK |Сведения об управлении виртуальными машинами с помощью C# см. в статье [Управление виртуальными машинами Azure с помощью Azure Resource Manager и языка C#](virtual-machines-windows-csharp-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). |

### <a name="log-on-to-the-vm"></a>Вход в виртуальную машину
Чтобы [запустить сеанс удаленного рабочего стола](virtual-machines-windows-connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), на портале Azure нажмите кнопку "Подключиться". Иногда при попытке использования удаленного подключения может произойти сбой. В этом случае см. справочные сведения в статье [Устранение неполадок с подключением к удаленному рабочему столу на виртуальной машине Azure под управлением Windows](virtual-machines-windows-troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="manage-availability"></a>Управление доступностью
Важно понимать, как [обеспечить высокий уровень доступности](virtual-machines-windows-manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) для приложения. Эта конфигурация требует создания нескольких виртуальных машин, чтобы гарантировать работу хотя бы одной из них.

Чтобы обеспечить соответствие соглашению об уровне обслуживания на 99,95 %, необходимо развернуть две или больше виртуальные машины для выполнения рабочих нагрузок в рамках [группы доступности](virtual-machines-windows-infrastructure-availability-sets-guidelines.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Такая конфигурация гарантирует, что виртуальные машины распределяются по нескольким доменам сбоя, а также развертываются на узлах с разными периодами обслуживания. В полном [соглашении об уровне обслуживания Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_0/) поясняется гарантированная доступность Azure в целом.

### <a name="back-up-the-vm"></a>Архивация виртуальной машины
[Хранилище служб восстановления](../backup/backup-introduction-to-azure-backup.md) используется для защиты данных и ресурсов в службе архивации Azure и службах Azure Site Recovery. Хранилище служб восстановления позволяет [развертывать резервные копии виртуальных машин, развернутых с помощью Resource Manager, и управлять ими с использованием PowerShell](../backup/backup-azure-vms-automation.md). 

## <a name="next-steps"></a>Дальнейшие действия
* Если планируется работать с виртуальными машинами Linux, см. сведения в статье [Azure и Linux](virtual-machines-linux-azure-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Дополнительные рекомендации по настройке инфраструктуры см. в статье [Пошаговое руководство по примеру инфраструктуры Azure](virtual-machines-windows-infrastructure-example.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Обязательно следуйте рекомендациям из статьи [Рекомендации по запуску виртуальных машин Windows в Azure](virtual-machines-windows-guidance-compute-single-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


