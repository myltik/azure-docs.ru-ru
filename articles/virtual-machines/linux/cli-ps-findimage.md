---
title: "Выбор образов виртуальных машин Linux с помощью Azure CLI | Документация Майкрософт"
description: "Узнайте, как использовать Azure CLI для определения издателя, предложения, номера SKU и версии для образов виртуальных машин из Marketplace."
services: virtual-machines-linux
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 7a858e38-4f17-4e8e-a28a-c7f801101721
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/24/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 79eb69b83e4ffc0a4ad7c2631ce4d1306a1e335c
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/09/2018
---
# <a name="how-to-find-linux-vm-images-in-the-azure-marketplace-with-the-azure-cli"></a>Поиск образов виртуальных машин Linux в Azure Marketplace с помощью Azure CLI
В этой статье описывается, как с помощью Azure CLI 2.0 находить образы виртуальных машин в Azure Marketplace. Воспользуйтесь этими сведениями, чтобы указать образ из Marketplace при создании виртуальной машины Linux.

Убедитесь, что у вас установлена последняя версия [Azure CLI 2.0](/cli/azure/install-az-cli2), и войдите в учетную запись Azure с помощью команды `az login`.

## <a name="terminology"></a>Терминология

Образы Marketplace определяются в интерфейсе командной строки и других инструментах Azure с учетом следующей иерархии:

* **Издатель.** Организация, создавшая образ. Пример: Canonical.
* **Предложение.** Группа связанных образов, созданных издателем. Пример: Ubuntu Server.
* **Номер SKU.** Экземпляр предложения, например основной выпуск дистрибутива. Пример: 16.04-LTS.
* **Версия.** Номер версии образа SKU. При указании образа его номер версии можно заменить ключевым словом latest. В этом случае будет выбрана последняя версия дистрибутива.

Образ Marketplace обычно определяется на основе образа *URN*. В URN эти значения объединены (в качестве разделителя используется двоеточие): *Издатель*:*Предложение*:*Номер SKU*:*Версия*. 


## <a name="list-popular-images"></a>Просмотр списка популярных образов

