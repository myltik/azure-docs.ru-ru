---
title: Важные особенности виртуальных машин в Azure Stack и рекомендации по работе с ними | Документация Майкрософт
description: Сведения об особенностях виртуальных машин в Azure Stack и рекомендации по работе с ними.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 6613946D-114C-441A-9F74-38E35DF0A7D7
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: brenduns
ms.openlocfilehash: 83a0b8ff040425ac30cff96936f2f639fd1b5643
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2018
ms.locfileid: "34076168"
---
# <a name="considerations-for-using-virtual-machines-in-azure-stack"></a>Рекомендации по использованию виртуальных машин в Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

Виртуальные машины Azure Stack — это масштабируемые вычислительные ресурсы, предоставляемые по запросу. Перед развертыванием виртуальных машин необходимо понять различия между функциями виртуальных машин, доступных в Azure Stack и Microsoft Azure. В этой статье рассмотрены эти различия и представлены основные рекомендации по планированию развертывания виртуальных машин. Чтобы узнать об общих различиях между Azure Stack и Azure, прочитайте статью [Важные аспекты использования служб и создания приложений в Azure Stack](azure-stack-considerations.md).

## <a name="cheat-sheet-virtual-machine-differences"></a>Памятка: различия виртуальных машин

| Функция | Azure (глобальная) | Azure Stack |
| --- | --- | --- |
| Образы виртуальных машин | Azure Marketplace содержит образы, на основе которых можно создать виртуальную машину. На странице [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/compute?subcategories=virtual-machine-images&page=1) представлен список образов, доступных в Azure Marketplace. | По умолчанию Azure Stack Marketplace не содержит образы. Чтобы пользователи могли использовать образы, администратор облака Azure Stack должен опубликовать или загрузить их в Azure Stack Marketplace. |
| Размер виртуальных машин | Azure поддерживает широкий ассортимент размеров виртуальных машин. Дополнительные сведения о доступных размерах и параметрах см. в статьях [Размеры виртуальных машин Windows в Azure](../../virtual-machines/virtual-machines-windows-sizes.md) и [Размеры виртуальных машин Linux в Azure](../../virtual-machines/linux/sizes.md). | Azure Stack поддерживает подмножество размеров виртуальных машин, доступных в Azure. Чтобы просмотреть список поддерживаемых размеров, изучите раздел [Размеры виртуальных машин](#virtual-machine-sizes) в этой статье. |
| Квоты для виртуальных машин | [Ограничения квот](../../azure-subscription-service-limits.md#service-specific-limits) устанавливаются корпорацией Майкрософт. | Администратор облака Azure Stack должен самостоятельно назначить квоты, прежде чем предлагать виртуальные машины пользователям. |
| Расширения виртуальных машин |Azure поддерживает широкий ассортимент расширений для виртуальных машин. Чтобы узнать о доступных расширениях, изучите статью [Обзор расширений и компонентов виртуальной машины под управлением Windows](../../virtual-machines/windows/extensions-features.md).| Azure Stack поддерживает подмножество расширений, доступных в Azure, и каждое расширение представлено в нескольких версиях. Администратор облака Azure Stack может выбрать, какие расширения будут доступны его пользователям. Чтобы просмотреть список поддерживаемых расширений, изучите раздел [Расширения виртуальных машин](#virtual-machine-extensions) в этой статье. |
| Сетевые ресурсы виртуальных машин | Общедоступные IP-адреса, назначенные для виртуальной машины клиента, доступны через Интернет.<br><br><br>Виртуальные машины Azure имеют фиксированное DNS-имя. | Общедоступные IP-адреса, назначенные для виртуальной машины клиента, доступны только в пределах среды Пакета средств разработки Azure Stack. Пользователь должен иметь доступ к Пакету средств разработки Azure Stack через [RDP](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop) или [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn), чтобы подключиться к виртуальной машине, созданной в Azure Stack.<br><br>Виртуальные машины, созданные в определенном экземпляре Azure Stack, получают DNS-имя в соответствии с параметрами, настроенными администратором облака. |
| Хранилище для виртуальных машин | Поддерживаются [управляемые диски.](../../virtual-machines/windows/managed-disks-overview.md) | Управляемые диски в Azure Stack пока не поддерживаются. |
| Версии API | Azure всегда использует последние версии API для всех компонентов виртуальной машины. | Azure Stack поддерживает только определенный набор служб Azure и конкретные версии API для каждой из этих служб. Чтобы просмотреть список поддерживаемых версий API, изучите раздел [Версии API](#api-versions) в этой статье. |
|Группы доступности виртуальных машин|Несколько доменов сбоя (2 или 3 в каждом регионе)<br>Несколько доменов обновления<br>Поддержка управляемых дисков|Несколько доменов сбоя (2 или 3 в каждом регионе)<br>Несколько доменов обновления (до 20)<br>Без поддержки управляемых дисков|
|наборы для масштабирования виртуальных машин|Автомасштабирование поддерживается|Автомасштабирование не поддерживается<br>В масштабируемый набор можно добавить дополнительные экземпляры с помощью портала, шаблонов Resource Manager или PowerShell.

## <a name="virtual-machine-sizes"></a>Размер виртуальных машин

В Azure Stack используются ограничения ресурсов. Это предотвращает их чрезмерное использование (на уровне локальных серверов и служб). Эти ограничения улучшают работу клиентов, уменьшая влияние других клиентов на потребление ресурсов.

- Для исходящего трафика виртуальной машины предусмотрены ограничения пропускной способности. Ограничения в Azure Stack аналогичны ограничениям в Azure.
- Для ресурсов хранилища в Azure Stack предусмотрены ограничения числа операций ввода-вывода в секунду. Это предотвращает основное чрезмерное использование ресурсов клиентами для доступа к хранилищу.
- Для виртуальных машин с несколькими дисками данных максимальная пропускная способность каждого диска данных составляет 500 операций ввода-вывода в секунду для HHD и 2300 операций ввода-вывода в секунду для SSD.

В следующей таблице перечислены поддерживаемые в Azure Stack виртуальные машины и их конфигурации:

| type           | Размер          | Диапазон поддерживаемых размеров |
| ---------------| ------------- | ------------------------ |
|Универсальные |Basic A        |[A0–A4](azure-stack-vm-sizes.md#basic-a)                   |
|Универсальные |Standard A     |[A0–A7](azure-stack-vm-sizes.md#standard-a)              |
|Универсальные |Серия D       |[D1–D4](azure-stack-vm-sizes.md#d-series)              |
|Универсальные |Серия Dv2     |[D1_v2–D5_v2](azure-stack-vm-sizes.md#ds-series)        |
|Универсальные |Серия DS      |[DS1–DS4](azure-stack-vm-sizes.md#dv2-series)            |
|Универсальные |Серия DSv2    |[DS1_v2–DS5_v2](azure-stack-vm-sizes.md#dsv2-series)      |
|Оптимизированные для памяти|Серия D       |[D11–D14](azure-stack-vm-sizes.md#mo-d)            |
|Оптимизированные для памяти|Серия DS      |[DS11–DS14](azure-stack-vm-sizes.md#mo-ds)|
|Оптимизированные для памяти|Серия Dv2     |[D11_v2–DS14_v2](azure-stack-vm-sizes.md#mo-dv2)     |
|Оптимизированные для памяти|Серия DSv2  |[DS11_v2–DS14_v2](azure-stack-vm-sizes.md#mo-dsv2)    |

Размеры виртуальных машин и объемы связанных с ними ресурсов в Azure Stack и Azure полностью совпадают. Это относится к объему памяти, количеству ядер, а также количеству и размерам создаваемых дисков данных. Однако фактическая производительность виртуальных машин зависит от базовых характеристик конкретной среды Azure Stack.

## <a name="virtual-machine-extensions"></a>Расширения виртуальных машин

 Azure Stack включает в себя небольшой набор расширений. Обновления и дополнительные расширения доступны посредством синдикации marketplace.

Используйте следующий скрипт PowerShell, чтобы получить список расширений виртуальной машины, доступных в вашей среде Azure Stack:

```powershell
Get-AzureRmVmImagePublisher -Location local | `
  Get-AzureRmVMExtensionImageType | `
  Get-AzureRmVMExtensionImage | `
  Select Type, Version | `
  Format-Table -Property * -AutoSize
```

## <a name="api-versions"></a>Версии API

Компоненты виртуальных машин в Azure Stack поддерживают следующие версии API:

![Типы ресурсов виртуальных машин](media/azure-stack-vm-considerations/vm-resoource-types.png)

Используйте следующий скрипт PowerShell, чтобы получить список версий API для компонентов виртуальной машины, доступных в вашей среде Azure Stack:

```powershell
Get-AzureRmResourceProvider | `
  Select ProviderNamespace -Expand ResourceTypes | `
  Select * -Expand ApiVersions | `
  Select ProviderNamespace, ResourceTypeName, @{Name="ApiVersion"; Expression={$_}} | `
  where-Object {$_.ProviderNamespace -like “Microsoft.compute”}
```

Список поддерживаемых типов ресурсов и версий API может измениться, если оператор облака обновит среду Azure Stack до более новой версии.

## <a name="windows-activation"></a>Активация Windows

Продукты Windows необходимо использовать в соответствии с правами на использование продуктов и условиями лицензии Майкрософт. Azure Stack использует [автоматическую активацию виртуальных машин](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v%3dws.11)) (AVMA) для активации виртуальных машин Windows Server.

- Узел Azure Stack активирует Windows с помощью ключей AVMA для Windows Server 2016. Все виртуальные машины под управлением Windows Server 2012 или более поздней версии активируются автоматически.
- Виртуальные машины под управлением Windows Server 2008 R2 не активируются автоматически. Их нужно активировать с помощью [MAK](https://technet.microsoft.com/library/ff793438.aspx).

Microsoft Azure использует сервер управления ключами для активации виртуальных машин Windows. Если вы переместили виртуальную машину из Azure Stack в Azure и столкнулись с проблемами при активации, ознакомьтесь с разделом [Устранение неполадок при активации виртуальных машин Windows в Azure](https://docs.microsoft.com/azure/virtual-machines/windows/troubleshoot-activation-problems). Дополнительные сведения можно найти в записи блога группы поддержки Azure [Troubleshooting Windows activation failures on Azure VMs](https://blogs.msdn.microsoft.com/mast/2017/06/14/troubleshooting-windows-activation-failures-on-azure-vms/) (Устранение неполадок при активации Windows на виртуальных машинах Azure).

## <a name="next-steps"></a>Дополнительная информация

[Создание виртуальной машины Windows с помощью PowerShell в Azure Stack](azure-stack-quick-create-vm-windows-powershell.md)