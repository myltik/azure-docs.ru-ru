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
ms.date: 02/01/2017
ms.author: steveesp
translationtype: Human Translation
ms.sourcegitcommit: 50be31e179bf52e009596fbc68339dfb5a1aa1e4
ms.openlocfilehash: d53b1cae9845be32bd053ef196203ea83df06b10


---

# <a name="optimize-network-throughput-for-azure-virtual-machines"></a>Оптимизации пропускной способности сети для виртуальной машины Azure

Виртуальные машины Azure имеют сетевые параметры по умолчанию, с помощью которых можно дополнительно оптимизировать пропускную способность сети. В этой статье описывается, как оптимизировать пропускную способность сети для виртуальных машин Microsoft Azure, которые работают под управлением Windows и Linux, включая такие основные дистрибутивы, как Ubuntu, CentOS и Red Hat.

## <a name="windows-vm"></a>Виртуальная машина Windows

Виртуальная машина, использующая масштабирование размера приема (RSS), может иметь большую максимальную пропускную способность, чем виртуальная машина без RSS. По умолчанию функция RSS может быть отключена на виртуальной машине Windows. Чтобы определить, включена ли эта функция, и включить ее при необходимости, выполните следующие действия.

1. Выполните команду PowerShell `Get-NetAdapterRss`, чтобы определить, включена ли функция RSS для сетевого адаптера. В следующем примере показаны результаты выполнения команды `Get-NetAdapterRss`, когда функция RSS не включена.

    ```powershell
    Name                    : Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled                 : False
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
    Enabled                 : True
    ```

## <a name="linux-vm"></a>Виртуальная машина Linux

Функция RSS по умолчанию всегда включена на виртуальной машине Azure по управлением Linux. Ядра Linux, выпущенные после января 2017 года, включают новые параметры оптимизации сети, которые обеспечивают более высокую пропускную способность сети для виртуальной машины Linux.

### <a name="ubuntu"></a>Ubuntu

Чтобы получить параметры оптимизации, сначала обновите систему до последней поддерживаемой версии состоянием на январь 2017 года:
```json
"Publisher": "Canonical",
"Offer": "UbuntuServer",
"Sku": "16.04.0-LTS",
"Version": "16.04.201609071"
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

### <a name="centos"></a>CentOS

Чтобы получить параметры оптимизации, сначала обновите систему до последней поддерживаемой версии состоянием на январь 2017 года:
```json
"Publisher": "OpenLogic",
"Offer": "CentOS",
"Sku": "7.3",
"Version": "latest"
```
После завершения обновления установите последнюю версию служб интеграции Linux (LIS).
Возможность оптимизации пропускной способности в службах LIS доступна начиная с версии 4.1.3. Введите следующие команды для установки LIS:

```bash
sudo yum update
sudo reboot
sudo yum install microsoft-hyper-v-4.1.3 kmod-microsoft-hyper-v-4.1.3
```

### <a name="red-hat"></a>Red Hat

Чтобы получить параметры оптимизации, сначала обновите систему до последней поддерживаемой версии состоянием на январь 2017 года:

"Publisher": "RedHat" "Offer": "RHEL" "Sku": "7.3" "Version": "7.3.20161104"

После завершения обновления установите последнюю версию служб интеграции Linux (LIS).
Возможность оптимизации пропускной способности в службах LIS доступна начиная с версии 4.1.3. Выполните следующие команды, чтобы загрузить и установить LIS:

```bash
mkdir lis4.1.3
cd lis4.1.3
wget https://download.microsoft.com/download/7/6/B/76BE7A6E-E39F-436C-9353-F4B44EF966E9/lis-rpms-4.1.3.tar.gz
tar xvzf lis-rpms-4.1.3.tar.gz
cd LISISO
install.sh  #or upgrade.sh if previous LIS was previously installed
```
 
Дополнительные сведения о версии 4.1 служб интеграции Linux (LIS) для Hyper-V см. на [странице скачивания](https://www.microsoft.com/download/details.aspx?id=51612).



<!--HONumber=Feb17_HO3-->


