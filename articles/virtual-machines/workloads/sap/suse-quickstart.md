---
title: Тестирование SAP NetWeaver на виртуальных машинах SUSE Linux в Microsoft Azure | Документация Майкрософт
description: Тестирование SAP NetWeaver на виртуальных машинах SUSE Linux Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 645e358b-3ca1-4d3d-bf70-b0f287498d7a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/14/2017
ms.author: hermannd
ms.openlocfilehash: 9bb13cde07141c576a0ec78c3b48c5a20d6dbb67
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34657661"
---
# <a name="running-sap-netweaver-on-microsoft-azure-suse-linux-vms"></a>Запуск SAP NetWeaver на виртуальных машинах SUSE Linux в Microsoft Azure
В этой статье описываются различные моменты, которые следует учитывать при запуске SAP NetWeaver на виртуальных машинах SUSE Linux в Microsoft Azure. По состоянию на 19 мая 2016 года SAP NetWeaver официально поддерживается на виртуальных машинах SUSE Linux в Azure. Все сведения о версиях Linux, версиях ядра SAP и других необходимых компонентах можно найти в примечании SAP 1928533 "Приложения SAP в Azure: поддерживаемые продукты и типы виртуальных машин Azure".
Дополнительную документацию по SAP на виртуальных машинах Linux можно найти здесь: [Размещение и выполнение сценариев рабочей нагрузки SAP с помощью Azure](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Следующие сведения помогут избежать некоторых подводных камней.

## <a name="suse-images-on-azure-for-running-sap"></a>Образы SUSE в Azure для запуска SAP
Для запуска SAP NetWeaver в Azure используйте SUSE Linux Enterprise Server SLES 12 (SPx) или SLES для SAP. Также ознакомьтесь с примечанием SAP 1928533. В Azure Marketplace можно найти специальный образ SUSE ("SLES 11 SP3 для SAP CAL"), но он не предназначен для общего использования. Не используйте этот образ, так как он зарезервирован для решения [SAP Cloud Appliance Library](https://cal.sap.com/).  

Используйте платформу развертывания Azure Resource Manager для всех установок в Azure. Для поиска образов и версий SUSE SLES с помощью Azure PowerShell или интерфейса командной строки используйте следующие команды. Впоследствии выходные данные можно будет использовать, например, для определения образа ОС в шаблоне JSON для развертывания новой виртуальной машины SUSE Linux.
Для Azure Powershell версии 1.0.1 и более поздних действительны следующие команды PowerShell.

Хотя для установки SAP можно использовать стандартные образы SLES, рекомендуется использовать новые образы SLES для SAP. Эти образы теперь доступны в коллекции образов Azure. Дополнительные сведения об этих образах можно найти на соответствующей [странице Azure Marketplace]( https://azuremarketplace.microsoft.com/en-us/marketplace/apps/SUSE.SLES-SAP ) или [веб-странице часто задаваемых вопросов о SUSE, посвященной SLES для SAP]( https://www.suse.com/products/sles-for-sap/frequently-asked-questions/ ).


* Поиск существующих издателей, включая SUSE:
  
   ```
   PS  : Get-AzureRmVMImagePublisher -Location "West Europe"  | where-object { $_.publishername -like "*US*"  }
   CLI : azure vm image list-publishers westeurope | grep "US"
   ```
* Поиск существующих предложений от SUSE:
  
   ```
   PS  : Get-AzureRmVMImageOffer -Location "West Europe" -Publisher "SUSE"
   CLI : azure vm image list-offers westeurope SUSE
   ```
* Поиск предложений SLES от SUSE:
  
   ```
   PS  : Get-AzureRmVMImageSku -Location "West Europe" -Publisher "SUSE" -Offer "SLES"
   PS  : Get-AzureRmVMImageSku -Location "West Europe" -Publisher "SUSE" -Offer "SLES-SAP"
   CLI : azure vm image list-skus westeurope SUSE SLES
   CLI : azure vm image list-skus westeurope SUSE SLES-SAP
   ```
* Поиск определенной версии SKU для SLES:
  
   ```
   PS  : Get-AzureRmVMImage -Location "West Europe" -Publisher "SUSE" -Offer "SLES" -skus "12-SP2"
   PS  : Get-AzureRmVMImage -Location "West Europe" -Publisher "SUSE" -Offer "SLES-SAP" -skus "12-SP2"
   CLI : azure vm image list westeurope SUSE SLES 12-SP2
   CLI : azure vm image list westeurope SUSE SLES-SAP 12-SP2
   ```

## <a name="installing-walinuxagent-in-a-suse-vm"></a>Установка WALinuxAgent в виртуальной машине SUSE
Агент WALinuxAgent является частью образов SLES в Azure Marketplace. Сведения о его установке вручную (например, при отправке виртуального жесткого диска с ОС SLES из локальной среды) см. в статьях:

* [OpenSUSE](http://software.opensuse.org/package/WALinuxAgent)
* [Таблицы Azure](../../linux/endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [SUSE](https://www.suse.com/communities/blog/suse-linux-enterprise-server-configuration-for-windows-azure/)

## <a name="sap-enhanced-monitoring"></a>"Расширенный мониторинг" SAP
"Расширенный мониторинг" SAP является обязательным компонентом для запуска SAP в Azure. Ознакомьтесь с информацией в примечании SAP 2191498 "SAP on Linux with Azure: Enhanced Monitoring" (SAP на платформе Linux в Azure: расширенный мониторинг).

## <a name="attaching-azure-data-disks-to-an-azure-linux-vm"></a>Подключение дисков данных Azure к виртуальной машине Linux Azure
Никогда не подключайте диски данных Azure к виртуальной машине Linux Azure через идентификатор устройства. Вместо этого используйте универсальный уникальный идентификатор (UUID). Будьте внимательны, например, при использовании графических средств для подключения дисков данных Azure. Дважды проверьте записи в /etc/fstab.

Проблема с идентификатором устройства заключается в том, что он может измениться, после чего виртуальная машина Azure может зависнуть в процессе загрузки. Для устранения этой проблемы можно добавить в /etc/fstab параметр nofail. Однако при применении параметра nofail следует соблюдать осторожность, так как приложения могут использовать прежнюю точку подключения и записать ее в корень файловой системы, если во время загрузки не был подключен внешний диск данных Azure.

Единственное исключение относительно подключения через UUID касается присоединения диска операционной системы для устранения неполадок, как описано в следующем разделе.

## <a name="troubleshooting-a-suse-vm-that-isnt-accessible-anymore"></a>Устранение неполадок с виртуальной машиной SUSE, которая стала недоступной
Бывают ситуации, когда виртуальная машина SUSE в Azure зависает в процессе загрузки (например, вместе с ошибкой, связанной с подключением дисков). Эту проблему можно идентифицировать с помощью функции диагностики загрузки для виртуальных машин Azure вер. 2 на портале Azure. Дополнительные сведения см. в статье [Диагностика загрузки](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/).

Единственный способ решить эту проблему — подключить диск ОС из поврежденной виртуальной машины к другой виртуальной машине SUSE в Azure. Затем внесите соответствующие изменения, например отредактируйте /etc/fstab или удалите правила udev сети, как описано в следующем разделе.

Но здесь есть один важный момент. Развертывание нескольких виртуальных машин SUSE из одного образа Azure Marketplace (например SLES 11 с пакетом обновления 4 (SP4)) приводит к тому, что диск ОС всегда подключается с использованием одного идентификатора UUID. Следовательно, при подключении диска ОС из другой виртуальной машины (с помощью UUID), которая была развернута из тех же образов Azure Marketplace, у вас будет два одинаковых UUID. Это может привести к тому, что виртуальная машина, используемая для устранения неполадок, загрузится с подключенного и поврежденного диска ОС вместо исходного диска ОС.

Проблем можно избежать двумя способами:

* Используйте другой образ из Azure Marketplace для устранения неполадок с виртуальной машиной (например, SLES 11 SPx вместо SLES 12).
* Не присоединяйте поврежденный диск ОС из другой виртуальной машины через UUID, используйте вместо этого другие возможности.

## <a name="uploading-a-suse-vm-from-on-premises-to-azure"></a>Отправка виртуальной машины SUSE из локальной среды в Azure
Описание действий по передаче виртуальной машины SUSE из локальной среды в Azure см. в статье [Подготовка виртуальной машины SLES или openSUSE для Azure](../../linux/suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Если требуется отправить виртуальную машину без отзыва в конце (например, чтобы сохранить существующую установку SAP, а также имя узла), следует проверить следующие элементы:

* Убедитесь, что диск ОС подключен через UUID, а не идентификатор устройства. Для диска ОС недостаточно изменить UUID только в /etc/fstab. Также не забудьте адаптировать загрузчик, например с помощью YaST или посредством изменения /boot/grub/menu.lst.
* Если вы использовали формат VHDX для диска операционной системы SUSE и преобразовали его в виртуальный жесткий диск для передачи в Azure, вероятно, что сетевое устройство изменится с eth0 на eth1. Чтобы избежать дальнейших проблем при загрузке в Azure, следует вернуть значение eth0, как описано в статье [Fixing eth0 in cloned SLES 11 VMware](https://dartron.wordpress.com/2013/09/27/fixing-eth1-in-cloned-sles-11-vmware/) (Восстановление значения eth0 при клонировании SLES 11 в VMware).

Помимо выполнения описанных в этой статье действий рекомендуется также удалить этот файл:

   /lib/udev/rules.d/75-persistent-net-generator.rules.

Избежать возможных проблем также поможет установка агента Linux для Azure (waagent), если только не используется несколько сетевых карт.

## <a name="deploying-a-suse-vm-on-azure"></a>Развертывание виртуальной машины SUSE в Azure
Новые виртуальные машины SUSE следует создавать с помощью JSON-файлов шаблонов в новой модели Azure Resource Manager. После создания JSON-файла шаблона виртуальную машину можно развернуть с помощью следующей команды в качестве альтернативы для PowerShell:

   ```
   azure group deployment create "<deployment name>" -g "<resource group name>" --template-file "<../../filename.json>"

   ```
Дополнительные сведения о JSON-файлах шаблонов см. в статьях [Описание структуры и синтаксиса шаблонов Azure Resource Manager](../../../resource-group-authoring-templates.md) и [Шаблоны быстрого запуска Azure](https://azure.microsoft.com/documentation/templates/).

Дополнительные сведения об интерфейсе командной строки и Azure Resource Manager см. в статье [Управление ресурсами и группами ресурсов Azure с помощью интерфейса командной строки Azure](../../../xplat-cli-azure-resource-manager.md).

## <a name="sap-license-and-hardware-key"></a>Ключ оборудования и лицензия SAP
Для официальной сертификации SAP-Azure был представлен новый механизм для вычисления ключа оборудования SAP, который используется для лицензии SAP. Ядро SAP было адаптировано для использования нового алгоритма. В предыдущих версиях ядра SAP для Linux такое изменение кода отсутствует. Поэтому в определенных ситуациях (например, при изменении размера виртуальной машины Azure) ключ оборудования SAP изменяется, что делает лицензию SAP недействительной. Решение предоставлено с более поздними версиями ядра SAP Linux.  Подробные исправления ядра SAP задокументированы в примечании SAP 1928533.

## <a name="suse-sapconf-package--tuned-adm"></a>Пакет SUSE sapconf / tuned-adm
В SUSE есть пакет под названием sapconf, который содержит набор параметров SAP. Дополнительные сведения о функциях данного пакета, его установке и применении см. в статьях [Using sapconf to prepare a SUSE Linux Enterprise Server to run SAP systems](https://www.suse.com/communities/blog/using-sapconf-to-prepare-suse-linux-enterprise-server-to-run-sap-systems/) (Использование sapconf для подготовки SUSE Linux Enterprise Server для запуска систем SAP) и [What is sapconf or how to prepare a SUSE Linux Enterprise Server for running SAP systems?](http://scn.sap.com/community/linux/blog/2014/03/31/what-is-sapconf-or-how-to-prepare-a-suse-linux-enterprise-server-for-running-sap-systems) (Что такое sapconf или как подготовить SUSE Linux Enterprise Server для запуска систем SAP?).

Вместо пакета sapconf также доступен новый инструмент — tuned-adm. С дополнительными сведениями можно ознакомиться по двум ссылкам, указанным ниже.

- Документацию SLES по профилю sap-hana в tuned-adm см. [здесь](https://www.suse.com/documentation/sles-for-sap-12/book_s4s/data/sec_s4s_configure_sapconf.html) 

- Сведения о настройке систем для рабочих нагрузок SAP с помощью tuned-adm см. в разделе 6.2 [этой статьи](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/book_s4s/book_s4s.pdf).

## <a name="nfs-share-in-distributed-sap-installations"></a>Общая папка NFS в распределенных установках SAP
В случае распределенной установки, когда требуется, например, установить сервер базы данных и сервер приложений SAP в отдельных виртуальных машинах, вы можете обеспечить общий доступ к каталогу /sapmnt через сетевую файловую систему (NFS). Если после создания общей папки NFS в процессе установки возникают неполадки, убедитесь, что для этой общей папки установлен параметр "no_root_squash".

## <a name="logical-volumes"></a>Логические тома
В прошлом, когда кому-либо требовался большой логический том на нескольких дисках данных Azure (например, для базы данных SAP), рекомендовалось использовать средство управления массивом MDADM, так как диспетчер логических томов (LVM) еще не был полностью проверен в Azure. Чтобы узнать, как настроить RAID Linux в Azure с помощью mdadm, ознакомьтесь со статьей [Настройка программного RAID-массива в Linux](../../linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Вместе с тем, начиная с мая 2016 года LVM также полностью поддерживается в Azure и может использоваться в качестве альтернативы MDADM. Дополнительные сведения о LVM в Azure см. в статье:  
[Настройка диспетчера логических томов на виртуальной машине Linux в Azure](../../linux/configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="azure-suse-repository"></a>Репозиторий SUSE Azure
Если у вас возникла проблема с доступом к стандартному репозиторию SUSE Azure, можно воспользоваться командой для ее устранения. Такие проблемы могут произойти, если создать частный образ ОС в одном регионе Azure, а затем скопировать его в другой регион, где требуется развернуть новые виртуальные машины на основе этого частного образа ОС. Выполните следующую команду в виртуальной машине:

   ```
   service guestregister restart
   ```

## <a name="gnome-desktop"></a>Gnome Desktop
Если вы хотите использовать Gnome Desktop для установки полноценной демонстрационной системы SAP внутри одной виртуальной машины, включая графический интерфейс SAP, браузер и консоль управления SAP, здесь приведена подсказка по его установке в образах SLES Azure:

   Для SLES 11:

   ```
   zypper in -t pattern gnome
   ```

   Для SLES 12:

   ```
   zypper in -t pattern gnome-basic
   ```

## <a name="sap-support-for-oracle-on-linux-in-the-cloud"></a>Поддержка SAP для Oracle на платформе Linux в облаке
Существует ограничение поддержки, накладываемое Oracle на Linux в виртуализованных средах. Несмотря на то что эти ограничения поддержки не относятся к Azure, их следует изучить. SAP не поддерживает Oracle на SUSE или RedHat в общедоступном облаке, таком как Azure. В то же время выполнение базы данных Oracle в Azure полностью поддерживается системой SAP на базе Oracle Linux (см. примечание к разделу SAP 1928533). Если требуются другие комбинации, обратитесь непосредственно в Oracle.

