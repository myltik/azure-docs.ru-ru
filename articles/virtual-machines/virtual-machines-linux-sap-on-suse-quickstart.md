<properties
   pageTitle="Тестирование SAP NetWeaver на виртуальных машинах SUSE Linux Microsoft Azure | Microsoft Azure"
   description="Тестирование SAP NetWeaver на виртуальных машинах SUSE Linux Microsoft Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="hermanndms"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"
   keywords=""/>
<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="09/15/2016"
   ms.author="hermannd"/>

# Запуск SAP NetWeaver на виртуальных машинах SUSE Linux в Microsoft Azure


В этой статье описываются различные моменты, которые следует учитывать при запуске SAP NetWeaver на виртуальных машинах SUSE Linux в Microsoft Azure. По состоянию на 19 мая 2016 года SAP NetWeaver официально поддерживается на виртуальных машинах SUSE Linux в Azure. Все сведения о версиях Linux, версиях ядра SAP и т. д. можно найти в примечании SAP 1928533 "SAP Applications on Azure: Supported Products and Azure VM types" (Приложения SAP в Azure: поддерживаемые продукты и типы виртуальных машин Azure). Дополнительную документацию по SAP на виртуальным машинам Linux можно найти здесь: [Использование SAP на виртуальных машинах Linux](virtual-machines-linux-sap-get-started.md).


Следующие сведения помогут избежать некоторых подводных камней.

## Образы SUSE в Azure для запуска SAP

