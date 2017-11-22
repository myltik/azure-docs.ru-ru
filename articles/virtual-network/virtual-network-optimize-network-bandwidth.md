---
title: "Оптимизации пропускной способности сети для виртуальной машины | Документация Майкрософт"
description: "Узнайте, как оптимизировать пропускную способность сети для виртуальной машины Azure."
services: virtual-network
documentationcenter: na
author: steveesp
manager: Gerald DeGrace
editor: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/24/2017
ms.author: steveesp
ms.openlocfilehash: d77440fe62bbd0e720e5ae60b15574dacc4180c0
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/15/2017
---
# <a name="optimize-network-throughput-for-azure-virtual-machines"></a>Оптимизации пропускной способности сети для виртуальной машины Azure

Виртуальные машины Azure имеют сетевые параметры по умолчанию, с помощью которых можно дополнительно оптимизировать пропускную способность сети. Из этой статьи вы узнаете, как оптимизировать пропускную способность сети для виртуальных машин Microsoft Azure, которые работают под управлением Windows и Linux, включая такие основные дистрибутивы, как Ubuntu, CentOS и Red Hat.

## <a name="windows-vm"></a>Виртуальная машина Windows

Если виртуальная машина Windows поддерживается с помощью [ускорения работы в сети](virtual-network-create-vm-accelerated-networking.md), включение этой функции обеспечит оптимальную конфигурацию для пропускной способности. Остальные виртуальные машины Windows, использующие масштабирование размера приема (RSS), могут иметь большую максимальную пропускную способность, чем виртуальная машина без RSS. По умолчанию функция RSS может быть отключена на виртуальной машине Windows. Чтобы определить, включена ли эта функция, и включить ее при необходимости, выполните следующие действия.

1. Выполните команду PowerShell `Get-NetAdapterRss`, чтобы определить, включена ли функция RSS для сетевого адаптера. В следующем примере показаны результаты выполнения команды `Get-NetAdapterRss`, когда функция RSS не включена.

    ```powershell
    Name                    : Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled              : False
    ```
2. Выполните следующую команду, чтобы включить RSS:

    ```powershell
    Get-NetAdapter | % {Enable-NetAdapterRss -Name $_.Name}
    ```
    Предыдущая команда не имеет выходные данные. Команда изменяет параметры сетевого адаптера, в результате чего около одной минуты отсутствует подключение. В это время появится диалоговое окно повторного подключения. Обычно после третьей попытки подключение восстанавливается.
3. Убедитесь, что функция RSS включена на виртуальной машине. Для этого еще раз выполните команду `Get-NetAdapterRss`. При успешном выполнении возвращается следующий результат:

    ```powershell
    Name                    :Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled              : True
    ```

## <a name="linux-vm"></a>Виртуальная машина Linux

Функция RSS по умолчанию всегда включена на виртуальной машине Azure по управлением Linux. Ядра Linux, выпущенные после октября 2017 года, включают новые параметры оптимизации сети, которые обеспечивают более высокую пропускную способность сети для виртуальной машины Linux.

### <a name="ubuntu-for-new-deployments"></a>Ubuntu для новых развертываний

Чтобы получить параметры оптимизации, сначала установите последнюю поддерживаемую версию 16.04-LTS, например:
```json
"Publisher": "Canonical",
"Offer": "UbuntuServer",
"Sku": "16.04-LTS",
"Version": "latest"
```
Завершив обновление, выполните следующие команды, чтобы получить последнюю версию ядра:

```bash
apt-get -f install
apt-get --fix-missing install
apt-get clean
apt-get -y update
apt-get -y upgrade
```

Необязательная команда:

`apt-get -y dist-upgrade`
#### <a name="ubuntu-azure-kernel-upgrade-for-existing-vms"></a>Обновление ядра Ubuntu Azure для существующих виртуальных машин

Обновив ядро Azure Linux, можно существенно повысить пропускную способность. Чтобы определить необходимость обновления, проверьте вашу версию ядра.

```bash
#Azure kernel name ends with "-azure"
uname -r

#sample output on Azure kernel:
#4.11.0-1014-azure
```

Затем выполните следующие команды (требуется доступ с правами root):
```bash
#run as root or preface with sudo
apt-get update
apt-get upgrade -y
apt-get dist-upgrade -y
apt-get install "linux-azure"
reboot
```

### <a name="centos"></a>CentOS

Чтобы получить новейшие параметры оптимизации, сначала обновите систему до последней поддерживаемой версии, например:
```json
"Publisher": "OpenLogic",
"Offer": "CentOS",
"Sku": "7.4",
"Version": "latest"
```
После завершения обновления установите последнюю версию служб интеграции Linux (LIS).
Оптимизация пропускной способности реализована в LIS начиная с 4.2.2-2. Более поздние версии содержат дополнительные усовершенствования. Введите следующие команды для установки новейшей версии LIS:

```bash
sudo yum update
sudo reboot
sudo yum install microsoft-hyper-v
```

### <a name="red-hat"></a>Red Hat

Чтобы получить параметры оптимизации, сначала обновите систему до последней поддерживаемой версии, например:
```json
"Publisher": "RedHat"
"Offer": "RHEL"
"Sku": "7-RAW"
"Version": "latest"
```
После завершения обновления установите последнюю версию служб интеграции Linux (LIS).
Возможность оптимизации пропускной способности предусмотрена в службах LIS начиная с версии 4.2. Выполните следующие команды, чтобы загрузить и установить LIS:

```bash
mkdir lis4.2.3-1
cd lis4.2.3-1
wget https://download.microsoft.com/download/6/8/F/68FE11B8-FAA4-4F8D-8C7D-74DA7F2CFC8C/lis-rpms-4.2.3-1.tar.gz
tar xvzf lis-rpms-4.2.3-1.tar.gz
cd LISISO
install.sh #or upgrade.sh if prior LIS was previously installed
```

Дополнительные сведения о версии 4.2 служб интеграции Linux (LIS) для Hyper-V см. на [странице скачивания](https://www.microsoft.com/download/details.aspx?id=55106).

## <a name="next-steps"></a>Дальнейшие действия
* Теперь, когда виртуальная машина оптимизирована, просмотрите результаты для своего сценария, используя сведения в статье [Проверка пропускной способности (NTTTCP)](virtual-network-bandwidth-testing.md).
* Дополнительные сведения см. в статье [Виртуальная сеть Azure: часто задаваемые вопросы](virtual-networks-faq.md).
