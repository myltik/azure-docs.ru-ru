---
title: "Руководство по группам доступности для виртуальных машин Linux в Azure | Документы Майкрософт"
description: "Вы можете узнать о группах доступности для виртуальных машин Linux в Azure."
documentationcenter: 
services: virtual-machines-linux
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/20/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: 65a7872e0880cc74a00ca1c05baae2b34b407a61
ms.lasthandoff: 04/26/2017


---

# <a name="how-to-use-availability-sets"></a>Использование групп доступности

В этом учебнике описано, как повысить доступность виртуальных машин, поместив их в логическую группу, называемую группой доступности. При создании виртуальных машин в группе доступности платформа Azure распределяет их по базовой инфраструктуре. Использование группы доступности гарантирует, что в случае планового обслуживания платформы либо аппаратного сбоя по меньшей мере одна виртуальная машина продолжит работать.

Для работы с этим руководством можно использовать последнюю версию [Azure CLI 2.0](/cli/azure/install-azure-cli).

## <a name="availability-set-overview"></a>Обзор групп доступности

Виртуальные машины можно создавать по логическим группам оборудования в базовом центре обработки данных Azure. При создании двух и более виртуальных машин вычислительные ресурсы и ресурсы хранения распределяются по оборудованию, например серверам, сетевым коммутаторам и хранилищу. Это позволяет поддерживать доступность приложения в случае обслуживания компонента оборудования. Группы доступности позволяют задать логические группы.

Группы доступности обеспечивают для виртуальных машин высокий уровень доступности. Следует также убедиться, что приложения способны выдерживать сбои или работы по обслуживанию.

## <a name="create-an-availability-set"></a>"Создать группу доступности"

Вы можете создать группу доступности с помощью команды [az vm availability-set create](/cli/azure/availability-set#create). В этом примере мы задали число доменов обновления и сбоя равным **2** для группы доступности **myAvailabilitySet** в группе ресурсов **myResourceGroupAvailability**.

```azurecli
az vm availability-set create \
    --resource-group myResourceGroupAvailability \
    --name myAvailabilitySet \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

## <a name="create-vms-inside-an-availability-set"></a>Создание виртуальных машин в группе доступности

чтобы виртуальные машины правильно распределялись по оборудованию, их нужно создать внутри группы доступности. Существующую виртуальную машину невозможно добавить в группу доступности после создания. 

Оборудование в расположении делится на несколько доменов обновления и доменов сбоя. **Домен обновления** — это группа виртуальных машин и базового физического оборудования, которую можно перезагрузить одновременно. Виртуальные машины в одном **домене сбоя** совместно используют общее хранилище, а также общий источник питания и сетевой коммутатор. 

При создании виртуальной машины с помощью команды [az vm create](/cli/azure/vm#create) вы указываете группу доступности, задав ее имя с помощью параметра `--availability-set`.

```azurecli
for i in `seq 1 2`; do
   az vm create \
     --resource-group myResourceGroupAvailability \
     --name myVM$i \
     --availability-set myAvailabilitySet \
     --size Standard_DS1_v2  \
     --image Canonical:UbuntuServer:14.04.4-LTS:latest \
     --admin-username azureuser \
     --generate-ssh-keys \
     --no-wait
done 
```

Теперь у нас есть две виртуальные машины, распределенные по базовому оборудованию. 

## <a name="check-for-available-vm-sizes"></a>Знакомство с доступными размерами виртуальной машины 

Позднее вы можете добавить в группу доступности другие виртуальные машины, но следует понимать, какие размеры виртуальных машин доступны на оборудовании. Используйте `az vm availability-set list-sizes` для получения списка всех доступных размеров в аппаратном кластере для данной группы доступности.

```azurecli
az vm availability-set list-sizes \
     --resource-group myResourceGroupAvailability \
     --name myAvailabilitySet \
     --output table  
```

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве вы изучили использование групп доступности. Перейдите к следующему руководству, чтобы узнать о масштабируемых наборах виртуальных машин.

[Создание масштабируемого набора виртуальных машин](tutorial-create-vmss.md)