Выполните команду [az vm image list](/cli/azure/vm/image#az_vm_image_list) без параметра `--all`, чтобы просмотреть список популярных образов виртуальных машин в Azure Marketplace. Например, чтобы увидеть кэшированный список популярных образов виртуальных машин в формате таблицы, выполните следующую команду:

```azurecli
az vm image list --output table
```

Выходные данные содержат URN (значение в столбце *URN*). На основе этого значения указывается образ. При создании виртуальной машины с помощью одного из популярных образов Marketplace в качестве альтернативы можно указать псевдоним URN, например *UbuntuLTS*.

```
You are viewing an offline list of images, use --all to retrieve an up-to-date list
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
CentOS         OpenLogic               7.3                 OpenLogic:CentOS:7.3:latest                                     CentOS               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
Debian         credativ                8                   credativ:Debian:8:latest                                        Debian               latest
openSUSE-Leap  SUSE                    42.2                SUSE:openSUSE-Leap:42.2:latest                                  openSUSE-Leap        latest
RHEL           RedHat                  7.3                 RedHat:RHEL:7.3:latest                                          RHEL                 latest
SLES           SUSE                    12-SP2              SUSE:SLES:12-SP2:latest                                         SLES                 latest
UbuntuServer   Canonical               16.04-LTS           Canonical:UbuntuServer:16.04-LTS:latest                         UbuntuLTS            latest
...
```

## <a name="find-specific-images"></a>Поиск определенных образов

Чтобы найти конкретный образ виртуальной машины в Marketplace, выполните команду `az vm image list` с параметром `--all`. Этот процесс занимает некоторое время и может возвращать большие объемы выходных данных. Поэтому вы можете отфильтровать список по `--publisher` или другому параметру. 

Например, приведенная ниже команда отображает предложения для Debian (помните, что без параметра `--all` поиск выполняется только в локальном кэше общих образов).

```azurecli
az vm image list --offer Debian --all --output table 

```

Частичные выходные данные приведены ниже. 
```
Offer    Publisher    Sku                Urn                                              Version
-------  -----------  -----------------  -----------------------------------------------  --------------
Debian   credativ     7                  credativ:Debian:7:7.0.201602010                  7.0.201602010
Debian   credativ     7                  credativ:Debian:7:7.0.201603020                  7.0.201603020
Debian   credativ     7                  credativ:Debian:7:7.0.201604050                  7.0.201604050
Debian   credativ     7                  credativ:Debian:7:7.0.201604200                  7.0.201604200
Debian   credativ     7                  credativ:Debian:7:7.0.201606280                  7.0.201606280
Debian   credativ     7                  credativ:Debian:7:7.0.201609120                  7.0.201609120
Debian   credativ     7                  credativ:Debian:7:7.0.201611020                  7.0.201611020
Debian   credativ     8                  credativ:Debian:8:8.0.201602010                  8.0.201602010
Debian   credativ     8                  credativ:Debian:8:8.0.201603020                  8.0.201603020
Debian   credativ     8                  credativ:Debian:8:8.0.201604050                  8.0.201604050
Debian   credativ     8                  credativ:Debian:8:8.0.201604200                  8.0.201604200
Debian   credativ     8                  credativ:Debian:8:8.0.201606280                  8.0.201606280
Debian   credativ     8                  credativ:Debian:8:8.0.201609120                  8.0.201609120
Debian   credativ     8                  credativ:Debian:8:8.0.201611020                  8.0.201611020
Debian   credativ     8                  credativ:Debian:8:8.0.201701180                  8.0.201701180
Debian   credativ     8                  credativ:Debian:8:8.0.201703150                  8.0.201703150
Debian   credativ     8                  credativ:Debian:8:8.0.201704110                  8.0.201704110
Debian   credativ     8                  credativ:Debian:8:8.0.201704180                  8.0.201704180
Debian   credativ     8                  credativ:Debian:8:8.0.201706190                  8.0.201706190
Debian   credativ     8                  credativ:Debian:8:8.0.201706210                  8.0.201706210
Debian   credativ     8                  credativ:Debian:8:8.0.201708040                  8.0.201708040
...
```

Примените аналогичные фильтры, используя параметры `--location`, `--publisher` и `--sku`. Можно даже искать частичные совпадения по фильтру. Так, с помощью параметра `--offer Deb` можно найти все образы Debian.

Если с помощью параметра `--location` не указать определенное расположение, то по умолчанию возвращаются значения для региона `westus`. (Задайте другое расположение по умолчанию с помощью команды `az configure --defaults location=<location>`.)

Например, следующая команда возвращает список всех номеров SKU для Debian 8 в регионе `westeurope`:

```azurecli
az vm image list --location westeurope --offer Deb --publisher credativ --sku 8 --all --output table
```

Частичные выходные данные приведены ниже.

```
Offer    Publisher    Sku                Urn                                              Version
-------  -----------  -----------------  -----------------------------------------------  -------------
Debian   credativ     8                  credativ:Debian:8:8.0.201602010                  8.0.201602010
Debian   credativ     8                  credativ:Debian:8:8.0.201603020                  8.0.201603020
Debian   credativ     8                  credativ:Debian:8:8.0.201604050                  8.0.201604050
Debian   credativ     8                  credativ:Debian:8:8.0.201604200                  8.0.201604200
Debian   credativ     8                  credativ:Debian:8:8.0.201606280                  8.0.201606280
Debian   credativ     8                  credativ:Debian:8:8.0.201609120                  8.0.201609120
Debian   credativ     8                  credativ:Debian:8:8.0.201611020                  8.0.201611020
Debian   credativ     8                  credativ:Debian:8:8.0.201701180                  8.0.201701180
Debian   credativ     8                  credativ:Debian:8:8.0.201703150                  8.0.201703150
Debian   credativ     8                  credativ:Debian:8:8.0.201704110                  8.0.201704110
Debian   credativ     8                  credativ:Debian:8:8.0.201704180                  8.0.201704180
Debian   credativ     8                  credativ:Debian:8:8.0.201706190                  8.0.201706190
Debian   credativ     8                  credativ:Debian:8:8.0.201706210                  8.0.201706210
...
```

## <a name="navigate-the-images"></a>Переход к образам 
Еще один способ поиска образа в определенном расположении — это выполнить по-очереди команды [az vm image list-publishers](/cli/azure/vm/image#az_vm_image_list_publishers), [az vm image list-offers](/cli/azure/vm/image#az_vm_image_list_offers) и [az vm image list-skus](/cli/azure/vm/image#az_vm_image_list_skus). С помощью этих команд определяются следующие значения:

1. Получить список издателей образов.
2. Получить список предложений нужного издателя.
3. Получить список номеров SKU для требуемого предложения.


Например, следующая команда позволяет получить список издателей образов в расположении "Западная часть США":

```azurecli
az vm image list-publishers --location westus --output table
```

Частичные выходные данные приведены ниже.

```
Location    Name
----------  ----------------------------------------------------
westus      1e
westus      4psa
westus      7isolutions
westus      a10networks
westus      abiquo
westus      accellion
westus      Acronis
westus      Acronis.Backup
westus      actian_matrix
westus      actifio
westus      activeeon
westus      adatao
...
```
Используйте эти сведения, чтобы найти предложения от определенного издателя. Например, если компания Canonical — издатель образов из западной части США, то найти ее предложения можно, выполнив команду `azure vm image list-offers`. Укажите расположение и издателя, как показано в следующем примере:

```azurecli
az vm image list-offers --location westus --publisher Canonical --output table
```

Выходные данные:

```
Location    Name
----------  -------------------------
westus      Ubuntu15.04Snappy
westus      Ubuntu15.04SnappyDocker
westus      UbunturollingSnappy
westus      UbuntuServer
westus      Ubuntu_Core
westus      Ubuntu_Snappy_Core
westus      Ubuntu_Snappy_Core_Docker
```
Вы видите, что издатель Canonical из западной части США предлагает **UbuntuServer** для Azure. Однако нам также нужны номера SKU. Чтобы получить их значения, выполните команду `azure vm image list-skus` и укажите обнаруженные расположение, издателя и предложение:

```azurecli
az vm image list-skus --location westus --publisher Canonical --offer UbuntuServer --output table
```

Выходные данные:

```
Location    Name
----------  -----------------
westus      12.04.3-LTS
westus      12.04.4-LTS
westus      12.04.5-DAILY-LTS
westus      12.04.5-LTS
westus      12.10
westus      14.04.0-LTS
westus      14.04.1-LTS
westus      14.04.2-LTS
westus      14.04.3-LTS
westus      14.04.4-LTS
westus      14.04.5-DAILY-LTS
westus      14.04.5-LTS
westus      16.04-beta
westus      16.04-DAILY-LTS
westus      16.04-LTS
westus      16.04.0-LTS
westus      16.10
westus      16.10-DAILY
westus      17.04
westus      17.04-DAILY
westus      17.10-DAILY
```

Наконец, выполните команду `az vm image list`, чтобы найти определенную версию номера SKU, например **16.04-LTS**:

```azurecli
az vm image list --location westus --publisher Canonical --offer UbuntuServer --sku 16.04-LTS --all --output table
```

Выходные данные:

```
Offer         Publisher    Sku        Urn                                               Version
------------  -----------  ---------  ------------------------------------------------  ---------------
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201611220  16.04.201611220
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201611300  16.04.201611300
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201612050  16.04.201612050
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201612140  16.04.201612140
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201612210  16.04.201612210
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201701130  16.04.201701130
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201702020  16.04.201702020
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201702200  16.04.201702200
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201702210  16.04.201702210
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201702240  16.04.201702240
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201703020  16.04.201703020
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201703030  16.04.201703030
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201703070  16.04.201703070
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201703270  16.04.201703270
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201703280  16.04.201703280
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201703300  16.04.201703300
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201705080  16.04.201705080
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201705160  16.04.201705160
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201706100  16.04.201706100
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201706191  16.04.201706191
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201707210  16.04.201707210
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201707270  16.04.201707270
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201708030  16.04.201708030
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201708110  16.04.201708110
UbuntuServer  Canonical    16.04-LTS  Canonical:UbuntuServer:16.04-LTS:16.04.201708151  16.04.201708151
```
## <a name="next-steps"></a>Дополнительная информация
Теперь по значению URN мы можем выбрать именно тот образ, который нам нужен. Передайте это значение с параметром `--image` при создании виртуальной машины с помощью команды [az vm create](/cli/azure/vm#az_vm_create). Помните, что при необходимости можно заменить номер версии в URN словом latest. В этом случае всегда будет выбираться последняя версия дистрибутива. Инструкции по быстрому созданию виртуальной машины на основе данных URN см. в статье [Создание виртуальных машин Linux и управление ими с помощью Azure CLI](tutorial-manage-vm.md).
