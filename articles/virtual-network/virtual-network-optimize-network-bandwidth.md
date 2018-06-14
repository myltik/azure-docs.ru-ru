---
title: Оптимизации пропускной способности сети для виртуальной машины | Документация Майкрософт
description: Узнайте, как оптимизировать пропускную способность сети для виртуальной машины Azure.
services: virtual-network
documentationcenter: na
author: steveesp
manager: Gerald DeGrace
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/15/2017
ms.author: steveesp
ms.openlocfilehash: b604782f917584d1ecec432c20de75f427176ed1
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/09/2018
ms.locfileid: "29132897"
---
# <a name="optimize-network-throughput-for-azure-virtual-machines"></a>Оптимизации пропускной способности сети для виртуальной машины Azure

Виртуальные машины Azure имеют сетевые параметры по умолчанию, с помощью которых можно дополнительно оптимизировать пропускную способность сети. Из этой статьи вы узнаете, как оптимизировать пропускную способность сети для виртуальных машин Microsoft Azure, которые работают под управлением Windows и Linux, включая такие основные дистрибутивы, как Ubuntu, CentOS и Red Hat.

## <a name="windows-vm"></a>Виртуальная машина Windows

Если виртуальная машина Windows поддерживает [ускорение работы в сети](create-vm-accelerated-networking-powershell.md), включив эту функцию, можно оптимизировать конфигурацию для пропускной способности. Остальные виртуальные машины Windows, использующие масштабирование размера приема (RSS), могут иметь большую максимальную пропускную способность, чем виртуальная машина без RSS. По умолчанию функция RSS может быть отключена на виртуальной машине Windows. Чтобы определить, включена ли функция RSS, и при необходимости включить ее, выполните следующие действия:

1. Определите, включена ли функция RSS для сетевого адаптера при помощи команды PowerShell `Get-NetAdapterRss`. В следующем примере показаны результаты выполнения команды `Get-NetAdapterRss`, когда функция RSS не включена.

    ```powershell
    Name                    : Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled                 : False
    ```
2. Чтобы включить RSS, введите следующую команду:

    ```powershell
    Get-NetAdapter | % {Enable-NetAdapterRss -Name $_.Name}
    ```
    Предыдущая команда не имеет выходные данные. Команда изменяет параметры сетевого адаптера, в результате чего около одной минуты отсутствует подключение. В это время появится диалоговое окно повторного подключения. Обычно после третьей попытки подключение восстанавливается.
3. Убедитесь, что функция RSS включена на виртуальной машине. Для этого еще раз выполните команду `Get-NetAdapterRss`. При успешном выполнении возвращается следующий результат:

    ```powershell
    Name                    : Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled                  : True
    ```

## <a name="linux-vm"></a>Виртуальная машина Linux

Функция RSS по умолчанию всегда включена на виртуальной машине Azure по управлением Linux. Ядра Linux, выпущенные после октября 2017 года, включают новые параметры оптимизации сети, которые обеспечивают более высокую пропускную способность сети для виртуальной машины Linux.

### <a name="ubuntu-for-new-deployments"></a>Ubuntu для новых развертываний

Ядро Ubuntu Azure обеспечивает самую высокую производительность сети в Azure. С 21 сентября 2017 г. оно используется по умолчанию. Чтобы получить это ядро, сначала установите последнюю поддерживаемую версию 16.04-LTS, как описано ниже:

```json
"Publisher": "Canonical",
"Offer": "UbuntuServer",
"Sku": "16.04-LTS",
"Version": "latest"
```

Затем выполните следующие команды, чтобы получить последние обновления. Эти шаги также применимы к виртуальным машинам, которые сейчас выполняются на базе ядра Ubuntu Azure.

```bash
#run as root or preface with sudo
apt-get -y update
apt-get -y upgrade
apt-get -y dist-upgrade
```

Следующий набор необязательных команд может быть полезным для существующих развертываний Ubuntu на базе ядра Azure, но для которых не удалось выполнить дальнейшие обновления без ошибок.

```bash
#optional steps may be helpful in existing deployments with the Azure kernel
#run as root or preface with sudo
apt-get -f install
apt-get --fix-missing install
apt-get clean
apt-get -y update
apt-get -y upgrade
apt-get -y dist-upgrade
```

#### <a name="ubuntu-azure-kernel-upgrade-for-existing-vms"></a>Обновление ядра Ubuntu Azure для существующих виртуальных машин

Обновив ядро Azure Linux, можно существенно повысить пропускную способность. Чтобы определить необходимость обновления, проверьте вашу версию ядра.

```bash
#Azure kernel name ends with "-azure"
uname -r

#sample output on Azure kernel:
#4.13.0-1007-azure
```

Если на виртуальной машине нет ядра Azure, номер версии обычно начинается с цифр 4.4. Если на виртуальной машине нет ядра Azure, выполните следующие команды как корневые:

```bash
#run as root or preface with sudo
apt-get update
apt-get upgrade -y
apt-get dist-upgrade -y
apt-get install "linux-azure"
reboot
```

### <a name="centos"></a>CentOS

Чтобы получить последние оптимизации, лучше всего создать виртуальную машину, используя последнюю поддерживаемую версию, указав следующие параметры:

```json
"Publisher": "OpenLogic",
"Offer": "CentOS",
"Sku": "7.4",
"Version": "latest"
```

Вы можете воспользоваться преимуществами использования новых и существующих виртуальных машин, установив последнюю версию служб интеграции Linux (LIS). Оптимизация пропускной способности реализована в LIS начиная с 4.2.2-2. Более поздние версии содержат дополнительные усовершенствования. Введите следующие команды для установки новейшей версии LIS:

```bash
sudo yum update
sudo reboot
sudo yum install microsoft-hyper-v
```

### <a name="red-hat"></a>Red Hat

Чтобы получить оптимизации, лучше всего создать виртуальную машину, используя последнюю поддерживаемую версию, указав следующие параметры:

```json
"Publisher": "RedHat"
"Offer": "RHEL"
"Sku": "7-RAW"
"Version": "latest"
```

Вы можете воспользоваться преимуществами использования новых и существующих виртуальных машин, установив последнюю версию служб интеграции Linux (LIS). Возможность оптимизации пропускной способности предусмотрена в службах LIS начиная с версии 4.2. Выполните следующие команды, чтобы загрузить и установить LIS:

```bash
mkdir lis4.2.3-5
cd lis4.2.3-5
wget https://download.microsoft.com/download/6/8/F/68FE11B8-FAA4-4F8D-8C7D-74DA7F2CFC8C/lis-rpms-4.2.3-5.tar.gz
tar xvzf lis-rpms-4.2.3-5.tar.gz
cd LISISO
install.sh #or upgrade.sh if prior LIS was previously installed
```

Дополнительные сведения о версии 4.2 служб интеграции Linux (LIS) для Hyper-V см. на [странице скачивания](https://www.microsoft.com/download/details.aspx?id=55106).

## <a name="next-steps"></a>Дополнительная информация
* Просмотрите оптимизированные результаты для своего сценария, используя сведения в статье [Проверка пропускной способности (NTTTCP)](virtual-network-bandwidth-testing.md).
* Узнайте, как [выделяется пропускная способность для виртуальных машин] (virtual-machine-network-throughput.md).
* Дополнительные сведения см. в статье [Виртуальная сеть Azure: часто задаваемые вопросы](virtual-networks-faq.md).
