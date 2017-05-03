---
title: "Создание пользовательских образов виртуальных машин с помощью Azure CLI | Документы Майкрософт"
description: "Руководство по созданию настраиваемого образа виртуальной машины с помощью Azure CLI."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/21/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: f7fd7d63e6bb1da7022cef782e3b84ea20a64c31
ms.lasthandoff: 04/26/2017

---

# <a name="create-a-custom-image-of-an-azure-vm-using-the-cli"></a>Создание пользовательского образа виртуальной машины Azure с помощью интерфейса командной строки

В этом руководстве вы узнаете, как создать пользовательский образ виртуальной машины Azure. Пользовательские образы похожи на образы магазина, однако их можно создавать самостоятельно. Пользовательские образы можно использовать для начальной загрузки конфигураций, например при предварительной загрузке приложений, конфигураций приложений и других конфигураций операционной системы. При создании пользовательского образа в него включается виртуальная машина со всеми подключенными дисками. 

Для работы с этим руководством можно использовать последнюю версию [Azure CLI 2.0](/cli/azure/install-azure-cli).

Для выполнения примера в этом руководстве требуется виртуальная машина. Этот [пример сценария](../scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md) позволяет создать ее при необходимости. При работе с примером по мере необходимости заменяйте имена групп ресурсов и виртуальных машин.

## <a name="create-an-image"></a>Создание образа

Чтобы создать образ виртуальной машины, нужно подготовить виртуальную машину, выполнив отзыв, отменив выделение и пометив исходную виртуальную машину как обобщенную.

### <a name="deprovision-the-vm"></a>Отзыв виртуальной машины 

Отзыв обобщает виртуальную машину, удаляя относящиеся к ней сведения. Такое обобщение позволяет развернуть множество виртуальных машин из одного образа. Во время отзыва имя узла сбрасывается в значение `localhost.localdomain`. Ключи узла SSH, конфигурации сервера доменных имен, пароль учетной записи root и кэшированные аренды DHCP также удаляются.

Чтобы отозвать виртуальную машину, используйте агент виртуальной машины Azure (waagent). Агент виртуальной машины Azure устанавливается на виртуальной машине и управляет подготовкой и взаимодействием с контроллером структуры Azure. Дополнительные сведения см. в [руководстве пользователя агента Linux Azure](agent-user-guide.md).

Подключитесь к виртуальной машине с помощью SSH и выполните команду для ее отзыва. При указании аргумента `+user` также удаляется последняя подготовленная учетная запись пользователя вместе со связанными данными. Замените IP-адрес в примере общедоступным IP-адресом виртуальной машины.

```bash
ssh azureuser@52.174.34.95 sudo waagent -deprovision+user -force
```
Закройте сеанс SSH.

```bash
exit
```

### <a name="deallocate-and-mark-the-vm-as-generalized"></a>Отмена выделения и пометка виртуальной машины как обобщенной

Чтобы создать образ, нужно отменить выделение виртуальной машины. Отмените выделение виртуальной машины с помощью команды [az vm deallocate](/cli//azure/vm#deallocate). 
   
```azurecli
az vm deallocate --resource-group myRGCaptureImage --name myVM
```

Наконец, задайте для виртуальной машины обобщенное состояние с помощью команды [az vm generalize](/cli//azure/vm#generalize).
   
```azurecli
az vm generalize --resource-group myResourceGroupImages --name myVM
```

### <a name="capture-the-image"></a>Запись образа

Теперь можно создать образ виртуальной машины с помощью команды [az image create](/cli//azure/image#create). В следующем примере создается образ `myImage` из виртуальной машины `myVM`.
   
```azurecli
az image create --resource-group myResourceGroupImages --name myImage --source myVM
```
 
## <a name="create-a-vm-from-an-image"></a>Создание виртуальной машины из образа

Вы можете создать виртуальную машину из образа с помощью команды [az vm create](/cli/azure/vm#create). В следующем примере создается виртуальная машина с именем `myVMfromImage` из образа с именем `myImage`.

```azurecli
az vm create --resource-group myResourceGroupImages --name myVMfromImage --image myImage --admin-username azureuser --generate-ssh-keys
```

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве вы научились создавать пользовательские образы виртуальных машин. Перейдите к следующему руководству, чтобы узнать о высокодоступных виртуальных машинах.

[Создание высокодоступных виртуальных машин](tutorial-availability-sets.md)