Для запуска SAP NetWeaver в Azure используйте только SUSE Linux Enterprise Server SLES 12 (SPx). Также ознакомьтесь с примечанием SAP 1928533. В Azure Marketplace можно найти специальный образ SUSE ("SLES 11 SP3 для SAP CAL"), но он не предназначен для общего использования. Не используйте этот образ, так как он зарезервирован для решения [SAP Cloud Appliance Library](https://cal.sap.com/).

Все новые тесты и установки в Azure должны выполняться с помощью Azure Resource Manager. Для поиска образов и версий SUSE SLES с помощью Azure Powershell или интерфейса командной строки используйте следующие команды. Впоследствии выходные данные можно будет использовать, например, для определения образа ОС в шаблоне JSON для развертывания новой виртуальной машины SUSE Linux. Для Azure Powershell версии 1.0.1 и более поздних действительны следующие команды PowerShell.

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
   CLI : azure vm image list-skus westeurope SUSE SLES
   ```

* Поиск определенной версии SKU для SLES:

   ```
   PS  : Get-AzureRmVMImage -Location "West Europe" -Publisher "SUSE" -Offer "SLES" -skus "12"
   CLI : azure vm image list westeurope SUSE SLES 12
   ```

## Установка WALinuxAgent в виртуальной машине SUSE

Агент WALinuxAgent является частью образов SLES в Azure Marketplace. Сведения о его установке вручную (например, при отправке виртуального жесткого диска с ОС SLES из локальной среды) см. в статьях:

- [OpenSUSE](http://software.opensuse.org/package/WALinuxAgent)

- [Таблицы Azure](virtual-machines-linux-endorsed-distros.md)

- [SUSE](https://www.suse.com/communities/blog/suse-linux-enterprise-server-configuration-for-windows-azure/)

## "Расширенный мониторинг" SAP

"Расширенный мониторинг" SAP является обязательным компонентом для запуска SAP в Azure. Ознакомьтесь с информацией в примечании SAP 2191498 "SAP on Linux with Azure: Enhanced Monitoring" (SAP на платформе Linux в Azure: расширенный мониторинг).

## Подключение дисков данных Azure к виртуальной машине Linux Azure

Никогда не подключайте диски данных Azure к виртуальной машине Linux Azure через идентификатор устройства. Вместо этого используйте универсальный уникальный идентификатор (UUID). Будьте внимательны, например, при использовании графических средств для подключения дисков данных Azure. Дважды проверьте записи в /etc/fstab.

Проблема с идентификатором устройства заключается в том, что он может измениться, после чего виртуальная машина Azure может зависнуть в процессе загрузки. Для устранения этой проблемы можно добавить в /etc/fstab параметр nofail. Однако при применении параметра nofail следует соблюдать осторожность, так как приложения могут использовать прежнюю точку подключения и записать ее в корень файловой системы, если во время загрузки не был подключен внешний диск данных Azure.

Единственное исключение относительно подключения через UUID касается присоединения диска операционной системы для устранения неполадок, как описано в следующем разделе.

## Устранение неполадок с виртуальной машиной SUSE, которая стала недоступной

Бывают ситуации, когда виртуальная машина SUSE в Azure зависает в процессе загрузки (например, вместе с ошибкой, связанной с подключением дисков). Эту проблему можно идентифицировать с помощью функции диагностики загрузки для виртуальных машин Azure вер. 2 на портале Azure. Дополнительные сведения см. в разделе [Диагностика загрузки](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/).

Единственный способ решить эту проблему — подключить диск ОС из поврежденной виртуальной машины к другой виртуальной машине SUSE в Azure. Затем внесите соответствующие изменения, например отредактируйте /etc/fstab или удалите правила udev сети, как описано в следующем разделе.

Но здесь есть один важный момент. Развертывание нескольких виртуальных машин SUSE из одного образа Azure Marketplace (например SLES 11 с пакетом обновления 4 (SP4)) приводит к тому, что диск ОС всегда подключается с использованием одного идентификатора UUID. Следовательно, при подключении диска ОС из другой виртуальной машины (с помощью UUID), которая была развернута из того же образа Azure Marketplace, у вас будет два одинаковых UUID. Это вызывает проблемы, а также приводит к тому, что виртуальная машина при устранении неполадок будет загружаться не с исходного диска ОС, а с присоединенного и поврежденного.

Этого можно избежать двумя способами:

* Используйте другой образ из Azure Marketplace для устранения неполадок с виртуальной машиной (например, SLES 11 SPx вместо SLES 12).
* Не присоединяйте поврежденный диск ОС из другой виртуальной машины через UUID, используйте вместо этого другие возможности.

## Отправка виртуальной машины SUSE из локальной среды в Azure

Описание действий по передаче виртуальной машины SUSE из локальной среды в Azure см. в статье [Подготовка виртуальной машины SLES или openSUSE для Azure](virtual-machines-linux-create-upload-vhd-suse.md).

Если требуется отправить виртуальную машину без отзыва в конце (например, чтобы сохранить существующую установку SAP, а также имя узла), следует проверить следующие элементы:

* Убедитесь, что диск ОС подключен через UUID, а не идентификатор устройства. Для диска ОС недостаточно изменить UUID только в /etc/fstab. Также не забудьте адаптировать загрузчик, например с помощью YaST или посредством изменения /boot/grub/menu.lst.
* Если вы использовали формат VHDX для диска операционной системы SUSE и преобразовали его в виртуальный жесткий диск для передачи в Azure, весьма вероятно, что сетевое устройство изменится с eth0 на eth1. Чтобы избежать дальнейших проблем при загрузке в Azure, следует вернуть значение eth0, как описано в статье [Fixing eth0 in cloned SLES 11 VMware](https://dartron.wordpress.com/2013/09/27/fixing-eth1-in-cloned-sles-11-vmware/) (Восстановление значения eth0 при клонировании SLES 11 в VMware).

Помимо выполнения описанных в этой статье действий рекомендуется также удалить:

   /lib/udev/rules.d/75-persistent-net-generator.rules.

Избежать возможных проблем также поможет установка агента Linux для Azure (waagent), если только не используется несколько сетевых карт.

## Развертывание виртуальной машины SUSE в Azure

Новые виртуальные машины SUSE следует создавать с помощью JSON-файлов шаблонов в новой модели Azure Resource Manager. После создания JSON-файла шаблона виртуальную машину можно развернуть с помощью следующей команды в качестве альтернативы для PowerShell:

   ```
   azure group deployment create "<deployment name>" -g "<resource group name>" --template-file "<../../filename.json>"

   ```
Дополнительные сведения о JSON-файлах шаблонов см. в разделах [Создание шаблонов Azure Resource Manager](resource-group-authoring-templates.md) и [Шаблоны быстрого запуска Azure](https://azure.microsoft.com/documentation/templates/).

Дополнительные сведения об интерфейсе командной строки и Azure Resource Manager см. в статье [Использование Azure CLI для Mac, Linux и Windows с диспетчером ресурсов Azure](xplat-cli-azure-resource-manager.md).

## Ключ оборудования и лицензия SAP

Для официальной сертификации SAP-Azure был представлен новый механизм для вычисления ключа оборудования SAP, который используется для лицензии SAP. Ядро SAP было адаптировано для использования данного ключа. В предыдущих версиях ядра SAP для Linux такое изменение кода отсутствует. Поэтому в определенных ситуациях (например, при изменении размера виртуальной машины Azure) ключ оборудования SAP изменяется, что делает лицензию SAP недействительной. Это исправлено в последних версиях ядра SAP для Linux. Подробные сведения представлены в примечании SAP 1928533.

## Пакет SUSE sapconf / tuned-adm

В SUSE есть пакет под названием sapconf, который содержит набор параметров SAP. Дополнительные сведения о функциях данного пакета, его установке и применении см. в статьях [Using sapconf to prepare a SUSE Linux Enterprise Server to run SAP systems](https://www.suse.com/communities/blog/using-sapconf-to-prepare-suse-linux-enterprise-server-to-run-sap-systems/) (Использование sapconf для подготовки SUSE Linux Enterprise Server для запуска систем SAP) и [What is sapconf or how to prepare a SUSE Linux Enterprise Server for running SAP systems?](http://scn.sap.com/community/linux/blog/2014/03/31/what-is-sapconf-or-how-to-prepare-a-suse-linux-enterprise-server-for-running-sap-systems) (Что такое sapconf или как подготовить SUSE Linux Enterprise Server для запуска систем SAP?).

Вместо пакета sapconf также доступен новый инструмент — tuned-adm. Дополнительные сведения об этом инструменте см. по двум ссылкам, указанным ниже.

Документацию SLES по профилю sap-hana в tuned-adm см. [здесь](https://www.suse.com/documentation/sles-for-sap-12/book_s4s/data/sec_s4s_configure_sapconf.html).

Сведения о настройке систем для рабочих нагрузок SAP с помощью tuned-adm см. в главе 6.2 [этой статьи](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/book_s4s/book_s4s.pdf).


## Общая папка NFS в распределенных установках SAP

В случае распределенной установки, когда требуется, например, установить сервер базы данных и сервер приложений SAP в отдельных виртуальных машинах, вы можете обеспечить общий доступ к каталогу /sapmnt через сетевую файловую систему (NFS). Если после создания общей папки NFS в процессе установки возникают неполадки, убедитесь, что для этой общей папки установлен параметр "no\_root\_squash".

## Логические тома

В прошлом, когда кому-либо требовался большой логический том на нескольких дисках данных Azure (например, для базы данных SAP), рекомендовалось использовать mdadm, так как диспетчер логических томов (LVM) еще не был полностью проверен в Azure. Чтобы узнать, как настроить RAID Linux в Azure с помощью mdadm, ознакомьтесь со статьей [Настройка программного RAID-массива в Linux](virtual-machines-linux-configure-raid.md). Вместе с тем, начиная с начала мая 2016 года LVM также полностью поддерживается в Azure и может использоваться в качестве альтернативы mdadm. Дополнительные сведения об использовании LVM в Azure см. в статье [Настройка диспетчера логических томов на виртуальной машине Linux в Azure](virtual-machines-linux-configure-lvm.md).


## Репозиторий SUSE Azure

Если у вас возникла проблема с доступом к стандартному репозиторию SUSE Azure, можно воспользоваться простой командой для ее устранения. Это может произойти, если создать частный образ ОС в одном регионе Azure, а затем скопировать его в другой регион, где требуется развернуть новые виртуальные машины на основе этого частного образа ОС. Просто выполните следующую команду в виртуальной машине:

   ```
   service guestregister restart
   ```

## Gnome Desktop

Если вы хотите использовать Gnome Desktop для установки полноценной демонстрационной системы SAP внутри одной виртуальной машины, включая графический интерфейс SAP, браузер и консоль управления SAP, здесь приведена подсказка по его установке в образах SLES Azure:

   Для SLES 11:

   ```
   zypper in -t pattern gnome
   ```

   Для SLES 12:

   ```
   zypper in -t pattern gnome-basic
   ```

## Поддержка SAP для Oracle на платформе Linux в облаке

Существует ограничение поддержки, накладываемое Oracle на Linux в виртуализованных средах. Несмотря на то, что эта тема не относится к Azure, ее следует изучить. SAP не поддерживает Oracle на SUSE или RedHat в общедоступном облаке, таком как Azure. По этому вопросу обращайтесь напрямую в Oracle.

<!---HONumber=AcomDC_0921_2016-->